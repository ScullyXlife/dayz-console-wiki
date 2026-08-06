---
title: "mapgroupproto.xml"
nav_order: 1
parent: "Map Loot System"
---

# mapgroupproto.xml

## Purpose

`mapgroupproto.xml` defines the loot blueprints for buildings — the physical loot points inside each building type, how many items can be there at once, what categories and usages are allowed at each spot, and how those spots are filtered. It is what makes a police station spawn police gear and a hospital spawn medical supplies rather than random items from everywhere.

This file does not control *which specific items* spawn — that is determined by types.xml usage/category routing. This file controls *where items can physically appear* inside buildings and *what rules filter which items are allowed in each spot*.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/mapgroupproto.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/mapgroupproto.xml    (PlayStation)
```

## Connects To

- **cfglimitsdefinition.xml** — category, tag, and usage names used here must be defined there
- **types.xml (db/)** — items are routed here via matching usage and category values
- **mapgrouppos.xml** — places group instances at specific world positions

---

## Full Block Structure

The installed root is `<prototype>`, not `<groups>`. Groups also support `<defaults>`, a `<value>` element, and `<dispatch>`/`<proxy>` records for exported proxy objects, none of which are in the older `<groups>` schema:

```xml
<prototype>

  <group name="Land_PoliceStation_Small" lootmax="8">
    <defaults>
      <category name="tools" />
    </defaults>
    <value>1</value>
    <usage name="Police" />
    <container name="lootFloor" lootmax="4">
      <category name="clothes" />
      <tag name="floor" />
      <point pos="1.2 0.4 -2.1" range="0.35" height="0.8" flags="32" />
      <point pos="-0.8 0.4 1.6" range="0.35" height="0.8" flags="32" />
    </container>
    <container name="lootShelves" lootmax="3">
      <category name="weapons" />
      <tag name="shelves" />
      <point pos="3.1 0.8 0.5" range="0.2" height="0.6" flags="32" />
    </container>
    <container name="lootGround" lootmax="2">
      <category name="tools" />
      <tag name="floor" />
      <point pos="0.0 0.2 0.0" range="0.5" height="0.5" flags="32" />
    </container>
    <dispatch dechance="0.1">
      <proxy type="Land_Wreck_Ikarus" pos="0 0 0" rpy="0 0 0" dechance="0.1" />
    </dispatch>
  </group>

  <group name="Land_House_1W01" lootmax="5">
    <usage name="Town" />
    <container name="lootFloor" lootmax="5">
      <category name="food" />
      <category name="clothes" />
      <category name="tools" />
      <tag name="floor" />
      <point pos="0.5 0.3 -1.0" range="0.4" height="0.7" flags="32" />
      <point pos="-1.2 0.3 0.8" range="0.4" height="0.7" flags="32" />
    </container>
  </group>

</prototype>
```

**Root element correction:** the document root is `<prototype>`. The rest of the group/container/point shape below is unchanged and confirmed.

---

## Field-by-Field Reference

### `<group name="..." lootmax="...">`

One building profile. Defines loot rules for a specific building classname.

| Attribute | What it controls |
|---|---|
| `name` | Building classname this profile applies to. Must match the engine's building class exactly. |
| `lootmax` | Total maximum items that can exist across all containers in this group simultaneously |

---

### `<usage name="..." />` (on group)

The usage channel this building belongs to. Matches usage values in types.xml. Items with a matching `<usage>` in types.xml are eligible to spawn in this building's loot points.

A group can have multiple `<usage>` entries — the building will accept items from any of those channels.

```xml
<group name="Land_PoliceStation_Small" lootmax="8">
  <usage name="Police" />
  <usage name="Military" />  <!-- also spawns military loot -->
```

---

### `<container name="..." lootmax="...">`

A sub-group of loot points within the building. Buildings are divided into containers to create different zones within the structure (floor loot, shelf loot, counter loot, etc.) with independent caps.

| Attribute | What it controls |
|---|---|
| `name` | Container identifier (used in logs and editor tools) |
| `lootmax` | Maximum items that can fill the points in this container at one time |

The sum of all container `lootmax` values should not exceed the group's `lootmax`. The group cap is the hard ceiling.

---

### `<category name="..." />` (on container)

Restricts which item category is eligible to spawn at points in this container. Must match a category defined in cfglimitsdefinition.xml.

Multiple `<category>` entries allow multiple categories at this container.

---

### `<tag name="..." />` (on container)

Further filters the container using placement context tags. Works alongside category to narrow which items from that category can appear here.

---

### `<point pos="x y z" range="..." height="..." flags="..." />`

One physical loot spawn volume inside the building.

| Attribute | Type | What it controls |
|---|---|---|
| `pos` | "x y z" floats | Position of the loot point center relative to the building's origin. Three space-separated values. |
| `range` | Float (meters) | Horizontal radius of the spawn volume. Items can appear anywhere within this circle. |
| `height` | Float (meters) | Vertical size of the spawn volume |
| `flags` | Integer | Bitmask controlling spawn behavior. `32` is the standard value for normal floor/ground loot. |

**Note on `pos`:** These are local coordinates relative to the building model's pivot point, not world coordinates. `y` here is vertical height within the building (e.g. `0.4` is just above floor level, `0.8` might be shelf height).

**Common `flags` values:**

| Value | Behavior |
|---|---|
| `32` | Standard loot spawn — items appear on ground/floor surfaces |

---

### `<defaults>` (on group)

Optional block of default category/tag/usage values applied to the group's containers unless a container overrides them. Point flags and full dispatch-behavior semantics beyond this are not fully resolved.

---

### `<value>` (on group)

A tier/value marker on the group itself, separate from any `<value>` entries inside container-level filtering. Exact interaction with per-item `<value>` routing in types.xml is not fully confirmed.

---

### `<dispatch dechance="...">` / `<proxy type="..." pos="..." rpy="..." dechance="...">`

Defines proxy objects the group can export — e.g. a wreck or prop attached to the building's footprint — and the "dechance" (de-spawn/decay chance) associated with them.

| Attribute | What it controls |
|---|---|
| `dechance` (on `<dispatch>`) | Overall chance modifier for the dispatch block |
| `type` (on `<proxy>`) | Classname of the exported proxy object |
| `pos` / `rpy` (on `<proxy>`) | Local position/rotation of the proxy relative to the group |
| `dechance` (on `<proxy>`) | Per-proxy chance value |

Point flags beyond `32`, full `dechance` formula, and export-shape meaning remain incompletely resolved — treat this section as an observed field list, not proven behavior.

---

## What Console Admins Typically Adjust

1. **`lootmax` on `<group>`** — increase overall how much loot can be in a building type.
2. **`lootmax` on `<container>`** — increase loot in a specific zone within the building.
3. **Adding `<category>` entries** — allow additional item types in a container.
4. **Adding `<usage>` to a group** — make a building eligible for an additional loot channel.

---

## The Risk of Mass Editing

This is a high-risk file for mass edits. Raising `lootmax` across hundreds of building groups simultaneously can:
- Dramatically increase total server loot density
- Create CE overtime pressure (RPT overtime errors)
- Slow CE cycles and degrade server performance

**Safe pattern:** Identify specific building types you want more loot in, raise their individual caps, test, then expand.

---

## How mapgrouppos.xml Connects

`mapgroupproto.xml` defines the *blueprint*. `mapgrouppos.xml` places *instances* of those blueprints at real world positions. A building type that has a group definition here but no entries in mapgrouppos.xml will not have active loot points placed in the world.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Group `lootmax` lower than sum of container `lootmax` values | Containers compete for the limited total slots |
| Category/usage names not in cfglimitsdefinition.xml | Filter is invalid, items may not route correctly |
| `point pos` Y value too high | Items float above visible surfaces |
| `point pos` Y value negative | Items buried in the floor |
| Raising lootmax on all groups in one edit | CE overtime pressure, server performance issues |
| Building classname in `name=` wrong | Profile never applies to any building in the world |
