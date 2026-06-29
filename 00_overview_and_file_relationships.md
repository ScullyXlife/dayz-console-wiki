---
title: "Overview & File Relationships"
nav_order: 2
---

# DayZ Console Server File Encyclopedia

## What This Is

This encyclopedia documents every user-editable DayZ server configuration file for console (Xbox / PlayStation). Each article covers what the file does, every field explained, what you normally change, how it connects to other files, and what breaks things.

---

## Console Directory Structure

```
dayzxb/                                         ← server root (Xbox)
dayzxb_missions/
└── dayzOffline.<mapname>/                      ← MISSION ROOT
    │
    ├── db/                                     ← 5 FILES ONLY
    │   ├── economy.xml
    │   ├── events.xml
    │   ├── globals.xml
    │   ├── messages.xml
    │   └── types.xml
    │
    ├── env/                                    ← TERRITORY FILES
    │   ├── bear_territories.xml
    │   ├── cattle_territories.xml
    │   ├── hare_territories.xml
    │   ├── hen_territories.xml
    │   ├── pig_territories.xml
    │   ├── red_deer_territories.xml
    │   ├── roe_deer_territories.xml
    │   ├── sheep_goat_territories.xml
    │   ├── wild_boar_territories.xml
    │   ├── wolf_territories.xml
    │   └── zombie_territories.xml
    │
    ├── custom/                                 ← USER FILES (loadouts, object spawners)
    │
    ├── areaflags.map
    ├── cfgeconomycore.xml
    ├── cfgEffectArea.json
    ├── cfgenvironment.xml
    ├── cfgeventgroups.xml
    ├── cfgeventspawns.xml
    ├── cfggameplay.json
    ├── cfgignorelist.xml
    ├── cfglimitsdefinition.xml
    ├── cfglimitsdefinitionuser.xml
    ├── cfgplayerspawnpoints.xml
    ├── cfgrandompresets.xml
    ├── cfgspawnabletypes.xml
    ├── cfgundergroundtriggers.json
    ├── cfgweather.xml
    ├── mapclusterproto.xml
    ├── mapgroupcluster.xml
    ├── mapgroupcluster01.xml
    ├── mapgroupcluster02.xml
    ├── mapgroupcluster03.xml
    ├── mapgroupcluster04.xml
    ├── mapgroupcluster05.xml
    ├── mapgrouppos.xml
    └── mapgroupproto.xml

restart.log
```

Map name options: `chernarusplus` · `enoch` (Livonia) · `sakhal`  
PlayStation uses `dayzps_missions/` instead of `dayzxb_missions/`

---

## How the Files Talk to Each Other

### The Economy Chain

```
cfglimitsdefinition.xml  +  cfglimitsdefinitionuser.xml
    ↓ defines the shared vocabulary: categories, usage names, tier names, tags
    ↓
types.xml  (db/)                        cfgspawnabletypes.xml
    ↓ what items exist, how many,           ↓ what spawns WITH each item
    ↓ how long they last, where they go     ↓ (attachments, cargo, damage range)
    ↓ uses: category, usage, value
    ↓
mapgroupproto.xml
    ↓ which building spots accept which loot
    ↓ uses: category, tag, usage to filter
    ↓
economy.xml  +  cfgeconomycore.xml  (db/)
    ↓ CE module config and core settings
    ↓
globals.xml  (db/)
    ↓ hard caps: max zombies, max animals, cleanup timers, loot damage ranges
```

If any name in types.xml (category, usage, value) doesn't match cfglimitsdefinition.xml, that item either doesn't spawn or spawns in wrong locations. Silently.

### The Event Chain

```
events.xml  (db/)
    ↓ event scheduler rules (how many, how long, spacing)
    ↓
cfgeventspawns.xml  (mission root)
    ↓ coordinate pools — where events can physically appear on the map
    ↓
cfgeventgroups.xml  (mission root)
    ↓ what objects and loot compose the event site
    ↓
types.xml  (db/)
    ↓ items used by event groups must exist here
```

An event with no coordinates in cfgeventspawns.xml cannot place. An event group referencing an item not in types.xml silently fails to spawn that item.

### The Territory Chain

```
cfgenvironment.xml  (mission root)
    ↓ wires territory files to the engine — references file paths
    ↓
env/*_territories.xml
    ↓ defines map zones where AI populations spawn
    ↓
types.xml / events.xml
    ↓ creature classnames must exist and be valid
```

A wrong file path in cfgenvironment.xml silently disables that entire territory category.

### The Map Loot Chain

```
mapgroupproto.xml  (mission root)
    ↓ defines loot slots inside specific buildings
    ↓
mapgrouppos.xml  (mission root)
    ↓ places those building group definitions at world positions
    ↓
mapgroupcluster*.xml  (mission root)
    ↓ cluster grouping of loot positions
    ↓
mapclusterproto.xml  (mission root)
    ↓ cluster prototype definitions
```

---

## Golden Rules Before Editing Any File

1. Download the live file before touching it.
2. Save a backup with a date stamp (`types.xml.2026-06-28.bak`).
3. Change one system at a time. Economy one pass. Events another.
4. Upload to the exact original path.
5. Re-download after uploading to confirm your edits are present before restarting.
6. When something breaks, roll back and compare the diff.

---

## File Index

### db/ — Core Economy (5 files)

| File | What It Controls |
|---|---|
| [types.xml](01_types_xml.md) | Item counts, lifetimes, location routing |
| [events.xml](03_events_xml.md) | Dynamic event scheduler rules |
| [globals.xml](06_globals_xml.md) | Global population caps, cleanup timers, loot damage |
| [messages.xml](09_messages_xml.md) | Automated server broadcast messages |
| [economy.xml](economy_xml.md) | CE core runtime settings |

### Mission Root — Config Files

| File | What It Controls |
|---|---|
| [cfgspawnabletypes.xml](02_cfgspawnabletypes_xml.md) | Attachments, cargo, item condition on spawn |
| [cfgeventspawns.xml](04_cfgeventspawns_xml.md) | Event spawn coordinate pools |
| [cfgeventgroups.xml](05_cfgeventgroups_xml.md) | Event object compositions |
| [cfglimitsdefinition.xml](08_cfglimitsdefinition_xml.md) | Shared vocabulary: categories, tags, usage, tiers |
| [cfglimitsdefinitionuser.xml](08_cfglimitsdefinition_xml.md) | User-added extensions to the limits vocabulary |
| [cfggameplay.json](10_cfggameplay_json.md) | All gameplay rules and toggles |
| [cfgweather.xml](11_cfgweather_xml.md) | Weather cycle and phenomena |
| [cfgplayerspawnpoints.xml](12_cfgplayerspawnpoints_xml.md) | Player spawn locations |
| [cfgenvironment.xml](13_cfgenvironment_xml.md) | Territory file wiring |
| [cfgundergroundtriggers.json](15_cfgundergroundtriggers_json.md) | Underground zone triggers |
| [cfgEffectArea.json](16_cfgEffectArea_json.md) | Contaminated/effect zones |
| [cfgignorelist.xml](cfgignorelist_xml.md) | Items excluded from economy |
| [cfgrandompresets.xml](17_cfgrandompresets_xml.md) | Randomized spawn presets |
| [mapgroupproto.xml](07_mapgroupproto_xml.md) | Building loot point blueprints |
| [mapgrouppos.xml](mapgrouppos_xml.md) | Building group world positions |
| [mapgroupcluster*.xml](mapgroupcluster_xml.md) | Loot cluster groupings |
| [mapclusterproto.xml](mapclusterproto_xml.md) | Cluster prototype definitions |
| [cfgeconomycore.xml](cfgeconomycore_xml.md) | CE module load configuration |
| [areaflags.map](areaflags_map.md) | Zone flag map (military, industrial, etc.) |

### env/ — Territory Files

| File | What It Controls |
|---|---|
| [*_territories.xml](14_territories_xml.md) | AI zone definitions per creature type |

### custom/ — User Files

| File | What It Controls |
|---|---|
| Loadout JSONs | Player spawn gear presets |
| Object spawner JSONs | Persistent world object placement |
