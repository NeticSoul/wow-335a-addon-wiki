---
title: Secure Execution
type: concept
source_chapters: [15, 25]
created: 2026-04-15
updated: 2026-04-17
tags: [security, taint, protected, secure-templates, snippets, state-drivers, combat]
---

# Secure Execution

## Summary

WoW 3.3.5a's **taint / secure-code model** exists to prevent addons from automating gameplay. Any code that can cast a spell, use an item, target a unit, move the player, or change other gameplay-critical state must run in a **secure** execution path, and the client refuses to perform those actions if any **tainted** (addon-origin) code is on the call stack or has influenced the relevant data.

Addons cannot produce secure code directly. Instead, Blizzard ships a catalog of **secure templates** and **secure handlers** (in `FrameXML\SecureTemplates.xml` and `FrameXML\SecureHandlers.xml`) whose built-in scripts are secure. Addons configure these templates via **frame attributes** — a channel deliberately designed to carry data from insecure code to secure code without spreading taint.

This model has two halves:

1. **Secure templates (Ch 15)** — configure what a button does when clicked, out of combat, using attributes.
2. **Secure handlers / snippets (Ch 25)** — run controlled Lua code *during combat*, sandboxed into a restricted environment.

## Detailed Explanation

### Trusted vs Tainted

Every Lua value in the client is internally tagged with its origin:

- **Secure**: created by `FrameXML` (Blizzard) files.
- **Tainted**: created by any addon, macro `/run`, or `/script` chat command. The addon name (or `"A macro script"`) is recorded as the taint source.

Taint spreads in two ways:

- **Execution taint** — transient. Travels up the current call stack: if a secure function is called *from* tainted code (via pcall, script handler, hook, etc.), it runs as tainted for that invocation. Clears when the stack unwinds.
- **Value taint** — persistent. If tainted code stores a value into a global, a table field, or a frame key that secure code later reads, the secure code that reads it becomes tainted. Persists until the value is overwritten (or, for `_G` entries, until `/reload`).

Protected actions (e.g. `CastSpellByName`, `UseAction`, `TargetUnit`) check: *is the current call stack fully secure?* If not, they silently refuse and print `"Interface action failed because of an AddOn"` or `"An action was blocked because of taint from <AddonName>"`.

### Protected Frames

A frame is **protected** when its XML template carries `protected="true"`, or when it is parented or anchored to a protected frame (implicit protection). `Frame:IsProtected()` returns `(isProtected, isExplicit)`.

Protected frames can take restricted actions. In exchange, **during combat** the following methods are blocked when called by tainted code on protected frames:

```
Show, Hide, SetPoint, SetAllPoints, ClearAllPoints, SetParent,
SetFrameStrata, SetFrameLevel, SetToplevel, Raise, Lower,
SetAlpha (indirectly via snippet), SetScale, SetWidth, SetHeight,
SetHitRectInsets, Enable, Disable, EnableMouse, EnableKeyboard,
EnableMouseWheel, SetHorizontalScroll, SetVerticalScroll,
SetAttribute, AllowAttributeChanges
```

Outside combat, all of these work normally. The combat window is delimited by the `PLAYER_REGEN_DISABLED` (entering combat) and `PLAYER_REGEN_ENABLED` (leaving combat) [events](events-system.md). `InCombatLockdown()` returns the current status and is the authoritative check.

### Frame Attributes: The Configuration Channel

Attributes are the *only* data channel addons can use to program secure templates. Key properties:

- Stored by `frame:SetAttribute(name, value)` / read by `frame:GetAttribute(name)`.
- Do **not** carry taint — secure code reading an attribute remains secure even if the attribute was set by an addon.
- Names are **case-insensitive** on set/get. In `OnAttributeChanged`, the `name` argument is always lowercase. Values can be any Lua type (string, number, boolean, table, frame).
- Every set fires `OnAttributeChanged(self, name, value)`, where `name` is always lowercase. This handler is the canonical bridge between attribute updates and cosmetic (insecure) updates.
- Setting an attribute is itself a restricted action on a protected frame, so it is **blocked in combat** from tainted code. Pre-configure outside combat; snippets can set attributes in combat.

### `SecureActionButtonTemplate` (Ch 15)

The workhorse. Click dispatch is driven by a `type` attribute; each `type` consumes additional named attributes:

| `type` | Key sub-attributes | Underlying action |
|--------|-------------------|-------------------|
| `spell` | `spell`, `unit` | `CastSpellByName(spell, unit)` |
| `item` | `item` (name / `"item:ID"` / `"slot"` / `"bag slot"`), `unit`, `target-slot`, `target-bag`, `target-item` | `UseItemByName` / `UseInventoryItem` |
| `macro` | `macro` (name or index) or `macrotext` (≤ 1023 bytes) | `RunMacro` / `RunMacroText` |
| `action` | `action` (slot, optional) | `UseAction(slot)` |
| `actionbar` | `action` (page number, `"1,2"` swap, `"increment"`, `"decrement"`) | `ChangeActionBarPage` |
| `pet` | `action` | `CastPetAction` |
| `multispell` | `action`, `spell` | Multi-cast slot |
| `cancelaura` | `index` **or** `spell` (+ `rank`) | `CancelUnitBuff` |
| `target` | `unit` (`"none"` clears) | `TargetUnit` / place held cursor on unit |
| `focus`, `assist` | `unit` | `FocusUnit` / `AssistUnit` |
| `maintank`, `mainassist` | `unit`, `action` (`set`/`clear`/`toggle`) | Raid role |
| `stop` | — | Cancels targeting cursor |
| `click` | `clickbutton` (direct frame reference, not name) | `button:Click()` securely |
| `attribute` | `attribute-frame`, `attribute-name`, `attribute-value` | `SetAttribute` on another protected frame |

If `type` is none of the above and a matching `_<type>` attribute exists containing a snippet, the click is dispatched to that snippet (this is the "custom type" entry point used by `menu`-style buttons in `BlessedMenus` in Ch 25).

### Modified Attributes (prefix-root-suffix)

Attribute names on secure templates follow the grammar:

```
[modifier-]root[suffix]
    modifier : combination of alt / ctrl / shift in alphabetical order,
               joined by hyphens. Trailing hyphen before the root.
    root     : the logical attribute (spell, unit, type, item, ...)
    suffix   : * (any) or a mouse-button digit 1..5 (1=left,2=right,3=middle,4,5)
```

Lookup order for `GetModifiedAttribute(root, clickButton, modifiersHeld)`:

1. `<modifier>-<root><button>` — exact
2. `*<root><button>` — any modifier, exact button
3. `<modifier>-<root>*` — exact modifier, any button
4. `*<root>*` — any modifier, any button
5. `<root>` — unmodified fallback

Important quirk: `spell` (bare) matches as the final fallback, while `spell2` (right-click only, no wildcard) matches **only when no modifier is held**. This trips up many authors — when in doubt prefer wildcards.

Override mappings via `modifiers` attribute, format `MODIFIER[:name][,MODIFIER[:name]]...`, e.g. `SELFCAST:self,ALT`.

### `useparent-*` Delegation

If a modified attribute isn't found, the secure helper then looks for `useparent-<root>` or `useparent*` on the button; if present, it re-runs the lookup on the parent frame. This is how Blizzard's action-bar buttons inherit `unit`, `actionpage`, etc. from `MainMenuBarArtFrame` / `MultiBarRight` / …

### `helpbutton` / `harmbutton`

When a unit is friendly (`UnitCanAssist`), a non-nil `helpbutton` value is spliced in as the *suffix*; when hostile (`UnitCanAttack`), `harmbutton` is. This lets a single button cast a heal on friends and a damage spell on enemies without branching logic.

```lua
btn:SetAttribute("unit", "target")
btn:SetAttribute("harmbutton", "nuke")
btn:SetAttribute("helpbutton", "heal")
btn:SetAttribute("*spell-nuke", "Shadow Word: Pain")
btn:SetAttribute("*spell-heal", "Lesser Heal")
```

Requires `unit` to be set explicitly — if `unit` is nil, both attributes are ignored.

### Secure Handlers & Snippets (Ch 25)

Snippets are **strings of Lua source** stored in frame attributes; the secure infrastructure compiles them into secure functions that run in a *restricted environment*. This is the only way for addons to take protected actions (move/show/hide protected frames, change attributes) **during combat**.

#### Handler templates

| Template | Attribute | Arguments to snippet |
|----------|-----------|----------------------|
| `SecureHandlerClickTemplate` | `_onclick` | `self, button, down` |
| `SecureHandlerDoubleClickTemplate` | `_ondoubleclick` | `self, button, down` |
| `SecureHandlerMouseUpDownTemplate` | `_onmouseup`, `_onmousedown` | `self, button` |
| `SecureHandlerMouseWheelTemplate` | `_onmousewheel` | `self, delta` |
| `SecureHandlerEnterLeaveTemplate` | `_onenter`, `_onleave` | `self` |
| `SecureHandlerDragTemplate` | `_ondragstart`, `_onreceivedrag` | `self, button, kind, value, ...` |
| `SecureHandlerShowHideTemplate` | `_onshow`, `_onhide` | `self` |
| `SecureHandlerAttributeTemplate` | `_onattributechanged` | `self, name, value` |
| `SecureHandlerStateTemplate` | `_onstate-<name>` | `self, stateid, newstate` |

A drag snippet may `return "kind", value, ...` to load or exchange cursor contents; add `"clear"` as first return to clear first. Supported kinds: `action`, `bag`, `bagslot`, `inventory`, `item`, `macro`, `petaction`, `spell`, `companion`, `equipmentset`, `money`, `merchant`.

Snippets are authored with Lua **long brackets** `[[ ... ]]` so embedded quotes and newlines need no escaping.

#### The restricted environment

Snippets compile into functions whose `_ENV` is a private per-frame table shared across all snippets belonging to the same "owner" frame. This environment:

- Has no reference to most game API. **Allowed** library functions: `type`, `select`, `tonumber`, `tostring`, `print`, `pairs`, `ipairs`, `next`, `unpack`; `string`, `math`, and a sandboxed `table` module.
- `table.new(...)` is the only way to create tables — the bare `{}` constructor **is rejected at compile time**. Non-integer keys must be added post-creation.
- Cannot declare new Lua functions (no `function` keyword permitted).
- Exposes an approved subset of WoW API: `SecureCmdOptionParse`, `GetMouseButtonClicked`, `GetShapeshiftForm`, `GetActionBarPage`, `GetBonusBarOffset`, `IsStealthed`, `IsMounted`, `IsSwimming`, `IsFlying`, `IsFlyableArea`, `IsIndoors`, `IsOutdoors`, `IsAltKeyDown`/`IsShiftKeyDown`/`IsControlKeyDown` (plus left/right variants), `IsModifierKeyDown`, `IsModifiedClick`, `GetBindingKey`, `HasAction`, `IsHarmfulSpell`/`IsHelpfulSpell`/`IsHarmfulItem`/`IsHelpfulItem`, `UnitExists`, `UnitIsDead`, `UnitIsGhost`, `UnitPlayerOrPetInParty`, `UnitPlayerOrPetInRaid`, `GetActionInfo`, `RegisterStateDriver`. Player-only variants: `PlayerCanAttack`, `PlayerCanAssist`, `PlayerIsChanneling`, `PlayerInCombat`, `PlayerInGroup` (returns `"raid"`/`"party"`/`nil`), `PlayerPetSummary`.
- Unreferenced globals inside snippets become **private globals** scoped to the frame's environment — they persist across invocations and let snippets track state without exposing data to insecure code (see `lastMenu` in BlessedMenus, Ch 25).

#### Frame handles

Frames cannot be imported into the sandbox directly. Instead, `SecureHandlerSetFrameRef(owner, label, otherFrame)` stores a **frame handle** (userdata proxy) under attribute `frameref-<label>`. Inside a snippet, `self:GetFrameRef("label")` retrieves it. Handles support most read/write frame methods (`Show`, `Hide`, `SetPoint`, `SetParent`, `SetAttribute`, `GetAttribute`, `GetChildren`, `IsProtected`, ...). Differences:

- They are **userdata**, not tables — you cannot set fields on them (`handle.foo = 1` errors).
- Methods that mutate an **unprotected** handle during combat throw an error.
- `SetPoint`/`SetAllPoints`/`SetParent` require the target frame to be **explicitly** protected, not merely implicitly.
- Special anchor arguments available only to handles: `"$parent"`, `"$screen"`, `"$cursor"` (places frame at current mouse position; it does **not** track the cursor).
- Handle-only methods: `GetFrameRef`, `GetEffectiveAttribute`, `GetChildList(tbl)`, `GetMousePosition`, `IsUnderMouse`, `RegisterAutoHide` / `UnregisterAutoHide` / `AddToAutoHide`, `SetBindingClick` / `SetBinding` / `SetBindingSpell` / `SetBindingMacro` / `SetBindingItem` / `ClearBinding` / `ClearBindings`.

#### The `control` object

Each secure environment contains a `control` object used to escape the sandbox in controlled ways:

- `control:CallMethod(key, ...)` — retrieves `owner[key]` (a plain Lua function) and calls it **insecurely** with `(owner, ...)`. Returns are discarded. Use this for cosmetic updates (`SetStatusBarColor`, `SetTexture`, etc.) driven from secure state changes. The snippet that called it remains secure.
- `control:Run(snippet, ...)` / `control:RunFor(handle, snippet, ...)` — execute an inline snippet with the owner frame (or `handle`) as `self`.
- `control:RunAttribute(name, ...)` — retrieves and runs the snippet stored in `owner:GetAttribute(name)`. Effectively the only way to call "private" snippets whose attribute names start with `_`.
- `control:ChildUpdate(id, message)` — for each child, dispatches the child's `_childupdate-<id>` or `_childupdate` snippet. Legacy, largely obsolete.

### State Drivers (`RegisterStateDriver`)

`RegisterStateDriver(frame, stateName, macroConditionString)` registers the frame to receive `_onstate-<stateName>` dispatches whenever the value of a macro-conditional string changes.

```lua
-- Show only in combat, hide otherwise:
RegisterStateDriver(frame, "visibility", "[combat] show; hide")

-- Drive action-bar page by stance:
RegisterStateDriver(MainMenuBarArtFrame, "page", "[bonusbar:5] 11; [bonusbar:1] 7; 1")
```

The `visibility` state is special-cased: values `"show"` and `"hide"` are applied automatically. Any other `stateName` triggers the `_onstate-<stateName>` snippet with `(self, stateid, newstate)`.

`RegisterUnitWatch(frame)` is a sugar wrapper: it auto-registers a visibility driver that shows the frame iff `UnitExists(frame:GetAttribute("unit"))`. Used by the default unit frames and by `SecureGroupHeaderTemplate` children (see [Ch 26](../chapters/ch26-unit-frames-group-templates.md)).

### `SecureHandlerWrapScript` (pre/post hooks)

`SecureHandlerWrapScript(wrappedFrame, scriptName, ownerFrame, preSnippet [, postSnippet])` secure-wraps an existing script handler. The pre-hook runs before the original; its first return value replaces `button` (useful for click handlers), second return is a `message` forwarded to the post-hook. A pre-hook returning literal `false` **suppresses** the original handler entirely. Requirements:

- The `ownerFrame` must be *explicitly* protected.
- If `wrappedFrame` becomes unprotected (all protected children reparented away), wrappers are silently bypassed until protection is restored.
- Unwrap with `SecureHandlerUnwrapScript(wrappedFrame, scriptName)`; it returns `(preSnippet, ownerFrame, postSnippet)`.

Wrappable scripts: `OnClick`, `OnDoubleClick`, `PreClick`, `PostClick`, `OnMouseUp`, `OnMouseDown`, `OnMouseWheel`, `OnEnter`, `OnLeave`, `OnDragStart`, `OnReceiveDrag`, `OnShow`, `OnHide`, `OnAttributeChanged`.

### `SecureHandlerExecute`

```lua
SecureHandlerExecute(frame, snippetString)
```

Runs a snippet in `frame`'s secure environment **immediately**, with `self = frame`. Blocked during combat (insecure caller). Primarily used for setup/bootstrap of other secure handler frames.

### Detecting Taint

```
/console taintLog 1       -- log taint only when it causes a block
/console taintLog 2       -- log every taint event (huge volume)
/console taintLog 0       -- off
```

Log file: `World of Warcraft\Logs\taint.log`. Written on `/reload` or client exit, periodically truncated during play. Each block entry contains the offending addon name, the blocked API, and a stack trace.

Programmatic check: `issecurevariable("name")` returns `true` if the named global is currently considered secure. `issecurevariable(table, "key")` works on table fields.

### Common Error Messages

- **"Interface action failed because of an AddOn"** — tainted call stack reached a protected function. Check `taint.log`.
- **"An action was blocked because of taint from <Addon>"** — same, but WoW identified the source directly.
- **"ADDON_ACTION_BLOCKED"** / **"ADDON_ACTION_FORBIDDEN"** — events fired when the block happens; some addons intercept and suppress them (controversial: may hide real problems).

## Examples

### Minimal heal button (Ch 15)

```lua
local b = CreateFrame("Button", "QH", UIParent,
                     "SecureActionButtonTemplate,UIPanelButtonTemplate2")
b:SetSize(80, 20)
b:SetPoint("CENTER")
b:SetText("Heal")
b:RegisterForClicks("AnyUp")
b:SetAttribute("type", "spell")
b:SetAttribute("spell", "Flash Heal")
b:SetAttribute("unit", "player")
b:SetAttribute("shift-type1", "spell")
b:SetAttribute("shift-spell1", "Power Word: Fortitude")
```

### Auto-hiding action bar (Ch 25, SecureHandlerEnterLeaveTemplate)

```lua
local show = [[ self:GetFrameRef("subject"):Show() ]]
local hide = [[ self:GetFrameRef("subject"):Hide() ]]
for _, bar in ipairs({MultiBarRight, MultiBarLeft,
                      MultiBarBottomRight, MultiBarBottomLeft}) do
    local w = CreateFrame("Frame", nil, bar:GetParent(),
                          "SecureHandlerEnterLeaveTemplate")
    w:SetAllPoints(bar)
    w:SetFrameLevel(bar:GetFrameLevel() - 1)
    bar:SetParent(w)
    bar:Hide()
    w:SetAttribute("_onenter", show)
    w:SetAttribute("_onleave", hide)
    SecureHandlerSetFrameRef(w, "subject", bar)
end
```

### State-driven visibility

```lua
local f = CreateFrame("Frame", "CombatOnly", UIParent, "SecureFrameTemplate")
f:SetSize(100, 100); f:SetPoint("CENTER")
RegisterStateDriver(f, "visibility", "[combat] show; hide")
```

### Combat bracket for deferred reconfiguration

```lua
local pending
local gate = CreateFrame("Frame")
gate:RegisterEvent("PLAYER_REGEN_ENABLED")
gate:SetScript("OnEvent", function() if pending then Apply(); pending=nil end end)

function RequestReconfigure()
    if InCombatLockdown() then pending = true
    else Apply() end
end
```

## Common Bugs and Pitfalls

- **Overwriting a secure `OnClick`**: setting your own `OnClick` on a frame inheriting `SecureActionButtonTemplate` *removes* the secure handler; the button will no longer cast. Use `PreClick`/`PostClick` or `SecureHandlerWrapScript` instead.
- **Using `{}` inside a snippet** — compile failure. Use `table.new()`.
- **Using `function(...) end` inside a snippet** — compile failure. Encapsulate reusable code as another attribute and call `control:RunAttribute`.
- **Storing a frame in a private global** — snippets only get *handles*, not frames. Use `SecureHandlerSetFrameRef` + `GetFrameRef`.
- **Configuring attributes in combat** — silently blocked. Either configure on `PLAYER_REGEN_ENABLED` or push the change via a snippet.
- **Parent relationships cause implicit taint**: anchoring a secure frame to a frame whose table you've mutated taints the secure frame. Keep the two hierarchies separate.
- **Setting frame keys to pass data** (`frame.myFlag = 1`) on a protected frame inside tainted code — taints subsequent secure reads. Use attributes instead.
- **Suppressing `ADDON_ACTION_BLOCKED`** — hides symptoms, not causes. Leave it on during development.

## Related Concepts

- [events-system](events-system.md) — `PLAYER_REGEN_DISABLED`/`ENABLED` bracket combat lockdown
- [frame-templates](frame-templates.md) — the `protected="true"` tag attribute defines a protected template
- [function-hooking](function-hooking.md) — `hooksecurefunc` vs taint; why hooking `CastSpellByName` breaks it
- [key-bindings](key-bindings.md) — `SetOverrideBindingClick` / frame-handle bindings
- [Ch 15](../chapters/ch15-secure-templates.md), [Ch 25](../chapters/ch25-protected-action-in-combat.md), [Ch 26](../chapters/ch26-unit-frames-group-templates.md)
- Entity: [CombatStatus](../entities/combatstatus.md), [SquareUnitFrames](../entities/squareunitframes.md)
