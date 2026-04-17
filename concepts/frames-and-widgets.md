---
title: Frames and Widgets
type: concept
source_chapters: [8, 9, 12, 29]
created: 2026-04-15
updated: 2026-04-17
tags: [frames, widgets, textures, fontstrings, layers, anchors, strata]
---

# Frames and Widgets

## Summary

The entire WoW 3.3.5a user interface is a tree of **widgets** - Lua tables backed by C++ objects - rooted at `UIParent` (the game UI) and `WorldFrame` (the 3D world). Every visible element is a widget: frames, buttons, textures, font strings, status bars, tooltips, minimap, models. Widgets have a fixed **type hierarchy**, are positioned via **anchors** (not coordinates), rendered in a predictable order determined by **strata x level x draw layer**, and expose both Lua methods and XML attributes for configuration.

Understanding three placement mechanisms - parenting, anchoring, and layering - is the prerequisite for everything else in the UI.

## Detailed Explanation

### Widget Type Hierarchy (WoW 3.3.5a)

```
UIObject
+-- Region                     (has position, size, visibility)
|   +-- LayeredRegion          (has draw layer)
|   |   +-- Texture
|   |   +-- FontString
|   +-- Frame                  (container; receives events & input)
|       +-- Button -> CheckButton
|       +-- EditBox
|       +-- Slider
|       +-- StatusBar
|       +-- ScrollFrame
|       +-- ScrollingMessageFrame
|       +-- MessageFrame
|       +-- SimpleHTML
|       +-- ColorSelect
|       +-- GameTooltip
|       +-- Minimap
|       +-- Model -> PlayerModel -> DressUpModel / TabardModel
|       +-- Cooldown
|       +-- MovieFrame
+-- FontInstance                (Font definitions; inherited by FontString)
```

Only **Frames** can register events, receive input, and have child widgets. `Texture` and `FontString` are leaf rendering elements and **must** have a parent frame.

### Creation

**Lua:**
```lua
local f = CreateFrame(widgetType, [globalName], [parent], [inheritsTemplate])
```

- `widgetType` string matches the XML tag: `"Frame"`, `"Button"`, `"StatusBar"`, etc.
- `globalName` is optional - omit for anonymous frames. Names register in `_G`.
- `parent` is the **frame object**, not its name.
- `inheritsTemplate` is a comma-separated list of virtual templates. See [frame-templates](frame-templates.md).

Textures/FontStrings are created on a parent frame:

```lua
local tex = frame:CreateTexture(name, drawLayer, inheritsTemplate, subLayer)
local fs  = frame:CreateFontString(name, drawLayer, inheritsTemplate)
```

**XML:**
```xml
<Frame name="MyFrame" parent="UIParent">
    <Size x="384" y="512"/>
    <Anchors>
        <Anchor point="CENTER"/>
    </Anchors>
</Frame>
```

Anywhere a name appears in XML, the string `$parent` is expanded to the parent's name. Prefer this over hand-concatenation.

### Parenting

Parenting has three effects:

1. **Visibility cascade** - hiding a parent hides all descendants (`IsVisible()` returns `false` for any descendant of a hidden frame).
2. **Effective scale** - `effectiveScale = ownScale * parent.effectiveScale`.
3. **Effective alpha** - `effectiveAlpha = ownAlpha * parent.effectiveAlpha`.

Setting parent:
- XML attribute: `parent="FrameName"` (Frame only; Textures/FontStrings are implicitly parented by nesting).
- XML nesting: a sub-frame placed inside `<Frames>` of another is automatically parented.
- Runtime: `child:SetParent(parentFrame)`.

Most UI frames should be parented to `UIParent` so that `Alt-Z` (UI hide) works correctly and the master UI scale applies.

### Sizing

- Absolute: `<Size x="384" y="512"/>` or `SetSize(w, h)` / `SetWidth`, `SetHeight`.
- Relative: `<Size><RelDimension x="0.5" y="0.5"/></Size>` - percentage of parent size. Rarely used; the default UI doesn't use it.
- Implicit: a frame with anchors covering opposite sides (e.g. `TOPLEFT` and `BOTTOMRIGHT`) has size derived from its anchors; no `<Size>` required.

### Anchoring (Not Coordinates)

All placement uses **anchors** - links between one of **nine points** on a frame and a point on another frame. A frame may have multiple anchors; resolution happens at layout time.

```
         TOPLEFT - TOP - TOPRIGHT
          |       |       |
            LEFT - CENTER - RIGHT
          |       |       |
     BOTTOMLEFT - BOTTOM - BOTTOMRIGHT
```

```xml
<Anchor point="TOPLEFT"                      <!-- point on THIS frame -->
        relativeTo="OtherFrame"              <!-- optional; defaults to parent -->
        relativePoint="BOTTOMLEFT">          <!-- point on target; defaults to same as `point` -->
    <Offset x="0" y="-5"/>
</Anchor>
```

Runtime equivalent:

```lua
f:SetPoint("TOPLEFT", otherFrame, "BOTTOMLEFT", 0, -5)
```

Key behaviors:

- **Sticky**: if `otherFrame` moves, `f` follows. The anchor is a live link, not a one-time placement.
- **Override**: calling `SetPoint` again for the same point replaces it. To rebuild: `f:ClearAllPoints()` first.
- **Stretch**: two or more non-colinear anchors stretch the frame between them.
- `SetAllPoints(other)` / XML `setAllPoints="true"`: shortcut for matching all four corners of `other` (defaults to parent).

### Layering: Strata -> Level -> Draw Layer

Rendering order is a **three-tier hierarchy**:

**1. Frame strata** (coarsest). Lowest to highest:

| Strata | Typical use |
|--------|-------------|
| `BACKGROUND` | Non-interactive backdrops; mouse blocked unless frame level > 1. |
| `LOW` | Buff frame, durability frame, party frame, pet frame. |
| `MEDIUM` | Default for `UIParent` children. |
| `HIGH` | Action buttons, tutorial frames, error frames. |
| `DIALOG` | Popup dialogs. |
| `FULLSCREEN` | World map, interface options. |
| `FULLSCREEN_DIALOG` | Dropdown menus atop fullscreen UI. |
| `TOOLTIP` | Always on top. |
| `PARENT` | Inherit parent's strata. |

Set with `SetFrameStrata("HIGH")` or XML `frameStrata="HIGH"`.

**2. Frame level** (within strata). Integer; higher draws atop lower. A child's default frame level is `parent.level + 1`. Override: `SetFrameLevel(n)`, XML `frameLevel="5"`.

**3. Draw layer** (within a single frame). For textures/font strings:

| Layer | Purpose |
|-------|---------|
| `BACKGROUND` | Background fill. |
| `BORDER` | Frame artwork, borders. |
| `ARTWORK` | Primary content (icons, portrait). |
| `OVERLAY` | On top of artwork (text labels, sweep overlays). |
| `HIGHLIGHT` | Rendered only on mouseover; frame must have mouse enabled. |

Within a layer, font strings always render over textures.

**Top-level promotion**: `SetTopLevel(true)` / `toplevel="true"` - on click, the frame is promoted to the highest level in its strata. Used for movable/resizable panels.

### Visibility

An object is **drawn** only when *all* of these hold:

1. It has a visual component (text, graphic, color) or descendants that do.
2. Width and height are both > 0.
3. Some portion lies within screen bounds.
4. It and every ancestor are `Shown` (not `Hidden`).

Methods:
- `IsShown()` - just this frame's own state.
- `IsVisible()` - this frame AND all ancestors are shown.
- `Show()` / `Hide()`.
- XML `hidden="true"` - starts hidden.

### Script Handlers

Scripts are bound per-widget via `SetScript`, `GetScript`, `HookScript` (secure, append-only - see [function-hooking](function-hooking.md)).

Common handlers:

| Handler | Args | Fires on |
|---------|------|----------|
| `OnLoad` | `self` | Frame created (XML only; Lua `CreateFrame` has no OnLoad). |
| `OnShow`, `OnHide` | `self` | Visibility change. |
| `OnEvent` | `self, event, ...` | Registered game event fires. See [events-system](events-system.md). |
| `OnUpdate` | `self, elapsed` | Every render frame while visible. See [onupdate-throttling](onupdate-throttling.md). |
| `OnMouseDown`, `OnMouseUp` | `self, button` | Mouse button press/release on frame. |
| `OnClick` | `self, button, down` | Button-type widgets. |
| `OnEnter`, `OnLeave` | `self, motion` | Mouseover enter/leave. |
| `OnKeyDown`, `OnKeyUp` | `self, key` | Requires `EnableKeyboard(true)`. |
| `OnAttributeChanged` | `self, name, value` | `SetAttribute` called. Bridge from secure -> insecure. |
| `OnDragStart`, `OnDragStop` | `self` | Drag begins/ends. Requires `RegisterForDrag`. |

### Input Enablement

- `EnableMouse(bool)` - frame receives mouse events.
- `EnableKeyboard(bool)` - frame receives keyboard events while focused.
- `RegisterForClicks("LeftButtonUp", "RightButtonDown", ...)` - button-specific.
- `RegisterForDrag("LeftButton")` - drag support.
- `SetMovable(true)` + `StartMoving`/`StopMovingOrSizing` - draggable.
- `SetResizable(true)` + `SetMinResize`/`SetMaxResize` - resizable.

### Widget Type Cheat Sheet

| Type | Role | Key methods |
|------|------|-------------|
| `Frame` | Container; events; input | `RegisterEvent`, `SetBackdrop`, `SetMovable` |
| `Button` | Clickable; 3 textures (Normal/Pushed/Highlight) | `SetText`, `Enable`, `Disable`, `RegisterForClicks` |
| `CheckButton` | Toggle button | `SetChecked`, `GetChecked` |
| `EditBox` | Text input | `SetText`, `GetText`, `SetFocus`, `ClearFocus`, `SetMaxLetters` |
| `Slider` | Range | `SetMinMaxValues`, `SetValue`, `SetValueStep` |
| `StatusBar` | Progress bar | `SetStatusBarTexture`, `SetStatusBarColor`, `SetMinMaxValues`, `SetValue` |
| `ScrollFrame` | Scroll viewport | `SetScrollChild`, `SetVerticalScroll` |
| `ScrollingMessageFrame` | Auto-scrolling log | `AddMessage`, `Clear` |
| `MessageFrame` | Auto-fading text | `AddMessage`, `SetFading` |
| `GameTooltip` | Tooltip | `SetOwner`, `AddLine`, `SetBagItem`, ... see [tooltips](tooltips.md) |
| `Model` | 3D model display | `SetModel`, `SetCamera`, `SetRotation` |
| `Minimap` | Minimap | `SetZoom`, `GetZoom`, `GetZoomLevels` |
| `Cooldown` | Sweep effect | `SetCooldown(start, duration)` |
| `SimpleHTML` | HTML subset | `SetText` |
| `ColorSelect` | Color picker | `SetColorRGB`, `GetColorRGB` |

### Textures

```lua
local tex = frame:CreateTexture(name, "ARTWORK")
tex:SetTexture("Interface\\Icons\\INV_Misc_EngGizmos_30")  -- file
tex:SetTexture(1, 0, 0, 0.5)                                -- r,g,b,a solid color
tex:SetTexCoord(0, 1, 0, 1)                                 -- UV sub-rect
tex:SetBlendMode("ADD")                                     -- DISABLE/BLEND/ALPHAKEY/ADD/MOD
tex:SetVertexColor(r, g, b, a)
tex:SetGradient("HORIZONTAL", r1,g1,b1, r2,g2,b2)
tex:SetAllPoints(frame)
```

`alphaMode` / `SetBlendMode` values:

| Mode | Effect |
|------|--------|
| `DISABLE` | Ignore alpha; fully opaque. |
| `BLEND` | Standard alpha blending. |
| `ALPHAKEY` | Black pixels transparent, others opaque. |
| `ADD` | Additive (used for glows, highlights). |
| `MOD` | Multiplicative (darken background). |

### Font Strings

```lua
local fs = frame:CreateFontString(name, "OVERLAY", "GameFontNormal")
fs:SetText("Hello")
fs:SetPoint("TOP", 0, -10)
fs:SetTextColor(1, 0.82, 0)
fs:SetJustifyH("CENTER")  -- LEFT / CENTER / RIGHT
fs:SetJustifyV("MIDDLE")  -- TOP  / MIDDLE / BOTTOM
```

Font strings inherit from **font definitions** (`GameFontNormal`, `GameFontHighlight`, `NumberFontNormal`, etc.) defined in `Fonts.xml` / `FontStyles.xml`. Changes to a font definition propagate live to every inheriting font string. See [frame-templates](frame-templates.md).

### Name Lookups and `parentKey`

When an XML element has a `name` attribute, it becomes a global. `$parent` expands to the parent frame's name:

```xml
<Frame name="BagBuddy">
    <Layers>
        <Layer level="BACKGROUND">
            <Texture name="$parent_Portrait" parentKey="portrait"/>
        </Layer>
    </Layers>
</Frame>
```

Yields three equivalent access paths to the texture:

```lua
BagBuddy_Portrait       -- global, from name="$parent_Portrait"
BagBuddy.portrait       -- parentKey attribute sets this key on the parent frame
BagBuddy["portrait"]    -- same, bracket syntax
```

Prefer `parentKey` - it is faster (no global lookup), doesn't pollute `_G`, and doesn't require the frame to be named. Name frames anyway so `/framestack` shows something useful.

### Debugging: `/framestack`

Toggle with `/framestack`. Displays the stack of frames under the cursor, grouped by strata, annotated with frame levels. Essential for inspecting Blizzard UI or debugging anchor/layer issues.

## Examples

### Minimal frame with events (Lua)

```lua
local f = CreateFrame("Frame", "MyAddonFrame", UIParent)
f:SetSize(300, 200)
f:SetPoint("CENTER")
f:SetBackdrop({
    bgFile   = "Interface\\DialogFrame\\UI-DialogBox-Background",
    edgeFile = "Interface\\DialogFrame\\UI-DialogBox-Border",
    tile = true, tileSize = 32, edgeSize = 32,
    insets = { left = 11, right = 12, top = 12, bottom = 11 },
})
f:EnableMouse(true)
f:SetMovable(true)
f:RegisterForDrag("LeftButton")
f:SetScript("OnDragStart", f.StartMoving)
f:SetScript("OnDragStop",  f.StopMovingOrSizing)

f:RegisterEvent("PLAYER_LOGIN")
f:SetScript("OnEvent", function(self, event, ...)
    if event == "PLAYER_LOGIN" then
        print("Hello from MyAddonFrame")
    end
end)
```

### Equivalent in XML

```xml
<Frame name="MyAddonFrame" parent="UIParent" movable="true" enableMouse="true">
    <Size x="300" y="200"/>
    <Anchors>
        <Anchor point="CENTER"/>
    </Anchors>
    <Backdrop bgFile="Interface\DialogFrame\UI-DialogBox-Background"
              edgeFile="Interface\DialogFrame\UI-DialogBox-Border" tile="true">
        <BackgroundInsets>
            <AbsInset left="11" right="12" top="12" bottom="11"/>
        </BackgroundInsets>
        <TileSize><AbsValue val="32"/></TileSize>
        <EdgeSize><AbsValue val="32"/></EdgeSize>
    </Backdrop>
    <Scripts>
        <OnLoad>
            self:RegisterForDrag("LeftButton")
            self:SetScript("OnDragStart", self.StartMoving)
            self:SetScript("OnDragStop",  self.StopMovingOrSizing)
            self:RegisterEvent("PLAYER_LOGIN")
        </OnLoad>
        <OnEvent>
            if event == "PLAYER_LOGIN" then print("Hello") end
        </OnEvent>
    </Scripts>
</Frame>
```

### BagBuddy portrait (Ch 9)

```xml
<Frame name="BagBuddy" parent="UIParent">
    <Size x="384" y="512"/>
    <Anchors><Anchor point="CENTER"/></Anchors>
    <Layers>
        <Layer level="BACKGROUND">
            <Texture name="$parent_Portrait" parentKey="portrait"
                     file="Interface\Icons\INV_Misc_EngGizmos_30">
                <Size x="60" y="60"/>
                <Anchors>
                    <Anchor point="TOPLEFT"><Offset x="7" y="-6"/></Anchor>
                </Anchors>
            </Texture>
        </Layer>
        <Layer level="BORDER">
            <Texture file="Interface\BankFrame\UI-BankFrame-TopLeft">
                <Anchors><Anchor point="TOPLEFT"/></Anchors>
            </Texture>
            <!-- 3 more corners -->
        </Layer>
        <Layer level="OVERLAY">
            <FontString name="$parent_Title" parentKey="title"
                        inherits="GameFontNormal" text="BagBuddy">
                <Anchors>
                    <Anchor point="TOP"><Offset y="-18"/></Anchor>
                </Anchors>
            </FontString>
        </Layer>
    </Layers>
    <Scripts>
        <OnLoad function="BagBuddy_OnLoad"/>
    </Scripts>
</Frame>
```

```lua
function BagBuddy_OnLoad(self)
    -- SetPortraitToTexture crops a square icon into the circular portrait cut-out
    SetPortraitToTexture(self.portrait, "Interface\\Icons\\INV_Misc_EngGizmos_30")
end
```

## Common Bugs

- **Creating a frame but nothing appears** - no visual component, zero size, off-screen anchor, or hidden ancestor. Check all four visibility rules.
- **Two frames mutually anchored** (`A` anchored to `B`, `B` anchored to `A`) - layout cycle; client clears anchors.
- **`SetPoint` called multiple times without `ClearAllPoints`** - previous anchors remain, producing stretched or displaced frames.
- **Using `parent="Something"` for a Texture or FontString** - textures must be declared inside a frame's `<Layers>`; the `parent` attribute does nothing on them.
- **Setting `strata` to `BACKGROUND` and expecting mouse clicks** - background strata blocks mouse unless frame level > 1.
- **`$parent` in a non-named frame** - expands to the empty string, producing name collisions.
- **Calling `CreateFrame("Frame", name, ...)` when `name` already exists as a global** - clobbers the prior frame silently.
- **Forgetting `EnableMouse(true)`** when a custom frame needs clicks or hover (textures & font strings in `HIGHLIGHT` layer don't appear without mouse enabled).

## Related Concepts

- [frame-templates](frame-templates.md) - virtual templates, `parentKey`, Blizzard templates library
- [xml-layout](xml-layout.md) - the XML schema and element-by-element reference
- [events-system](events-system.md) - `RegisterEvent` / `OnEvent`
- [onupdate-throttling](onupdate-throttling.md) - per-frame rendering callback
- [function-hooking](function-hooking.md) - `HookScript` for safe handler composition
- [secure-execution](secure-execution.md) - protected frames and restricted widget API
- [tooltips](tooltips.md) - `GameTooltip` frame type
- [Ch 9 - Frames and Widgets](../chapters/ch09-frames-and-widgets.md)
- [Ch 12 - Interacting with Widgets](../chapters/ch12-interacting-with-widgets.md)
- [Ch 29 - Widget Reference](../chapters/ch29-widget-reference.md)
- [Widget hierarchy](../reference/widget-hierarchy.md)
