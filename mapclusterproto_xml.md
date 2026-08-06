---
title: "mapclusterproto.xml"
nav_order: 4
parent: "Map Loot System"
---

# mapclusterproto.xml

## Purpose

`mapclusterproto.xml` defines the prototype rules for cluster groups referenced by `mapgroupcluster*.xml` — the blueprint that governs how a named cluster behaves, in the same relationship that `mapgroupproto.xml` has to `mapgrouppos.xml`.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/mapclusterproto.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/mapclusterproto.xml    (PlayStation)
```

## Connects To

- **mapgroupcluster\*.xml** — `<group name="...">` records there select a cluster prototype defined here

---

## Full Block Structure

The confirmed root is `<prototype>`, containing named cluster definitions with container/point records:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<prototype>

  <cluster name="Chernogorsk_Center" lootmax="8" maxinstances="3">
    <container>
      <point pos="1.2 0.4 -2.1" />
      <point pos="-0.8 0.4 1.6" />
    </container>
    <de />
  </cluster>

</prototype>
```

---

## Field-by-Field Reference

### `<cluster name="..." lootmax="..." maxinstances="...">`

One cluster prototype definition.

| Attribute | What it controls |
|---|---|
| `name` | Cluster identity — referenced by `mapgroupcluster*.xml` group records |
| `lootmax` | Loot cap for the cluster |
| `maxinstances` | Maximum simultaneous instances of this cluster |

### `<container>` / `<point pos="..." />`

Container and point records defining spawn positions within the cluster, structurally similar to the container/point pattern in `mapgroupproto.xml`.

### `<de />`

A cluster export marker. Its exact runtime effect on cluster export/dispatch is not fully confirmed.

---

## What Console Admins Typically Adjust

This file is not manually edited in standard console server operations.

---

## Confidence Note

The `<prototype>` root, cluster `name`/`lootmax`/`maxinstances` attributes, and container/point records are confirmed against the installed/native-linked shape. The exact behavior of `<de />` and full cluster export semantics are not fully resolved — treat those as observed fields, not proven behavior.
