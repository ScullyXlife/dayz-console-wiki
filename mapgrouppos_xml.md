---
title: "mapgrouppos.xml"
nav_order: 2
parent: "Map Loot System"
---

# mapgrouppos.xml

## Purpose

`mapgrouppos.xml` places instances of building loot blueprints (defined in `mapgroupproto.xml`) at real world positions on the map. Where mapgroupproto.xml defines *what loot a building type can have*, mapgrouppos.xml says *which actual buildings in the world use that blueprint*.

Every building in the game world that has active loot spawning has an entry here linking it to its proto group definition.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/mapgrouppos.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/mapgrouppos.xml    (PlayStation)
```

## Connects To

- **mapgroupproto.xml** — `group=` values here must match `name=` values in mapgroupproto.xml
- **mapgroupcluster*.xml** — clusters reference groups of positions from this file

---

## Full Block Structure

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<map>

  <group pos="3421.50 0.00 7832.10" rpy="0.00 0.00 0.00" a="52.3" name="Land_PoliceStation_Small" />
  <group pos="5102.25 0.00 4671.80" rpy="0.00 0.00 0.00" a="127.0" name="Land_House_1W01" />
  <group pos="6234.10 0.00 3901.45" rpy="0.00 0.00 0.00" a="0.0" name="Land_Mil_Barracks_i" />

</map>
```

**Root element correction:** the document root is `<map>`, not `<structures>`. The `<group pos rpy a name/>` record shape itself is unchanged and confirmed.

---

## Field-by-Field Reference

### `<group pos="..." rpy="..." a="..." name="..." />`

One building placement entry.

| Attribute | What it controls |
|---|---|
| `pos` | World position as "X Y Z". Y is typically `0.00` (terrain height is resolved automatically). |
| `rpy` | Roll/Pitch/Yaw rotation. Usually `"0.00 0.00 0.00"`. |
| `a` | Angle (yaw rotation) of the building in degrees. |
| `name` | The proto group name from mapgroupproto.xml that this building instance uses. |

---

## How This Works With mapgroupproto.xml

```
mapgroupproto.xml:
  defines group "Land_PoliceStation_Small" with lootmax=8, containers, points

mapgrouppos.xml:
  places "Land_PoliceStation_Small" at world pos 3421.50, 7832.10
  places "Land_PoliceStation_Small" at world pos 9021.30, 2301.60
  (every actual police station building in the world gets its own entry)
```

The same proto group appears multiple times in mapgrouppos.xml — once for every building of that type that exists on the map.

---

## What Console Admins Typically Adjust

This file is rarely edited manually on console servers. It is large (thousands of entries for a full map) and the positions correspond to exact building world coordinates.

**When you would edit it:**
- Adding loot spawning to a building that currently has none (add a new entry pointing it to an appropriate proto group)
- Removing loot from a specific building (delete its entry)
- Changing which proto a specific building uses

**You would not edit this file to change how much loot spawns** — that is in mapgroupproto.xml. This file only controls which buildings participate and which proto they use.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| `name=` references a group not in mapgroupproto.xml | That building position has no valid loot blueprint, no loot spawns there |
| Wrong world coordinates | Loot points float in air or are underground at the wrong location |
| Duplicate entries for the same building | Two overlapping loot systems at same position |
