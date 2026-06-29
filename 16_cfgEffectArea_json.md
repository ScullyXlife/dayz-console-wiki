---
title: "cfgEffectArea.json"
nav_order: 12
parent: "Mission Root Config"
---

# cfgEffectArea.json

## Purpose

`cfgEffectArea.json` defines contaminated areas on the map — the toxic gas zones players must use NBC gear to survive. Each area has a position, radius, height, particle effects, and player visual effects. The file also contains a list of safe positions outside contaminated zones.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgEffectArea.json    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgEffectArea.json    (PlayStation)
```

---

## Full Block Structure (Vanilla)

```json
{
  "Areas": [
    {
      "AreaName": "Ship-Bow",
      "Type": "ContaminatedArea_Static",
      "TriggerType": "ContaminatedTrigger",
      "Data": {
        "Pos": [13917, 0, 11178],
        "Radius": 75,
        "PosHeight": 20,
        "NegHeight": 3,
        "InnerPartDist": 80,
        "OuterOffset": 30,
        "ParticleName": "graphics/particles/contaminated_area_gas_bigass"
      },
      "PlayerData": {
        "AroundPartName": "graphics/particles/contaminated_area_gas_around",
        "TinyPartName": "graphics/particles/contaminated_area_gas_around_tiny",
        "PPERequesterType": "PPERequester_ContaminatedAreaTint"
      }
    }
  ],

  "SafePositions": [
    [434, 13624],
    [360, 10986]
  ]
}
```

---

## Field-by-Field Reference

### `Areas` (array)

Array of contaminated area definitions. Each entry is one contaminated zone.

---

### Per-Area Fields

#### `AreaName`
**Type:** String  
Unique identifier for this contaminated area. Used in logs and admin tools.

---

#### `Type`
**Type:** String  
The contamination area class type.

| Value | Meaning |
|---|---|
| `ContaminatedArea_Static` | Permanent, fixed-position contaminated zone |
| `ContaminatedArea_Dynamic` | Dynamically placed contaminated zone (event-driven) |

---

#### `TriggerType`
**Type:** String  
The trigger class used to detect player entry/exit.

`ContaminatedTrigger` is the standard value for all contaminated areas.

---

### `Data` object

The physical geometry and visual configuration of the zone.

#### `Pos`
**Type:** Array of 3 numbers `[X, Y, Z]`  
World center position of the contaminated area. Y is typically `0` — terrain height is used automatically.

---

#### `Radius`
**Type:** Integer (meters)  
Horizontal radius of the contaminated zone circle.

**Vanilla examples:**
- Ship zones: 60–100m
- Pavlovo military zones: 60–100m

---

#### `PosHeight`
**Type:** Integer (meters)  
How far above the terrain the contamination zone extends. Covers upward into buildings and hills.

---

#### `NegHeight`
**Type:** Integer (meters)  
How far below the terrain surface the zone extends downward. Prevents players from hiding underground to escape the gas.

---

#### `InnerPartDist`
**Type:** Integer (meters)  
Distance from center at which the inner gas particle effect starts. The particle effect is rendered within this radius of center.

---

#### `OuterOffset`
**Type:** Integer (meters)  
Offset distance for the outer boundary of particle rendering. Controls the visual fade zone at the edge of the contaminated area.

---

#### `ParticleName`
**Type:** String  
Path to the particle effect file used for the gas cloud visual.

Standard values:
- `"graphics/particles/contaminated_area_gas_bigass"` — large gas cloud for the main zone
- `"graphics/particles/contaminated_area_gas_around"` — smaller surrounding particle effect

---

### `PlayerData` object

Controls the visual effects applied to the player while inside the contaminated zone.

#### `AroundPartName`
**Type:** String  
Particle effect played around the player while in the zone.

#### `TinyPartName`
**Type:** String  
Smaller particle variant for tight spaces or lighter effect contexts.

#### `PPERequesterType`
**Type:** String  
Post-processing effect applied to the player's screen while in the zone.

`"PPERequester_ContaminatedAreaTint"` is the standard screen tint effect for contaminated areas.

---

### `SafePositions` (array)

A list of world X/Z coordinate pairs that are guaranteed to be outside any contaminated zone. Used by the engine as spawn fallback positions for newly spawned players to prevent them spawning inside a contaminated area.

```json
"SafePositions": [
  [434, 13624],
  [360, 10986],
  [1412, 13505]
]
```

Each entry is `[X, Z]`. These positions should cover all areas of the map that players might reasonably spawn at.

---

## Vanilla Contaminated Zones on Chernarus

The default Chernarus configuration has contaminated zones at:
- **The ship** (Rify/wreck offshore) — 4 overlapping zones covering bow, center, stern, and east
- **Pavlovo military base** — 6 zones covering uphill, buildings, and downhill sections

---

## What Console Admins Typically Adjust

1. **`Radius`** — increase to make a zone larger, decrease to tighten it.
2. **`Pos`** — move a zone to a different map position.
3. **Adding new areas** — define additional contaminated zones at new locations.
4. **`PosHeight`** — increase to cover taller structures within the zone.
5. **`SafePositions`** — add more safe spawn positions if you've added new zones that might block default spawns.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| JSON syntax error | File rejected entirely, vanilla contaminated zones may revert or zones fail to load |
| `Pos` coordinates wrong | Zone appears at wrong location |
| No `SafePositions` entries | Players may spawn inside contaminated zones |
| `InnerPartDist` larger than `Radius` | Particle effect extends beyond the actual zone boundary |
| Forgetting `PlayerData` block | Zone works but player has no screen visual effects while inside |
