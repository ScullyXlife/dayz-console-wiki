---
title: "cfglimitsdefinition.xml"
nav_order: 4
parent: "Mission Root Config"
---

# cfglimitsdefinition.xml and cfglimitsdefinitionuser.xml

## Purpose

These two files are the shared vocabulary for the entire economy system. They define what names are valid for categories, placement tags, usage channels, and tier values. Every reference in `types.xml` and `mapgroupproto.xml` to a category, usage, tag, or value must match an entry here or the reference is silently ignored.

- `cfglimitsdefinition.xml` — the base definition file provided by the game. Do not delete entries from this file.
- `cfglimitsdefinitionuser.xml` — the user-extension file. Add your custom categories, usages, or tags here.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfglimitsdefinition.xml        (Xbox)
dayzxb_missions/dayzOffline.<mapname>/cfglimitsdefinitionuser.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfglimitsdefinition.xml        (PlayStation)
dayzps_missions/dayzOffline.<mapname>/cfglimitsdefinitionuser.xml    (PlayStation)
```

## Connects To

- **types.xml (db/)** — `<category>`, `<usage>`, and `<value>` references must exist here
- **mapgroupproto.xml** — `<category>`, `<tag>`, and `<usage>` on loot containers must match entries here

---

## Full Block Structure

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<lists>

  <categories>
    <category name="tools"/>
    <category name="containers"/>
    <category name="clothes"/>
    <category name="food"/>
    <category name="weapons"/>
    <category name="books"/>
    <category name="explosives"/>
    <category name="lootdispatch"/>
  </categories>

  <tags>
    <tag name="floor"/>
    <tag name="shelves"/>
    <tag name="ground"/>
  </tags>

  <usageflags>
    <usage name="Military"/>
    <usage name="Police"/>
    <usage name="Medic"/>
    <usage name="Firefighter"/>
    <usage name="Industrial"/>
    <usage name="Farm"/>
    <usage name="Coast"/>
    <usage name="Town"/>
    <usage name="Village"/>
    <usage name="Hunting"/>
    <usage name="Office"/>
    <usage name="School"/>
    <usage name="Prison"/>
    <usage name="Lunapark"/>
    <usage name="SeasonalEvent"/>
    <usage name="ContaminatedArea"/>
    <usage name="ContaminatedArea_Dynamic"/>
    <usage name="Historical"/>
    <usage name="Special"/>
    <usage name="Underground"/>
  </usageflags>

  <valueflags>
    <value name="Tier1"/>
    <value name="Tier2"/>
    <value name="Tier3"/>
    <value name="Tier4"/>
    <value name="Unique"/>
  </valueflags>

</lists>
```

---

## Field-by-Field Reference

### `<categories>`

Defines valid item categories. Categories are the broadest item grouping — used to filter which loot containers in buildings can accept which item types.

| Category | What it typically covers |
|---|---|
| `tools` | Tools, medical, utility items |
| `containers` | Bags, boxes, storage items |
| `clothes` | Clothing and gear items |
| `food` | Food and drink items |
| `weapons` | Weapons and weapon-related items |
| `books` | Books and readable items |
| `explosives` | Grenades, mines, explosive items |
| `lootdispatch` | Special dispatch category for CE routing |

Only one category per item in types.xml.

---

### `<tags>`

Defines valid placement context tags. Tags are used by `mapgroupproto.xml` loot points to further filter what types of items can spawn at a specific physical spot within a building.

| Tag | Typical use |
|---|---|
| `floor` | Items placed on floor surfaces |
| `shelves` | Items placed on shelves/counters |
| `ground` | Items placed on ground outside |

Tags work in conjunction with categories — a loot point might accept `tools` on `shelves` but not `weapons`.

---

### `<usageflags>`

Defines the valid usage channel names. Usage is the routing system that connects items to the types of buildings they can spawn in. Every `<usage name="..."/>` in types.xml must match an entry here.

**Standard usage channels and their routing:**

| Usage | Where it routes loot |
|---|---|
| `Military` | Military bases, barracks, checkpoints, armories |
| `Police` | Police stations |
| `Medic` | Hospitals, clinics, medical tents |
| `Firefighter` | Fire stations |
| `Industrial` | Factories, warehouses, industrial buildings |
| `Farm` | Barns, farm structures |
| `Coast` | Coastal buildings, lighthouses, boat areas |
| `Town` | Town buildings — shops, houses, apartments |
| `Village` | Small village houses and structures |
| `Hunting` | Hunting cabins, tree stands, ranger posts |
| `Office` | Office buildings |
| `School` | Schools and educational buildings |
| `Prison` | Prison structures |
| `Lunapark` | Amusement park buildings |
| `SeasonalEvent` | Seasonal event content |
| `ContaminatedArea` | Static contaminated zones |
| `ContaminatedArea_Dynamic` | Dynamic contaminated zones |
| `Historical` | Historical/castle structures |
| `Special` | Special placement contexts |
| `Underground` | Underground areas, bunkers |

An item in types.xml can have multiple `<usage>` lines — it will be eligible to spawn in any of those contexts.

---

### `<valueflags>`

Defines the valid tier/value routing names. Tiers correspond to geographic loot zones on the map. Tier1 is the most accessible (coastal/starter) and Tier4 is the most restricted (deep military).

| Value | Zone |
|---|---|
| `Tier1` | Coastal/starter zones |
| `Tier2` | Inland civilian areas |
| `Tier3` | Military-adjacent / mid-tier zones |
| `Tier4` | Deep military / high-end loot zones |
| `Unique` | Single-location or highly restricted spawning |

An item can have multiple `<value>` lines in types.xml — it will be eligible in any of those tiers.

---

## cfglimitsdefinitionuser.xml

This is your extension file. Add entries here when you need custom categories, usages, or values that aren't in the base file. The engine merges both files.

**Example — adding a custom usage:**
```xml
<lists>
  <usageflags>
    <usage name="CustomMilitary"/>
    <usage name="EventOnly"/>
  </usageflags>
</lists>
```

Once added here, you can reference `<usage name="CustomMilitary"/>` in types.xml and it will be recognized.

---

## What Console Admins Typically Adjust

1. **Adding to `cfglimitsdefinitionuser.xml`** — adding new usage channels or categories for custom mod items.
2. **These files are usually left alone** — the base file covers all standard DayZ content. Only edit when introducing new vocabulary that doesn't exist yet.

---

## How This File Breaks Things (Silently)

The most dangerous aspect of these files is that mismatches fail silently:

- An item in types.xml with `<usage name="Millitary"/>` (typo) — the usage name doesn't exist in this file, so the item has no valid usage routing and will not spawn naturally.
- An item with `<value name="Tier5"/>` — Tier5 doesn't exist in valueflags, so the tier reference is invalid and the item's tier routing is broken.
- A loot point in mapgroupproto.xml with `<category name="gear"/>` — if `gear` isn't in categories, that filter is ignored or broken.

There is no in-game error. No warning. The item simply doesn't appear where expected.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Typo in a usage/category name elsewhere that doesn't match this file | Item silently doesn't spawn in expected locations |
| Removing an entry from cfglimitsdefinition.xml that types.xml still references | Broken reference, item loses that routing |
| Adding new entries to base file instead of user file | Works but makes future updates harder to track |
| Case mismatch (`military` vs `Military`) | Reference fails — these are case-sensitive |
