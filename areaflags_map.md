---
title: "areaflags.map"
nav_order: 4
parent: "Map Loot System"
---

# areaflags.map

## Purpose

`areaflags.map` is a binary map file that paints geographic zone classifications across the terrain — which areas are Military, Industrial, Town, Village, Coast, etc. These zone flags are what allow the CE to route items from `types.xml` usage channels to the correct buildings in the correct map areas.

This file is **not a text file** and cannot be edited in a text editor. It is edited using **DayZ Tools** (available free on Steam) and can then be uploaded to your Nitrado server to change loot zone definitions.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/areaflags.map    (Xbox)
dayzps_missions/dayzOffline.<mapname>/areaflags.map    (PlayStation)
```

---

## What It Does

The usage channels in `types.xml` (`Military`, `Town`, `Village`, etc.) need a way to know which parts of the map correspond to which context. `areaflags.map` provides that geographic context by painting the map with zone type overlays.

When CE tries to spawn an item with `<usage name="Military"/>`, it uses areaflags.map to identify buildings that sit inside Military-flagged zones. Combined with mapgroupproto.xml's `<usage>` filters on building containers, this completes the routing chain.

---

## How the Zone Chain Works

```
types.xml: <usage name="Military"/>
    ↓
areaflags.map: which map coordinates are tagged as Military
    ↓
mapgroupproto.xml: which buildings in those zones have Military container filtering
    ↓
Result: item spawns in military buildings within military zones
```

All three must align for items to appear where expected. If areaflags.map doesn't tag an area as Military, items with Military usage won't appear there no matter how high their nominal is in types.xml.

---

## Editing With DayZ Tools

**DayZ Tools** is a free tool available on Steam (search "DayZ Tools"). It includes **Terrain Builder** which can open and repaint `areaflags.map` files.

### General Workflow

1. **Download** `areaflags.map` from your Nitrado server via the file browser.
2. **Open DayZ Tools** → Terrain Builder (or the Area Flags editor component).
3. **Load the map** for your server's terrain (chernarusplus, enoch, or sakhal).
4. **Import** the downloaded `areaflags.map` file.
5. **Repaint zones** — select a zone type (Military, Town, Village, etc.) and paint over the map areas you want to reclassify.
6. **Export** the modified `areaflags.map`.
7. **Upload** the exported file back to your Nitrado server at the exact original path.
8. **Restart** the server for the changes to take effect.

---

## Zone Types You Can Paint

| Zone | Effect on loot routing |
|---|---|
| `Military` | Buildings here accept Military usage items |
| `Police` | Buildings here accept Police usage items |
| `Medic` | Buildings here accept Medic usage items |
| `Firefighter` | Buildings here accept Firefighter usage items |
| `Industrial` | Buildings here accept Industrial usage items |
| `Farm` | Buildings here accept Farm usage items |
| `Coast` | Buildings here accept Coast usage items |
| `Town` | Buildings here accept Town usage items |
| `Village` | Buildings here accept Village usage items |
| `Hunting` | Buildings here accept Hunting usage items |
| `Office` | Buildings here accept Office usage items |
| `School` | Buildings here accept School usage items |
| `Prison` | Buildings here accept Prison usage items |

These zone names correspond directly to the `<usage>` names in `cfglimitsdefinition.xml` and types.xml.

---

## Why You Would Repaint areaflags.map

1. **Expand a military zone** — make a larger area around an airfield or base count as Military so more buildings there spawn military loot.
2. **Reclassify a building area** — a cluster of buildings that the game marks as Town but you want to spawn Medical or Industrial loot.
3. **Create a custom zone** — mark a previously unclassified area so it participates in a specific loot channel.
4. **Shrink an overpowered zone** — reduce the footprint of a Military zone to make military loot more concentrated and less spread out.

---

## Important Considerations

- Changes to areaflags.map affect which buildings are eligible for which loot types — but the building also needs the matching `<usage>` in mapgroupproto.xml's container definitions. Painting a zone Military doesn't automatically make every building in it spawn military loot — the building's proto group must also accept Military usage.
- The base game's areaflags.map is maintained by Bohemia Interactive and reflects their intended zone layout. You are overriding it when you upload a repainted version.
- Keep a backup of the original before uploading your edited version.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Uploading to wrong path | File not found by engine, falls back to default or errors |
| Painting a zone but not updating mapgroupproto.xml to match | Zone is reclassified but buildings still have old usage filters — loot routing unchanged |
| Very large Military zone paintings | Military loot becomes available in too many buildings, dilutes economy |
| Not restarting after upload | Old areaflags.map still in use until restart |
