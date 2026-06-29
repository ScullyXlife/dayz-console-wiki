---
title: "cfgundergroundtriggers.json"
nav_order: 11
parent: "Mission Root Config"
---

# cfgundergroundtriggers.json

## Purpose

`cfgundergroundtriggers.json` defines the underground zones and their entry/exit trigger areas. When a player crosses into a trigger volume, the engine switches them to the underground rendering and simulation context. This file defines where those transitions happen and what underground areas they connect to.

On most console servers this file is left at defaults. It becomes relevant if your server has a custom underground area or if you are adjusting the trigger geometry for existing underground zones.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgundergroundtriggers.json    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgundergroundtriggers.json    (PlayStation)
```

---

## Full Block Structure

**Vanilla default — empty trigger list:**

```json
{
  "Triggers": []
}
```

The vanilla file ships with no triggers defined. Triggers are added when underground areas with entry/exit points exist on the map.

**Populated example:**

```json
{
  "Triggers": [
    {
      "name": "Bunker_Main_Entrance",
      "pos": [4521.0, 15.0, 7832.0],
      "size": [3.0, 4.0, 3.0],
      "dir": [0.0, 0.0, 1.0],
      "innerDist": 1.5,
      "USAreaName": "Bunker_01"
    },
    {
      "name": "Bunker_Secondary_Entrance",
      "pos": [4535.0, 14.5, 7845.0],
      "size": [2.5, 3.5, 2.5],
      "dir": [1.0, 0.0, 0.0],
      "innerDist": 1.0,
      "USAreaName": "Bunker_01"
    }
  ]
}
```

---

## Field-by-Field Reference

### `Triggers` (array)

Array of trigger volume definitions. Each entry is one entrance/exit point to an underground area.

---

### Per-Trigger Fields

#### `name`
**Type:** String  
Identifier for this trigger. Used in logs. Should be unique.

---

#### `pos`
**Type:** Array of 3 floats `[X, Y, Z]`  
World position of the trigger volume center. Y is height above terrain.

---

#### `size`
**Type:** Array of 3 floats `[width, height, depth]`  
Dimensions of the trigger box in meters. Players entering this volume trigger the underground transition.

---

#### `dir`
**Type:** Array of 3 floats `[X, Y, Z]`  
Direction vector indicating which way the trigger is oriented (facing direction).

---

#### `innerDist`
**Type:** Float (meters)  
Inner distance threshold within the trigger volume at which the transition actually fires.

---

#### `USAreaName`
**Type:** String  
The underground area this trigger connects to. Multiple triggers can reference the same area name — they are all entry/exit points for the same underground space.

---

## What Console Admins Typically Adjust

Rarely edited in normal console operations. Relevant when:
- Adding a custom bunker or underground area
- Adjusting trigger size if players are having trouble with underground transitions
- Adding new entry points to an existing underground area

---

## Common Mistakes

| Mistake | Result |
|---|---|
| `pos` coordinates wrong | Trigger is in wrong location or underground — players can't find it |
| `size` too small | Players walk past the entrance without triggering the transition |
| `USAreaName` mismatch between entrance triggers | Multiple entrances don't connect to same underground space |
| JSON syntax error | File rejected entirely, underground transitions may break |
