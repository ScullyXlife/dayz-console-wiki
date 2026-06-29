---
title: "economy.xml"
nav_order: 2
parent: "Core Economy — db/"
---

# economy.xml

## Purpose

`economy.xml` is a master toggle file for the Central Economy (CE). It controls whether each major entity group is initialized, loaded from storage, saved to storage, and respawned at server startup. Think of it as a series of on/off switches for the CE subsystems.

On console servers this file is rarely edited. The defaults work correctly for normal operation. It becomes relevant when diagnosing persistent world issues or when intentionally resetting specific entity groups.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/db/economy.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/db/economy.xml    (PlayStation)
```

---

## Full Block Structure

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<economy>
  <dynamic  init="1" load="1" respawn="1" save="1"/>
  <animals  init="1" load="0" respawn="1" save="0"/>
  <zombies  init="1" load="0" respawn="1" save="0"/>
  <vehicles init="1" load="1" respawn="1" save="1"/>
  <randoms  init="0" load="0" respawn="1" save="0"/>
  <custom   init="0" load="0" respawn="0" save="0"/>
  <building init="1" load="1" respawn="0" save="1"/>
  <player   init="1" load="1" respawn="1" save="1"/>
</economy>
```

---

## What the Vanilla Defaults Tell You

Reading the vanilla defaults reveals key design decisions about how Bohemia intends the CE to work:

- **Animals and zombies do not persist between restarts** (`load="0"`, `save="0"`) — their populations rebuild fresh every restart.
- **Building loot persists but does not respawn on startup** (`load="1"`, `respawn="0"`) — looted buildings stay looted at restart, CE gradually refills them during runtime.
- **Dynamic events and vehicles fully persist** (`load="1"`, `save="1"`) — their world state carries across restarts.
- **Randoms and custom are disabled by default** (`init="0"`) — not active unless explicitly enabled.

---

## Entity Groups

| Element | What it covers |
|---|---|
| `dynamic` | Dynamic events (vehicle wrecks, heli crashes, item events) |
| `animals` | Animal population (deer, wolves, bears, etc.) |
| `zombies` | Infected (zombie) population |
| `vehicles` | Spawned vehicles |
| `randoms` | Random loot spawns |
| `custom` | Custom CE entities |
| `building` | Loot inside buildings |
| `player` | Player-related CE data |

---

## Attributes on Each Line

Every entity group element has four boolean attributes:

### `init`
**Type:** Boolean (true/false)  
Whether the CE initializes this entity group at server startup.

- `true` — group is active and managed by CE
- `false` — CE ignores this group entirely at startup. That group of entities will not exist.

---

### `load`
**Type:** Boolean  
Whether CE loads the previously saved state of this group from storage on startup.

- `true` — server continues from last saved state (persistent world)
- `false` — group starts fresh each restart (no persistence for this group)

---

### `respawn`
**Type:** Boolean  
Whether CE respawns entities from this group at startup.

- `true` — entities in this group are respawned at server start
- `false` — entities are not auto-respawned at startup (they must build up naturally)

Note: `player` has `respawn="false"` by default — players are not respawned by CE.

---

### `save`
**Type:** Boolean  
Whether CE saves the state of this group to storage periodically and on shutdown.

- `true` — state is persisted between restarts
- `false` — state is not saved — all entities in this group are gone on next restart

---

## Common Use Cases

### Wiping loot on next restart
Set `load="false"` on `building` temporarily. On next restart, building loot starts fresh instead of loading from stored state. Set back to `true` after the wipe restart.

### Wiping animals on next restart
Set `load="false"` on `animals`. Animal population starts from scratch.

### Disabling vehicle CE entirely
Set `init="false"` on `vehicles`. No vehicle spawning at all.

---

## What Console Admins Typically Adjust

This file is almost never edited in normal operation. The defaults are correct for a standard server. The most common reason to touch it is a deliberate wipe of one entity group without doing a full server wipe.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Setting `save="false"` on `building` permanently | Loot resets completely every restart |
| Setting `init="false"` on `zombies` | No infected spawn anywhere on the server |
| Leaving `load="false"` after a wipe restart | Entity group keeps wiping every restart |
