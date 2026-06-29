---
title: "cfgignorelist.xml"
nav_order: 10
parent: "Mission Root Config"
---

# cfgignorelist.xml

## Purpose

`cfgignorelist.xml` is a blocklist of item classnames that the Central Economy completely ignores. Items on this list are excluded from CE tracking, spawning, and counting — even if they have entries in types.xml.

This file is used to prevent CE from managing items that should not be part of the natural loot economy — typically crafted items, quest items, or items that exist in the game files but should never naturally spawn.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgignorelist.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgignorelist.xml    (PlayStation)
```

---

## Full Block Structure

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ignore>
  <type name="RoadFlare_ColorBase" />
  <type name="Inventory_Base" />
  <type name="CivilianSedan_Base" />
  <type name="BaseBuildingBase" />
  <type name="TentBase" />
</ignore>
```

---

## Field-by-Field Reference

### `<type name="...">`

One item classname to exclude from CE management.

- `name` — exact classname, case-sensitive
- Names must be unique — duplicates cause a validation error

The CE will not initialize, spawn, track, count, or manage items on this list. They cannot appear in the world through normal CE loot spawning.

---

## What This File Is Used For

### Base class entries
Many entries in the default ignore list are abstract base classes — parent classes that concrete items inherit from. Since you never want to spawn the abstract base itself (only its children), they go here.

```xml
<type name="BaseBuildingBase" />   <!-- parent of all base-building objects -->
<type name="TentBase" />           <!-- parent of all tent types -->
```

### Items that should never spawn naturally
Some items exist in the game files for crafting, quests, or other systems but should not appear as world loot.

---

## What Console Admins Typically Adjust

Rarely edited in normal console server operations. The main reason to edit it:

1. **Adding a classname** — prevent a specific item from ever spawning naturally (e.g. a base class added by a mod that would otherwise generate errors).
2. **Removing a classname** — allow an item that was previously blocked to re-enter the economy (rare, usually wrong approach).

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Adding a concrete item classname you still want in the economy | That item will never spawn naturally even with high nominal in types.xml |
| Duplicate classnames | File validation error |
| Wrong classname (typo) | Entry is effectively ignored — no effect, no error |
