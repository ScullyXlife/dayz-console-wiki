---
title: "mapgroupcluster.xml"
nav_order: 3
parent: "Map Loot System"
---

# mapgroupcluster.xml / mapgroupcluster01-05.xml

## Purpose

The `mapgroupcluster*.xml` files group building loot positions from `mapgrouppos.xml` into geographic clusters. Clustering allows the CE to manage loot across a geographic area as a coordinated group rather than individual disconnected buildings.

There are multiple cluster files (`mapgroupcluster.xml`, `mapgroupcluster01.xml` through `mapgroupcluster05.xml`) that together cover the full map, organized by geographic area.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/mapgroupcluster.xml
dayzxb_missions/dayzOffline.<mapname>/mapgroupcluster01.xml
...
dayzxb_missions/dayzOffline.<mapname>/mapgroupcluster05.xml
```

## Connects To

- **mapgrouppos.xml** — clusters reference the building position groups defined there
- **mapclusterproto.xml** — cluster prototypes define the cluster behavior rules

---

## Full Block Structure

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<mapgroupcluster>

  <group name="Chernogorsk_Center">
    <map>
      <mapgrouppos name="Land_PoliceStation_Small" pos="3421.50 0.00 7832.10" />
      <mapgrouppos name="Land_House_1W01" pos="5102.25 0.00 4671.80" />
      <mapgrouppos name="Land_Store_Grocery" pos="5110.00 0.00 4680.50" />
    </map>
  </group>

  <group name="NW_Airfield_Complex">
    <map>
      <mapgrouppos name="Land_Mil_Barracks_i" pos="6234.10 0.00 3901.45" />
      <mapgrouppos name="Land_Mil_Barracks_i" pos="6245.00 0.00 3912.00" />
    </map>
  </group>

</mapgroupcluster>
```

---

## Field-by-Field Reference

### `<group name="...">`

A named cluster of building positions that the CE manages together as a geographic group.

- `name` — identifier for this cluster, used internally by CE

---

### `<mapgrouppos name="..." pos="..." />`

References a specific building instance by its proto name and world position. Must match an entry in mapgrouppos.xml.

| Attribute | What it controls |
|---|---|
| `name` | Proto group name — must match mapgroupproto.xml |
| `pos` | World position — must match the position in mapgrouppos.xml |

---

## What Console Admins Typically Adjust

These files are almost never manually edited on console servers. They are auto-generated for each map and represent the full geographic grouping of all loot positions.

The main scenario where you would touch these files is when adding new buildings to the map loot system — new buildings added to mapgrouppos.xml would need to be added to the appropriate cluster file to be fully integrated into CE geographic management.

---

## mapclusterproto.xml

`mapclusterproto.xml` defines the prototype rules for cluster behavior — essentially the blueprint that governs how cluster groups operate within CE. Like mapgroupproto.xml is to mapgrouppos.xml, mapclusterproto.xml is to the cluster files.

**Console Path:**
```
dayzxb_missions/dayzOffline.<mapname>/mapclusterproto.xml
```

This file is not manually edited in standard console server operations.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Building in mapgrouppos.xml not added to any cluster file | Building may not be managed in CE geographic grouping |
| Position mismatch between cluster file and mapgrouppos.xml | Reference fails, building excluded from cluster |
