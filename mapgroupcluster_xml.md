---
title: "mapgroupcluster.xml"
nav_order: 3
parent: "Map Loot System"
---

# mapgroupcluster.xml / mapgroupcluster01-05.xml

## Purpose

The `mapgroupcluster*.xml` files are a flat map of group records, plus include links that split the full map's cluster data across multiple files. They are not a nested geographic grouping of `mapgrouppos.xml` buildings — each `<group>` record here is itself a placement entry, in the same shape as `mapgrouppos.xml`, just organized into these split files.

There are multiple cluster files (`mapgroupcluster.xml`, `mapgroupcluster01.xml` through `mapgroupcluster05.xml`) that together cover the full map. An installed mission spans six such files (225,016 split group records total) plus six `<include>` records tying them together.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/mapgroupcluster.xml
dayzxb_missions/dayzOffline.<mapname>/mapgroupcluster01.xml
...
dayzxb_missions/dayzOffline.<mapname>/mapgroupcluster05.xml
```

## Connects To

- **mapgroupproto.xml** — `name` values reference prototype/group names defined there
- **mapclusterproto.xml** — cluster prototypes define cluster-level behavior rules

---

## Full Block Structure

The installed root is `<map>`, with flat `<group>` records carrying `name`, `pos`, and `a` attributes directly — not a nested `<group><map><mapgrouppos>` hierarchy:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<map>

  <group name="Land_PoliceStation_Small" pos="3421.50 0.00 7832.10" a="52.3" />
  <group name="Land_House_1W01" pos="5102.25 0.00 4671.80" a="127.0" />

  <include file="mapgroupcluster01.xml" />

</map>
```

**This is a structural correction, not a cosmetic one.** The previous nested `<mapgroupcluster><group><map><mapgrouppos name pos /></map></group>` shape, and the claim that these files represent geographic groupings of buildings, were not confirmed against the installed file and have been removed.

---

## Field-by-Field Reference

### `<group name="..." pos="..." a="..." />`

One placement record, structurally identical in shape to a `mapgrouppos.xml` entry.

| Attribute | What it controls |
|---|---|
| `name` | Prototype/group name — must match mapgroupproto.xml |
| `pos` | World position ("X Y Z") |
| `a` | Angle (yaw rotation) in degrees |

---

### `<include file="..." />`

Pulls in another cluster file's `<group>` records as part of the same logical set. Used to split the full map's cluster data across `mapgroupcluster.xml` and `mapgroupcluster01.xml`–`mapgroupcluster05.xml`.

---

## What Console Admins Typically Adjust

These files are almost never manually edited on console servers. They are generated for each map and represent the full split set of cluster group placements.

---

## mapclusterproto.xml

`mapclusterproto.xml` defines the prototype rules for cluster behavior. See [mapclusterproto.xml](mapclusterproto_xml.md) for its confirmed field shape.

**Console Path:**
```
dayzxb_missions/dayzOffline.<mapname>/mapclusterproto.xml
```

This file is not manually edited in standard console server operations.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| `name` in a `<group>` record not defined in mapgroupproto.xml | Reference fails, group excluded |
| Broken `<include file="...">` reference | That split file's records are not loaded |

---

## Confidence Note

The flat `<map>`/`<group pos a>`/`<include file>` structure is confirmed against an installed mission (six cluster files, 225,016 split group records, six include records). The exact runtime purpose of the split (performance, editing convenience, or something else) is not confirmed.
