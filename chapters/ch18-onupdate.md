---
title: "Ch 18 — Responding to Graphic Updates with OnUpdate"
type: chapter
source_chapters: [18]
part: "III — Advanced Addon Techniques"
pages: 8
chars: 11034
created: 2026-04-15
updated: 2026-04-15
tags: [OnUpdate, animation, performance, frame-rate, elapsed]
---

# Ch 18 — Responding to Graphic Updates with OnUpdate

## Overview

The `OnUpdate` script handler fires every frame render — useful for animations, smooth updates, and periodic checks. Also covers performance implications and throttling.

## Key Concepts

### OnUpdate Handler
```lua
frame:SetScript("OnUpdate", function(self, elapsed)
    -- elapsed = seconds since last frame
end)
```

- Fires every rendered frame (~60 times/second at 60 FPS)
- `elapsed` parameter = time in seconds since last OnUpdate
- **Performance warning**: expensive OnUpdate handlers reduce FPS

### Common Uses
- Smooth animations (position, alpha, color transitions)
- Periodic polling (check something every N seconds)
- Cooldown displays
- Timer countdowns

### Throttling Pattern
Run expensive code only every N seconds:
```lua
local timer = 0
frame:SetScript("OnUpdate", function(self, elapsed)
    timer = timer + elapsed
    if timer >= 0.5 then  -- every half second
        timer = 0
        -- do expensive work
    end
end)
```

### Animation System Alternative
WoW provides a built-in animation system that is more efficient for common animation patterns:
- `AnimationGroup` — container for animations
- `Alpha`, `Scale`, `Translation`, `Rotation` — animation types
- `OnFinished` — callback when animation completes
- Preferred over manual OnUpdate for simple animations

### Performance Best Practices
- Avoid creating closures or tables inside OnUpdate
- Use throttling for non-smooth updates
- Remove OnUpdate handler when not needed: `frame:SetScript("OnUpdate", nil)`
- Prefer the animation system when applicable

## Connections

- Complements the [Events System](../concepts/events-system.md) (events = game state; OnUpdate = frame-rate)
- Used for smooth display in [CombatTracker](ch14-combat-tracker.md) timer
- Performance considerations in [Appendix A](ch31-best-practices.md)
