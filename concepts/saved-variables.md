---
title: Saved Variables
type: concept
source_chapters: [8, 13]
created: 2026-04-15
updated: 2026-04-17
tags: [persistence, savedvariables, toc, addon-loaded, lifecycle]
---

# Saved Variables

## Summary

SavedVariables are the **only first-class persistence mechanism** for addons in WoW 3.3.5a. An addon declares one or more named global variables in its `.toc`; the game serializes those variables to a Lua file on `/reload`, logout, or client exit, and deserializes them before the addon's code runs for the next session.

Two scopes exist:

- `## SavedVariables:` — account-wide, shared by every character on the account.
- `## SavedVariablesPerCharacter:` — isolated per (realm + character).

Both directives accept a comma-separated list of global names. There is no Blizzard-provided API for explicit save/load; the entire lifecycle is implicit.

## Detailed Explanation

### Declaration (TOC)

```
## Interface: 30300
## Title: MyAddon
## SavedVariables: MyAddonDB, MyAddonDebug
## SavedVariablesPerCharacter: MyAddonCharDB
MyAddon.lua
```

Rules:

- Only **global** variable names are valid. Locals in a file are never saved.
- Changes to `## SavedVariables*` directives take effect only on a **full client restart** — not `/reload`, not re-entering world. `/reload` re-parses addon files but does not re-read the addon metadata.
- The variable does not need to exist at save time; missing globals are stored as `nil` (i.e. the file simply omits them).

### On-Disk Layout

```
<WoW>\WTF\Account\<AccountName>\SavedVariables\<AddonName>.lua
<WoW>\WTF\Account\<AccountName>\<Realm>\<Character>\SavedVariables\<AddonName>.lua
```

The file is a valid Lua chunk; the client writes `VariableName = <literal>` for each declared name. Comments at the top record timestamp and WoW build.

### What Can Be Serialized

The raw MCP chapters explicitly state that a SavedVariable can be a `string`, `number`, `Boolean`, or `table`.

The source material does not document the serializer's edge-case behavior in more detail, so this wiki avoids making stronger claims here. In practice, treat SavedVariables as **plain Lua data** intended for persistent settings/state rather than depending on undocumented behavior for runtime objects or unusually complex table shapes.

### Lifecycle & Timing (3.3.5a)

`ADDON_LOADED` is the common initialization anchor, rather than `VARIABLES_LOADED`. The full load sequence (Ch 8):

1. Client launch: `WTF\Account\...\SavedVariables\<AddonName>.lua` is located but **not yet parsed**.
2. Character selected; entering world.
3. `FrameXML` loads (Blizzard's own addons).
4. For each enabled non-LoD addon, in dependency order:
   a. Dependencies resolved first.
   b. `.toc` file list processed, each file parsed and executed top-level (XML/Lua).
   c. **SavedVariables are assigned** to the declared globals.
   d. `ADDON_LOADED` fires with the addon name.
5. Blizzard's own SavedVariables/keybinds/macros synchronize in the background.
6. `SPELLS_CHANGED` fires (spellbook usable).
7. `PLAYER_LOGIN` fires (most player APIs usable).
8. `PLAYER_ENTERING_WORLD` fires.
9. Eventually, `VARIABLES_LOADED` fires — by this point **all** SavedVariables are loaded for every addon.

Critical consequences:

- During **top-level** execution of your Lua files, `MyAddonDB` is still `nil`. Never touch your SavedVariables at file scope.
- Initialize in an `ADDON_LOADED` handler filtered by your addon name, *or* at `PLAYER_LOGIN`.
- If your addon depends on another addon's SavedVariables, observe its `ADDON_LOADED` or wait for `VARIABLES_LOADED`.

See [addon-loading-lifecycle](addon-loading-lifecycle.md) for the full timeline.

### When Data Is Written

The client persists SavedVariables at:

- `/reload` (`ReloadUI()`)
- Character logout back to character select
- Full client exit
- **Never** mid-session. Crashes discard the session's changes.

Addons wanting durability against crashes must minimize time between observable user action and a `/reload`, or accept the loss.

### Load-on-Demand Addons

LoD addons (`## LoadOnDemand: 1`) have their SavedVariables loaded when `LoadAddOn("<name>")` is called, which fires their `ADDON_LOADED`. Until then the declared globals are `nil`. Authors that split configuration UIs into LoD addons must be careful: the main addon cannot read the LoD addon's DB before it is requested.

### Defensive Initialization Pattern

```lua
local ADDON = "MyAddon"
local defaults = {
    version = 1,
    showFrame = true,
    position = { point = "CENTER", x = 0, y = 0 },
}

local function copyDefaults(src, dst)
    for k, v in pairs(src) do
        if type(v) == "table" then
            dst[k] = dst[k] or {}
            copyDefaults(v, dst[k])
        elseif dst[k] == nil then
            dst[k] = v
        end
    end
end

local f = CreateFrame("Frame")
f:RegisterEvent("ADDON_LOADED")
f:SetScript("OnEvent", function(self, _, name)
    if name ~= ADDON then return end
    MyAddonDB = MyAddonDB or {}
    copyDefaults(defaults, MyAddonDB)
    self:UnregisterEvent("ADDON_LOADED")
end)
```

Always merge defaults rather than replacing the table — users may have values from older versions that should survive migrations.

### Schema Migration

Because there is no built-in versioning, authors conventionally stamp a `version` field:

```lua
local CURRENT = 3
if not MyAddonDB.version then MyAddonDB.version = 1 end
if MyAddonDB.version < 2 then
    MyAddonDB.legacyField, MyAddonDB.newField = nil, MyAddonDB.legacyField
    MyAddonDB.version = 2
end
if MyAddonDB.version < 3 then
    -- ... subsequent migration ...
    MyAddonDB.version = 3
end
```

Never blow away the table wholesale on a version mismatch unless you explicitly intend to. Users keep settings across addon upgrades.

### PLAYER_LOGOUT for final writes

`PLAYER_LOGOUT` fires *just before* the client serializes SavedVariables. It is the correct hook for any last-moment bookkeeping (compacting a history buffer, stripping transient fields). Handlers must be fast and must not call APIs that require the game world to still be present.

```lua
f:RegisterEvent("PLAYER_LOGOUT")
f:SetScript("OnEvent", function(_, event)
    if event == "PLAYER_LOGOUT" then
        MyAddonDB.sessionCache = nil     -- drop transient state before write
    end
end)
```

### Size and Performance

- The raw chapters do not define a formal size limit for SavedVariables.
- As practical guidance, keep them focused on durable state rather than using them as large transient caches or per-session logs.
- Remember the documented write points: the client persists SavedVariables on logout, client exit, and `/reload`, not continuously during play.

### BagBuddy Example (Ch 13)

BagBuddy persists loot timestamps in a per-character SavedVariable:

```
## SavedVariablesPerCharacter: BagBuddy_ItemTimes
```

Initialization (inside the addon's `ADDON_LOADED` branch):

```lua
if event == "ADDON_LOADED" and ... == "BagBuddy" then
    BagBuddy_ItemTimes = BagBuddy_ItemTimes or {}
    for bag = 0, NUM_BAG_SLOTS do
        BagBuddy_ScanBag(bag, true)   -- seed from persisted timestamps
    end
    self:UnregisterEvent("ADDON_LOADED")
    self:RegisterEvent("BAG_UPDATE")
end
```

The `or {}` idiom handles first-ever launch where the variable has not yet been written.

## Common Bugs

- **Mutating SavedVariables at file scope** — variable is `nil` at that point; `MyAddonDB.x = 1` errors.
- **Storing frames** — silently dropped on save; tests pass in-session, break on reload.
- **Storing cross-references inside one table** — serializer cannot express shared references; loads as two separate tables.
- **Forgetting to restart the client after changing `## SavedVariables*`** — `/reload` is not enough.
- **Replacing instead of merging defaults** — resets user settings on every login.
- **Assuming `VARIABLES_LOADED` fires before the first `ADDON_LOADED`** — it fires later; trust `ADDON_LOADED` with your name.
- **Using `SavedVariablesPerCharacter` then expecting shared account data** — each realm+character has its own file.

## Related Concepts

- [addon-loading-lifecycle](addon-loading-lifecycle.md) — full timeline of `ADDON_LOADED`, `PLAYER_LOGIN`, `PLAYER_ENTERING_WORLD`, `VARIABLES_LOADED`
- [toc-file](toc-file.md) — all TOC directives
- [events-system](events-system.md) — `ADDON_LOADED`, `PLAYER_LOGOUT`
- [addon-structure](addon-structure.md) — where SavedVariables fit in the file layout
- [addon-libraries](addon-libraries.md) — AceDB-3.0 wraps SavedVariables with profiles, defaults, and migration helpers
- [Ch 8 — Anatomy of an Addon](../chapters/ch08-anatomy-of-addon.md)
