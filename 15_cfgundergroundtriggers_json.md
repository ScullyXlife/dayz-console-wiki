---
title: "cfgundergroundtriggers.json"
nav_order: 11
parent: "Mission Root Config"
---

# cfgundergroundtriggers.json

## Purpose

`cfgundergroundtriggers.json` defines the trigger volumes that switch a player into the underground rendering and simulation context (bunkers) and back out again. This file defines where those transitions happen.

On most console servers this file is left at defaults. It becomes relevant if your server has a custom underground area or if you are adjusting trigger geometry for an existing one.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgundergroundtriggers.json    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgundergroundtriggers.json    (PlayStation)
```

---

## Full Block Structure

The installed schema does not match the field names used in older guides (`name`, `pos`, `size`, `dir`, `innerDist`, `USAreaName`). The confirmed structure is:

```json
{
  "Triggers": [
    {
      "Position": [4521.0, 15.0, 7832.0],
      "Orientation": [0.0, 0.0, 0.0],
      "Size": [3.0, 4.0, 3.0],
      "EyeAccommodation": 1.5,
      "InterpolationSpeed": 1.0,
      "AmbientSoundSet": "bunker_ambient",
      "Breadcrumbs": [
        {
          "Position": [4521.0, 14.0, 7830.0],
          "EyeAccommodation": 1.0,
          "Radius": 2.0,
          "UseRaycast": true
        }
      ]
    }
  ]
}
```

An installed 8-trigger mission carries 21 `Breadcrumbs` records total across its triggers.

---

## Field-by-Field Reference

### `Triggers` (array)

Array of trigger volume definitions. Each entry is one underground transition point.

---

### Per-Trigger Fields

#### `Position`
Array of 3 floats `[X, Y, Z]`. World position of the trigger volume.

#### `Orientation`
Array of 3 floats. Rotation of the trigger volume.

#### `Size`
Array of 3 floats `[width, height, depth]` in meters. Dimensions of the trigger box.

#### `EyeAccommodation`
Float. Governs how the camera/eye adjusts when crossing the trigger (transition easing). Exact formula not confirmed.

#### `InterpolationSpeed`
Float. Speed of the visual transition between above-ground and underground rendering contexts.

#### `AmbientSoundSet`
String. Name of the ambient sound set played inside the connected underground area.

#### `Breadcrumbs`
Array of waypoint-style records used inside the underground area itself, each with `Position`, `EyeAccommodation`, `Radius`, and `UseRaycast`. Their exact routing purpose (guiding the transition camera, or pathing) is not fully confirmed — treat as an observed field list, not a fully proven behavior.

---

## What Console Admins Typically Adjust

Rarely edited in normal console operations. Relevant when:
- Adding a custom bunker or underground area
- Adjusting `Size` if players are having trouble triggering the transition
- Adding new `Breadcrumbs` entries to an existing underground area

---

## Common Mistakes

| Mistake | Result |
|---|---|
| `Position` coordinates wrong | Trigger is in the wrong location — players can't find it |
| `Size` too small | Players walk past the entrance without triggering the transition |
| JSON syntax error | File rejected entirely, underground transitions may break |

---

## Confidence Note

This page reflects the confirmed installed field shape (`Position`, `Orientation`, `Size`, `EyeAccommodation`, `InterpolationSpeed`, `AmbientSoundSet`, `Breadcrumbs`). The previous `name`/`pos`/`dir`/`innerDist`/`USAreaName` schema did not match any installed mission and has been removed. The exact numeric behavior of `EyeAccommodation` and `InterpolationSpeed` is observed-field-only, not binary-confirmed.
