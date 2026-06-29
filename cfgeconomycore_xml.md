---
title: "cfgeconomycore.xml"
nav_order: 9
parent: "Mission Root Config"
---

# cfgeconomycore.xml

## Purpose

`cfgeconomycore.xml` is the Central Economy module configuration file. It tells the CE engine what entity classes it manages, sets global CE defaults, and registers any custom CE files that should be loaded alongside the standard db/ files.

On console servers this file is almost never edited directly. It is a framework-level file. Understanding it is valuable for diagnosing CE behavior and for advanced setups involving custom CE file loading.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgeconomycore.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgeconomycore.xml    (PlayStation)
```

---

## Full Block Structure

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<economycore>

  <classes>
    <rootclass name="Loot"      act="none"      reportMemoryLOD="no"  />
    <rootclass name="Animal"    act="character"  reportMemoryLOD="yes" />
    <rootclass name="ZombieBase" act="character" reportMemoryLOD="no"  />
    <rootclass name="Car"       act="car"        reportMemoryLOD="yes" />
  </classes>

  <defaults>
    <default name="world_segments"    value="12"    />
    <default name="backup_period"     value="60"    />
    <default name="backup_count"      value="12"    />
    <default name="backup_startup"    value="false" />
    <default name="dyn_radius"        value="20"    />
    <default name="dyn_smin"          value="0"     />
    <default name="dyn_smax"          value="0"     />
    <default name="dyn_dmin"          value="0"     />
    <default name="dyn_dmax"          value="0"     />
    <default name="save_events_startup"       value="true"  />
    <default name="save_types_startup"        value="true"  />
    <default name="log_hivewarning"           value="true"  />
    <default name="log_storageinfo"           value="false" />
    <default name="log_missionfilewarning"    value="true"  />
  </defaults>

  <!-- Register a custom types file from a mod -->
  <ce folder="custom">
    <file name="mytypes.xml" type="types" />
    <file name="myevents.xml" type="events" />
  </ce>

</economycore>
```

---

## Section Reference

### `<classes>` / `<rootclass>`

Defines the root entity classes the CE engine manages.

| Attribute | What it controls |
|---|---|
| `name` | Engine class name |
| `act` | Entity type: `none` (loot), `character` (AI), `car` (vehicle) |
| `reportMemoryLOD` | Whether to log warnings about missing memory LODs (`yes`/`no`) |

**Do not edit this section** unless you are adding a new root class for a custom entity type. Incorrect edits here can break CE initialization entirely.

---

### `<defaults>` / `<default>`

CE global defaults. These set baseline values for CE behavior.

| Variable | Type | Default | What it controls |
|---|---|---|---|
| `world_segments` | Integer | 12 | How many segments the world is split into for CE processing. Affects performance on large maps. |
| `backup_period` | Integer (minutes) | 60 | How often CE creates automatic world backups |
| `backup_count` | Integer | 12 | How many backups to retain |
| `backup_startup` | Boolean | false | Whether to create a backup when the server starts |
| `dyn_radius` | Float (meters) | 20 | Default radius for dynamic infected zones |
| `dyn_smin` | Float | 0 | Default static minimum count for dynamic infected zones |
| `dyn_smax` | Float | 0 | Default static maximum count for dynamic infected zones |
| `dyn_dmin` | Float | 0 | Default dynamic minimum count for dynamic infected zones |
| `dyn_dmax` | Float | 0 | Default dynamic maximum count for dynamic infected zones |
| `save_events_startup` | Boolean | true | Whether to create `data/events.bin` on startup |
| `save_types_startup` | Boolean | true | Whether to create `data/types.bin` on startup |
| `log_hivewarning` | Boolean | true | Log hive warning messages |
| `log_storageinfo` | Boolean | false | Log periodic storage info messages |
| `log_missionfilewarning` | Boolean | true | Log warnings about mission files |
| `log_ce_loop` | Boolean | false | Log CE loop timing |
| `log_ce_dynamicevent` | Boolean | false | Log dynamic event CE detail |
| `log_ce_lootspawn` | Boolean | false | Log loot spawn detail |
| `log_ce_lootcleanup` | Boolean | false | Log loot cleanup detail |
| `log_ce_lootrespawn` | Boolean | false | Log loot respawn detail |
| `log_ce_zombie` | Boolean | false | Log infected CE detail |
| `log_ce_animal` | Boolean | false | Log animal CE detail |

**Diagnostics use:** Enabling CE logging flags (`log_ce_lootrespawn`, `log_ce_lootspawn`) generates detailed RPT output useful for diagnosing CE overtime and spawn issues. Disable after diagnostics — they create large log files.

---

### `<ce folder="...">` / `<file>`

Registers additional CE files beyond the default db/ files. This is how custom types, events, or globals from a subfolder are loaded by CE.

```xml
<ce folder="custom">
  <file name="mod_types.xml" type="types" />
</ce>
```

| Attribute | What it controls |
|---|---|
| `folder` | Subfolder path relative to mission root |
| `name` | Filename within that folder |
| `type` | What CE file type to treat it as: `types`, `events`, `globals`, `messages`, `spawnabletypes`, `economy` |

**Console use case:** If you have a custom mod adding items, its types.xml can be registered here as an additional types file rather than merging it into the main types.xml.

---

## What Console Admins Typically Adjust

1. **`backup_period`** — change how often automatic world backups are created.
2. **`backup_count`** — change how many backups are retained.
3. **`log_ce_lootrespawn` / `log_ce_lootspawn`** — temporarily enable for CE diagnostics.
4. **`<ce folder="...">` / `<file>`** — register custom mod CE files.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Editing `<classes>` incorrectly | CE initialization failure |
| Wrong `type=` in a `<file>` registration | Custom file loaded as wrong CE type |
| Wrong `folder=` path | Custom file not found, silently skipped |
| Enabling all log flags permanently | RPT log files grow extremely large, performance impact |
