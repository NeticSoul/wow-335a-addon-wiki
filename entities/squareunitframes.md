---
title: SquareUnitFrames (Sample Addon)
type: entity
source_chapters: [26]
created: 2026-04-15
updated: 2026-04-15
tags: [addon, sample, unit-frames, raid, secure]
---

# SquareUnitFrames

A compact raid-frame addon from Ch 26 built with SecureGroupHeaderTemplate.

## Purpose
Replaces the default party/raid frames with custom compact squares showing health, name, and debuff status.

## Key Techniques Demonstrated
- `SecureGroupHeaderTemplate` for automatic unit frame management
- Group sorting/filtering attributes
- Unit watch registration (`RegisterUnitWatch`)
- Health bar updates via `UNIT_HEALTH` events
- Compact UI layout for raid scenarios

## See Also
- [Secure Execution](../concepts/secure-execution.md)
- [Frames and Widgets](../concepts/frames-and-widgets.md)
- [Unit IDs](../concepts/unit-ids.md)
- [Events System](../concepts/events-system.md)
