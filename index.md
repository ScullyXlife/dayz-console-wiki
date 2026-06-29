---
title: Home
nav_order: 1
---

# DayZ Console Server Wiki

Complete field-level reference for every user-editable DayZ server configuration file — Xbox and PlayStation.

---

## What's Here

This wiki documents each config file: what it does, every field explained, how it connects to other files, and what silently breaks things when it's wrong.

| Section | Files |
|---|---|
| [Overview & File Relationships](00_overview_and_file_relationships.md) | Directory structure, how the chains connect, golden rules |
| [Core Economy — db/](core-economy.md) | types.xml, economy.xml, events.xml, globals.xml, messages.xml |
| [Mission Root Config](mission-config.md) | cfgspawnabletypes, cfggameplay, cfgweather, cfglimitsdefinition, and more |
| [Map Loot System](map-loot.md) | mapgroupproto, mapgrouppos, mapgroupcluster, areaflags.map |
| [Territory System](territory-system.md) | env/ territory files for animals and zombies |

---

## Quick-Start Rules

1. Download the live file before touching it.
2. Save a backup with a date stamp (`types.xml.2026-06-28.bak`).
3. Change one system at a time — economy one pass, events another.
4. Upload to the exact original path.
5. Re-download after uploading to confirm your edits are present before restarting.
6. When something breaks, roll back and diff.

---

## Console Directory

```
dayzxb_missions/dayzOffline.<mapname>/     ← MISSION ROOT (Xbox)
dayzps_missions/dayzOffline.<mapname>/     ← MISSION ROOT (PlayStation)

    db/           types.xml  events.xml  globals.xml  messages.xml  economy.xml
    env/          *_territories.xml
    custom/       loadouts, object spawners
    (root)        cfgspawnabletypes.xml  cfgeventspawns.xml  cfgeventgroups.xml
                  cfglimitsdefinition.xml  cfggameplay.json  cfgweather.xml
                  cfgplayerspawnpoints.xml  cfgenvironment.xml  cfgeconomycore.xml
                  cfgignorelist.xml  cfgrandompresets.xml  cfgundergroundtriggers.json
                  cfgEffectArea.json  mapgroupproto.xml  mapgrouppos.xml
                  mapgroupcluster*.xml  areaflags.map
```

Map names: `chernarusplus` · `enoch` (Livonia) · `sakhal`
