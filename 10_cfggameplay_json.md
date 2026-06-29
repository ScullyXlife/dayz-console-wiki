---
title: "cfggameplay.json"
nav_order: 5
parent: "Mission Root Config"
---

# cfggameplay.json

## Purpose

`cfggameplay.json` is the primary gameplay rules file. It controls a wide range of player behavior settings — stamina, shock recovery, movement, drowning, base building placement rules, UI elements, map behavior, and vehicle decay. Unlike XML economy files, this is a JSON file and must follow valid JSON syntax.

This file only takes effect when `enableCfgGameplayFile` is set to `1` (enabled) in your Nitrado server settings.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfggameplay.json    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfggameplay.json    (PlayStation)
```

**Prerequisite:** In Nitrado settings, `enableCfgGameplayFile` must be enabled (set to 1 / true). If this is off, the file is completely ignored.

---

## Full Block Structure

```json
{
  "version": 123,
  "GeneralData": {
    "disableBaseDamage": false,
    "disableContainerDamage": false,
    "disableRespawnDialog": false,
    "disableRespawnInUnconsciousness": false
  },
  "PlayerData": {
    "disablePersonalLight": false,
    "StaminaData": {
      "sprintStaminaModifierErc": 1.0,
      "sprintStaminaModifierCro": 1.0,
      "staminaWeightLimitThreshold": 6000.0,
      "staminaMax": 100.0,
      "staminaKgToStaminaPercentPenalty": 1.75,
      "staminaMinCap": 5.0,
      "sprintSwimmingStaminaModifier": 1.0,
      "sprintLadderStaminaModifier": 1.0,
      "meleeStaminaModifier": 1.0,
      "obstacleTraversalStaminaModifier": 1.0,
      "holdBreathStaminaModifier": 1.0
    },
    "ShockHandlingData": {
      "shockRefillSpeedConscious": 5.0,
      "shockRefillSpeedUnconscious": 1.0,
      "allowRefillSpeedModifier": true
    },
    "MovementData": {
      "timeToStrafeJog": 0.1,
      "rotationSpeedJog": 0.3,
      "timeToSprint": 0.45,
      "timeToStrafeSprint": 0.3,
      "rotationSpeedSprint": 0.15,
      "allowStaminaAffectInertia": true
    },
    "DrowningData": {
      "staminaDepletionSpeed": 10.0,
      "healthDepletionSpeed": 10.0,
      "shockDepletionSpeed": 10.0
    },
    "WeaponObstructionData": {
      "staticMode": 1,
      "dynamicMode": 1
    },
    "spawnGearPresetFiles": []
  },
  "WorldsData": {
    "lightingConfig": 0,
    "objectSpawnersArr": [],
    "environmentMinTemps": [-3,-2,0,4,9,14,18,17,13,11,9,0],
    "environmentMaxTemps": [3,5,7,14,19,24,26,25,18,14,10,5],
    "wetnessWeightModifiers": [1.0, 1.0, 1.33, 1.66, 2.0],
    "playerRestrictedAreaFiles": []
  },
  "BaseBuildingData": {
    "HologramData": {
      "disableIsCollidingBBoxCheck": false,
      "disableIsCollidingPlayerCheck": false,
      "disableIsClippingRoofCheck": false,
      "disableIsBaseViableCheck": false,
      "disableIsCollidingGPlotCheck": false,
      "disableIsCollidingAngleCheck": false,
      "disableIsPlacementPermittedCheck": false,
      "disableHeightPlacementCheck": false,
      "disableIsUnderwaterCheck": false,
      "disableIsInTerrainCheck": false,
      "disableColdAreaPlacementCheck": false,
      "disableColdAreaBuildingCheck": false,
      "disallowedTypesInUnderground": ["FenceKit","TerritoryFlagKit","WatchtowerKit"]
    },
    "ConstructionData": {
      "disablePerformRoofCheck": false,
      "disableIsCollidingCheck": false,
      "disableDistanceCheck": false
    }
  },
  "UIData": {
    "use3DMap": false,
    "HitIndicationData": {
      "hitDirectionOverrideEnabled": false,
      "hitDirectionBehaviour": 1,
      "hitDirectionStyle": 0,
      "hitDirectionIndicatorColorStr": "0xffbb0a1e",
      "hitDirectionMaxDuration": 2.0,
      "hitDirectionBreakPointRelative": 0.2,
      "hitDirectionScatter": 10.0,
      "hitIndicationPostProcessEnabled": true
    }
  },
  "MapData": {
    "ignoreMapOwnership": false,
    "ignoreNavItemsOwnership": false,
    "displayPlayerPosition": false,
    "displayNavInfo": true
  },
  "VehicleData": {
    "boatDecayMultiplier": 1.0
  }
}
```

---

## Section-by-Section Reference

### `version`

**Type:** Integer  
Internal version marker. The vanilla value is `123`. Do not change this — the engine uses it to track file compatibility.

---

### `GeneralData`

#### `disableBaseDamage`
**Type:** Boolean (true/false) or integer (0/1)  
Disables damage dealt to base-building structures from the environment and decay.

- `false` — bases take decay damage normally
- `true` — base structures do not decay from environmental damage

**Popular on console:** `true` — players build once and don't have to repair constantly.

---

#### `disableContainerDamage`
**Type:** Boolean  
Disables damage to containers like tents, barrels, and storage items.

- `true` — tents and barrels do not degrade

---

#### `disableRespawnDialog`
**Type:** Boolean  
Disables the UI prompt that asks players to choose their respawn type after pressing the respawn button.

- `true` — removes the dialog, player respawns immediately via the configured spawn method

---

#### `disableRespawnInUnconsciousness`
**Type:** Boolean  
Prevents players from using the respawn button while unconscious.

- `false` (default) — players can respawn while unconscious
- `true` — players must wait out the unconscious state or die naturally

---

### `PlayerData`

#### `disablePersonalLight`
**Type:** Boolean  
Disables the ambient personal light that illuminates objects near the player at night.

- `false` — subtle light exists around player at night
- `true` — pure darkness at night, significantly harder without a light source

---

#### `StaminaData`

All stamina values must be ≥ 0.

| Field | What it controls | Default |
|---|---|---|
| `sprintStaminaModifierErc` | Rate of stamina consumed during upright sprinting. Higher = more drain. | 1.0 |
| `sprintStaminaModifierCro` | Rate of stamina consumed during crouched sprinting | 1.0 |
| `staminaMax` | Maximum stamina pool size | 100.0 |
| `staminaMinCap` | Minimum stamina cap — even heavily encumbered players retain at least this much | 5.0 |
| `staminaWeightLimitThreshold` | Weight (in grams) below which no stamina penalty applies | 6000.0 |
| `staminaKgToStaminaPercentPenalty` | How much each kg of overweight reduces max stamina (%) | 1.75 |
| `sprintSwimmingStaminaModifier` | Stamina drain rate during fast swimming | 1.0 |
| `sprintLadderStaminaModifier` | Stamina drain rate during fast ladder climbing | 1.0 |
| `meleeStaminaModifier` | Stamina consumed per heavy melee attack | 1.0 |
| `obstacleTraversalStaminaModifier` | Stamina consumed during jumps, vaults, and climbs | 1.0 |
| `holdBreathStaminaModifier` | Stamina drain rate when holding breath (scoped shooting) | 1.0 |

**To reduce stamina impact:** Lower the modifier values below 1.0. Values above 1.0 increase drain.

---

#### `ShockHandlingData`

| Field | What it controls | Default |
|---|---|---|
| `shockRefillSpeedConscious` | Shock recovery rate per second while conscious | 5.0 |
| `shockRefillSpeedUnconscious` | Shock recovery rate per second while unconscious | 1.0 |
| `allowRefillSpeedModifier` | Allow ammo type to affect shock recovery speed | true |

Higher `shockRefillSpeedConscious` means players wake up from unconsciousness faster.

---

#### `MovementData`

| Field | What it controls | Minimum |
|---|---|---|
| `timeToStrafeJog` | Blend time for diagonal jog movement (seconds) | 0.01 |
| `rotationSpeedJog` | Character rotation speed while jogging | 0.01 |
| `timeToSprint` | Time to reach full sprint from jog (seconds) | 0.01 |
| `timeToStrafeSprint` | Blend time for diagonal sprint movement (seconds) | 0.01 |
| `rotationSpeedSprint` | Character rotation speed while sprinting | 0.01 |
| `allowStaminaAffectInertia` | Stamina level affects movement inertia | — |

---

#### `DrowningData`

How fast each stat depletes per second while a player is underwater without air.

| Field | Default |
|---|---|
| `staminaDepletionSpeed` | 10.0 |
| `healthDepletionSpeed` | 2.0 |
| `shockDepletionSpeed` | 5.0 |

Higher values = faster drowning death.

---

#### `WeaponObstructionData`

Controls whether weapons visually lower/reposition when near walls or geometry.

| Value | Behavior |
|---|---|
| `0` | Disabled — weapon stays in raised position regardless |
| `1` | Enabled — weapon lowers near obstacles |
| `2` | Always — weapon always in lowered state |

`staticMode` applies to stationary obstructions. `dynamicMode` applies to moving obstructions.

---

#### `spawnGearPresetFiles`

**Type:** Array of strings  
List of JSON file paths (relative to mission root) pointing to player spawn gear loadout files. When populated, players spawn with the defined gear.

```json
"spawnGearPresetFiles": ["custom/starter_kit.json"]
```

Leave as empty array `[]` for default (naked) spawns.

---

### `WorldsData`

#### `lightingConfig`
**Type:** Integer  
Controls night-time lighting mode.

| Value | Effect |
|---|---|
| `0` | Bright nights — vanilla default |
| `1` | Dark nights — standard darkness |
| `2` | Sakhal mode — used for the snow map's specific lighting |

---

#### `objectSpawnersArr`
**Type:** Array of strings  
File paths to object spawner JSON files that place persistent static objects on the map (vehicles, static items, etc.).

```json
"objectSpawnersArr": ["custom/spawn_vehicles.json"]
```

---

#### `environmentMinTemps` and `environmentMaxTemps`
**Type:** Array of 12 numbers (exactly)  
Monthly temperature ranges in Celsius. Each position is a month (January through December).

```json
"environmentMinTemps": [-10,-8,-4,2,8,14,16,14,10,4,-2,-8],
"environmentMaxTemps": [2,4,8,16,22,26,28,26,22,14,6,2]
```

Must have exactly 12 values. Affects player warmth/cold gameplay.

---

#### `wetnessWeightModifiers`
**Type:** Array of exactly 5 numbers  
Weight multipliers for items at each wetness level: `[DRY, DAMP, WET, SOAKED, DRENCHED]`

---

### `BaseBuildingData`

#### `HologramData` — Placement Check Disablers

Each field disables a specific placement validation check. All default to `false` (checks enabled).

| Field | What disabling it allows |
|---|---|
| `disableIsCollidingBBoxCheck` | Place when colliding with world objects |
| `disableIsCollidingPlayerCheck` | Place when touching another player |
| `disableIsClippingRoofCheck` | Place when clipping roof geometry |
| `disableIsBaseViableCheck` | Place on dynamic objects and incompatible bases |
| `disableIsCollidingGPlotCheck` | Place garden plots on incompatible surfaces |
| `disableIsCollidingAngleCheck` | Place despite exceeding angle limits |
| `disableIsPlacementPermittedCheck` | Bypass basic placement permission checks |
| `disableHeightPlacementCheck` | Place with limited height clearance |
| `disableIsUnderwaterCheck` | Place underwater |
| `disableIsInTerrainCheck` | Place when clipping terrain |
| `disableColdAreaPlacementCheck` | Place garden plots on frozen ground |
| `disableColdAreaBuildingCheck` | Build in frozen area conditions |

**Console common use:** Some servers enable `disableIsCollidingAngleCheck` and `disableIsCollidingBBoxCheck` to make base building easier on uneven terrain.

#### `disallowedTypesInUnderground`
**Type:** Array of classname strings  
Item types that cannot be built in underground areas.

Default: `["FenceKit","TerritoryFlagKit","WatchtowerKit"]`

---

#### `ConstructionData`

| Field | What disabling it allows |
|---|---|
| `disablePerformRoofCheck` | Construct when clipping the roof |
| `disableIsCollidingCheck` | Construct when colliding with world objects |
| `disableDistanceCheck` | Remove minimum player distance requirement |

---

### `UIData`

#### `use3DMap`
**Type:** Boolean  
Enables the 3D map and disables the default 2D overlay map. Rarely used.

#### `HitIndicationData`

Controls the hit direction indicator (the UI element showing what direction damage came from).

| Field | What it controls |
|---|---|
| `hitDirectionOverrideEnabled` | Whether any of this block takes effect |
| `hitDirectionBehaviour` | `0`=disabled, `1`=static indicator, `2`=dynamic moving indicator |
| `hitDirectionStyle` | `0`=splash, `1`=spike, `2`=arrow |
| `hitDirectionIndicatorColorStr` | Color in ARGB hex format (`0xAARRGGBB`) |
| `hitDirectionMaxDuration` | How long the indicator shows (seconds) |
| `hitDirectionBreakPointRelative` | Fraction of duration before fading starts (0.0–1.0) |
| `hitDirectionScatter` | Angular inaccuracy added to direction (degrees) |
| `hitIndicationPostProcessEnabled` | Enable/disable the red screen flash on hit |

---

### `MapData`

| Field | What it controls |
|---|---|
| `ignoreMapOwnership` | Allow map to be opened without having a map item in inventory |
| `ignoreNavItemsOwnership` | Show compass/GPS helpers on map without owning those items |
| `displayPlayerPosition` | Show player's position marker on map |
| `displayNavInfo` | Show GPS/compass UI legend on the map screen |

**Popular console settings:** `ignoreMapOwnership: true`, `displayPlayerPosition: true` — makes navigation much friendlier for console players.

---

### `VehicleData`

#### `boatDecayMultiplier`
**Type:** Number  
Multiplies the speed at which boats decay.
- `1.0` — normal decay rate
- `0.0` — boats don't decay
- `2.0` — boats decay twice as fast

---

## JSON Syntax Rules

This file is JSON, not XML. Different rules apply:

- No trailing commas after the last item in an object or array
- Strings in double quotes only
- `true`/`false` lowercase (not `True` or `1` unless the schema accepts integers)
- Arrays use `[]`, objects use `{}`
- Comments are not supported in JSON — remove any `//` or `/* */` style comments

A single syntax error will cause the entire file to be ignored with no warning in game.

---

## What Console Admins Typically Adjust

1. **`disableBaseDamage: true`** — stop base decay
2. **`disableContainerDamage: true`** — protect tents and barrels
3. **`ignoreMapOwnership: true`** — quality-of-life for console players
4. **`displayPlayerPosition: true`** — show player dot on map
5. **`disablePersonalLight: true`** — hardcore night experience
6. **StaminaData modifiers** — reduce stamina drain for more casual gameplay
7. **`lightingConfig`** — change night brightness
8. **`spawnGearPresetFiles`** — add starter kits

---

## Common Mistakes

| Mistake | Result |
|---|---|
| `enableCfgGameplayFile` not enabled in Nitrado settings | File is completely ignored |
| JSON syntax error (trailing comma, missing brace) | Entire file ignored, no error shown in game |
| `environmentMinTemps` or `environmentMaxTemps` not exactly 12 values | File validation error |
| `wetnessWeightModifiers` not exactly 5 values | File validation error |
| Setting stamina modifier to `0` | Stamina cost becomes zero — no stamina drain ever |
