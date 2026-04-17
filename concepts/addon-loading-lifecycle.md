---
title: Addon Loading Lifecycle
type: concept
source_chapters: [8, 13]
created: 2026-04-17
updated: 2026-04-17
tags: [lifecycle, ADDON_LOADED, PLAYER_LOGIN, VARIABLES_LOADED, load-order, toc]
---

# Addon Loading Lifecycle

## Summary

The 3.3.5a client follows a **documented load sequence** when starting an addon session. Understanding this sequence is critical because most addon initialization bugs are timing bugs: code that assumes data exists before it has been loaded, or assumes an event fires that has not yet fired.

This page consolidates the timeline, identifies which APIs/data are valid at each stage, and lists the common hooks addon code should attach to.

## Detailed Explanation

### Client Launch (pre-character)

1. The `Interface\AddOns\` directory is scanned.
2. For each subfolder, `<FolderName>.toc` is parsed once: metadata (`Title`, `Notes`, `Interface`, `Dependencies`, `SavedVariables`, …) is cached.
3. **Metadata is cached until client exit** — `/reload` does *not* re-read `.toc` files. Any change to `## SavedVariables*`, `## Dependencies*`, `## LoadOnDemand`, etc. requires a full client restart.
4. The Addons button on the character-select screen shows addons whose `Interface` matches the client (others flagged *Out of date* or *Incompatible*).

### Entering World

Once the player picks a character and clicks Enter World:

5. `FrameXML` loads — Blizzard's own UI XML + Lua files are parsed in the order listed in `FrameXML.toc`. Some Blizzard addons (`Blizzard_CombatLog`, `Blizzard_AuctionUI`, `Blizzard_DebugTools`, ...) are marked `LoadOnDemand` and are not yet loaded.

6. **Addon load loop.** For each enabled, non-LoD addon, in dependency order (then OS-dependent order for non-dependent addons):

   a. If the addon has unloaded dependencies, load them first recursively.
   b. Parse the addon's `.toc` to get its file list.
   c. Load each listed file **in order**:
      - `.xml` files are parsed against the UI schema; top-level `<Script file="..."/>` tags resolve Lua files immediately.
      - `.lua` files are compiled and executed at top level. Globals created here are visible to later files.
      - Each file has its own upvalue scope: `local` variables in file A are invisible to file B.
   d. The addon's declared **SavedVariables are assigned** to the corresponding global names (still `nil` during step c!).
   e. `ADDON_LOADED` fires, with the addon's folder name as the single argument. Every registered event frame that listens for `ADDON_LOADED` receives this — your frame will see other addons' names too, so always filter.

7. Blizzard's own key bindings, macros, and account-wide SavedVariables begin synchronizing from the server; this runs in the background and can overlap subsequent steps.

8. `SPELLS_CHANGED` fires — spellbook and spell textures are populated. Note: in 3.3.5a this event also fires on every spell rank change thereafter, so it is not a reliable "spellbook ready" one-shot.

9. `PLAYER_LOGIN` fires — on login and UI reload, immediately before `PLAYER_ENTERING_WORLD`. By this point most player-state APIs are valid (`UnitName("player")`, `UnitClass`, `GetNumPartyMembers`, equipment, spec, inventory). This is a common initialization hook for anything that does not need SavedVariables of other addons.

10. `PLAYER_ENTERING_WORLD` fires — player has entered the game world. **Also fires on every `/reload` and every zone/instance transition.** Use it for state that must re-sync after reloads, loading screens, and zone/instance transitions.

11. At some indeterminate point after `PLAYER_ENTERING_WORLD`, `VARIABLES_LOADED` fires once. By this moment every addon's SavedVariables (and Blizzard's keybinds/macros) are fully synchronized. Historically this was the "safe to read *any* addon's DB" signal; in practice many addons use `PLAYER_LOGIN` or their own `ADDON_LOADED`.

### Load-on-Demand Activation

LoD addons (`## LoadOnDemand: 1`) are skipped in step 6 and load only when some other addon calls `LoadAddOn("<name>")`. The LoD loading path runs steps 6b–6e (and will fire that addon's `ADDON_LOADED` mid-session). Nested LoD chains are legal.

`## LoadsWith: <OtherAddon>` triggers an LoD addon's load immediately after `<OtherAddon>` finishes loading.

`## LoadManager: <Addon>` delegates the loading decision to an external addon (commonly **AddonLoader**) that may load it based on class, zone, spec, or custom conditions (`## X-LoadOn-Class: Rogue`, etc.).

### Logout / Reload

1. User issues `/reload`, `/logout`, `/exit`, or closes the client.
2. `PLAYER_LEAVING_WORLD` fires (rarely used).
3. `PLAYER_LOGOUT` fires — last hook where addon Lua can run and mutate SavedVariables.
4. All declared SavedVariables globals are serialized to disk.
5. Process exits (or, for `/reload`, re-runs from step 5 of "Entering World" — the raw events reference documents `PLAYER_LOGIN` immediately before `PLAYER_ENTERING_WORLD` on both login and UI reload).

### Which Event for Which Job?

| Need | Hook |
|------|------|
| First-time init of your SavedVariables | `ADDON_LOADED` filtered by your addon name |
| Read another addon's SavedVariables | That addon's `ADDON_LOADED`, or `VARIABLES_LOADED` |
| Query player state (class, spec, group) | `PLAYER_LOGIN` |
| Re-apply stuff after zone/instance/reload | `PLAYER_ENTERING_WORLD` |
| General initialization once world data is ready | `PLAYER_LOGIN` for most addons |
| Final save-side cleanup | `PLAYER_LOGOUT` |
| Spellbook textures available | `PLAYER_ENTERING_WORLD` (more reliable than `SPELLS_CHANGED`; see Ch 25 BlessedMenus note) |

### Common Timeline Traps

- **Assuming `PLAYER_LOGIN` does not fire on `/reload`** — the raw events reference documents it on UI reload as well. Prefer `PLAYER_ENTERING_WORLD` when you specifically need the broader loading-screen/zone-transition behavior.
- **Top-level `MyAddonDB.foo = 1`** — DB is still `nil` at file parse time.
- **Filtering `ADDON_LOADED` by equality to `GetAddOnMetadata("...", "X-Name")`** — `ADDON_LOADED`'s argument is the **folder name**, never the `Title` or `X-Name`.
- **Registering for `ADDON_LOADED` inside another addon's `ADDON_LOADED`** — risky if the target has already loaded before your code runs; check `IsAddOnLoaded("<name>")` immediately and fall back to the event.
- **Calling server-backed queries at `PLAYER_LOGIN`** — data (`GuildRoster`, `UPDATE_INSTANCE_INFO`, achievements) requires explicit request + paired event (see [events-system](events-system.md) § Query Events).
- **Dependency cycles** — the client silently refuses to load any addon in the cycle.

### Relationship With the Taint Model

SavedVariables assignment is done by secure code, so the restored globals are initially **secure**. The moment an addon's Lua runs and mutates them, they taint. This taint is inherited when Blizzard code later reads them — which is why some addons that replace Blizzard-owned globals (e.g. storing a table into `UIPanelWindowsOpen`) break protected action. See [secure-execution](secure-execution.md).

## Examples

### Robust initialization skeleton

```lua
local ADDON = "MyAddon"
local f = CreateFrame("Frame")
f:RegisterEvent("ADDON_LOADED")
f:RegisterEvent("PLAYER_LOGIN")
f:RegisterEvent("PLAYER_ENTERING_WORLD")
f:RegisterEvent("PLAYER_LOGOUT")

local inited
f:SetScript("OnEvent", function(self, event, a1)
    if event == "ADDON_LOADED" and a1 == ADDON then
        MyAddonDB = MyAddonDB or { version = 1 }
        self:UnregisterEvent("ADDON_LOADED")
    elseif event == "PLAYER_LOGIN" then
        if not inited then InitializeFrames(); inited = true end
    elseif event == "PLAYER_ENTERING_WORLD" then
        if inited then RefreshAfterReload() end
    elseif event == "PLAYER_LOGOUT" then
        MyAddonDB.sessionTmp = nil
    end
end)
```

### Waiting for another addon

```lua
local wait = CreateFrame("Frame")
local function TryInit()
    if IsAddOnLoaded("Recount") then
        HookIntoRecount()
        wait:UnregisterAllEvents()
    end
end
wait:RegisterEvent("ADDON_LOADED")
wait:SetScript("OnEvent", TryInit)
TryInit()                               -- in case Recount already loaded
```

## Related Concepts

- [saved-variables](saved-variables.md)
- [events-system](events-system.md)
- [toc-file](toc-file.md)
- [addon-structure](addon-structure.md)
- [secure-execution](secure-execution.md)
- [Ch 8 — Anatomy of an Addon](../chapters/ch08-anatomy-of-addon.md)
- [Ch 13 — Responding to Game Events](../chapters/ch13-game-events.md)
