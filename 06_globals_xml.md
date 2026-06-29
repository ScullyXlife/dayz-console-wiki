---
title: "globals.xml"
nav_order: 4
parent: "Core Economy — db/"
---

# globals.xml

## Purpose

`globals.xml` is a flat list of runtime variables that control the server's Central Economy (CE) behavior at the macro level — population caps, cleanup timers, spawn behavior, respawn rules, and world simulation settings. It is the "ceiling and floor" file: it sets the hard limits that the rest of the economy system operates within.

Unlike most other economy files, globals.xml has no nesting or complex structure. Every setting is one line.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/db/globals.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/db/globals.xml    (PlayStation)
```

## Connects To

- **types.xml** — nominal/min values only matter up to the caps set here
- **events.xml** — zombie and animal counts tie into ZombieMaxCount and AnimalMaxCount

---

## Full Block Structure (Vanilla Defaults)

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<variables>
  <var name="AnimalMaxCount" type="0" value="200"/>
  <var name="CleanupAvoidance" type="0" value="100"/>
  <var name="CleanupLifetimeDeadAnimal" type="0" value="1200"/>
  <var name="CleanupLifetimeDeadInfected" type="0" value="330"/>
  <var name="CleanupLifetimeDeadPlayer" type="0" value="3600"/>
  <var name="CleanupLifetimeDefault" type="0" value="45"/>
  <var name="CleanupLifetimeLimit" type="0" value="50"/>
  <var name="CleanupLifetimeRuined" type="0" value="330"/>
  <var name="FlagRefreshFrequency" type="0" value="432000"/>
  <var name="FlagRefreshMaxDuration" type="0" value="3456000"/>
  <var name="FoodDecay" type="0" value="1"/>
  <var name="IdleModeCountdown" type="0" value="60"/>
  <var name="IdleModeStartup" type="0" value="1"/>
  <var name="InitialSpawn" type="0" value="100"/>
  <var name="LootDamageMax" type="1" value="0.82"/>
  <var name="LootDamageMin" type="1" value="0.0"/>
  <var name="LootProxyPlacement" type="0" value="1"/>
  <var name="LootSpawnAvoidance" type="0" value="100"/>
  <var name="RespawnAttempt" type="0" value="2"/>
  <var name="RespawnLimit" type="0" value="20"/>
  <var name="RespawnTypes" type="0" value="12"/>
  <var name="RestartSpawn" type="0" value="0"/>
  <var name="SpawnInitial" type="0" value="1200"/>
  <var name="TimeHopping" type="0" value="60"/>
  <var name="TimeLogin" type="0" value="15"/>
  <var name="TimeLogout" type="0" value="15"/>
  <var name="TimePenalty" type="0" value="20"/>
  <var name="WorldWetTempUpdate" type="0" value="1"/>
  <var name="ZombieMaxCount" type="0" value="1000"/>
  <var name="ZoneSpawnDist" type="0" value="300"/>
</variables>
```

---

## How to Read Each Line

```xml
<var name="LootDamageMin" type="1" value="0.01"/>
```

- `name` — the engine variable key. Never rename these.
- `type="0"` — whole number (integer) expected.
- `type="1"` — decimal number (float) expected. Putting an integer value in a type 1 field causes an error.
- `value` — the active setting.

**Critical rule:** Do not change `type`. Do not put a decimal in a `type="0"` field.

---

## Field-by-Field Reference

### Population Caps

---

#### `AnimalMaxCount`
**Type:** Integer  
The hard cap on how many animals (deer, boar, wolves, bears, etc.) can exist on the map simultaneously. CE will not spawn new animals once this number is reached regardless of nominal targets in types.xml or territory settings.

**Default:** 200  
**Console typical range:** 200–1500  
**Warning:** Setting this very high without matching territory capacity causes CE overtime pressure.

---

#### `ZombieMaxCount`
**Type:** Integer  
The hard cap on total infected population on the map. Works the same way as AnimalMaxCount.

**Default:** 1000  
**Console typical range:** 1000–2000  
**Note:** This cap applies globally. Even if you raise individual zombie event nominals, they cannot exceed this total.

---

#### `SpawnInitial`
**Type:** Integer  
The quantity target used during the startup spawn phase when the server first initializes the economy. Setting this too low causes an under-populated world immediately after restart.

**Default:** 1200  
**Safe guidance:** Keep in rough alignment with ZombieMaxCount + AnimalMaxCount.

---

#### `InitialSpawn`
**Type:** Integer (percentage 0–100)  
Controls how much of the economy is pre-populated during the initial startup spawn, expressed as a percentage of the SpawnInitial target.

**Default:** 100 (percentage — full startup population)  
**Note:** Lowering this causes a slower economy ramp-up after restart.

---

#### `RestartSpawn`
**Type:** Integer  
Controls restart-related spawn behavior. Rarely adjusted.

---

### Cleanup System

The cleanup system removes dead bodies, ruined items, and unclaimed loot. These timers determine how long each category persists before removal.

---

#### `CleanupAvoidance`
**Type:** Integer (distance in meters)  
The radius around players within which CE avoids running cleanup. Items within this radius of any player are not cleaned up even if their lifetime has expired.

**Default:** 100  
**Raising this:** Items near players persist longer — reduces frustration of loot disappearing while nearby.  
**Lowering this:** More aggressive cleanup near players.

---

#### `CleanupLifetimeDeadPlayer`
**Type:** Integer (seconds)  
How long a dead player's body remains in the world before cleanup removes it.

**Default:** 3600 (1 hour)  
**Console typical range:** 1800–7200  
**Note:** Higher values give players more time to retrieve their gear after death. Very high values cause body accumulation on active servers.

---

#### `CleanupLifetimeDeadInfected`
**Type:** Integer (seconds)  
How long dead infected remain before cleanup.

**Default:** 330  
**Note:** Lower values keep the world cleaner and reduce visual clutter from kills. High-traffic combat areas accumulate fast.

---

#### `CleanupLifetimeDeadAnimal`
**Type:** Integer (seconds)  
How long dead animals (not killed by players — natural despawn) remain before cleanup.

**Default:** 1200 (20 minutes)

---

#### `CleanupLifetimeDefault`
**Type:** Integer (minutes)  
Default cleanup lifetime for general objects not covered by a more specific lifetime rule.

**Default:** 45  
**Note:** This acts as a fallback floor for miscellaneous world objects.

---

#### `CleanupLifetimeLimit`
**Type:** Integer  
A threshold value used by the cleanup scheduler. Interacts with the general cleanup timing system.

---

#### `CleanupLifetimeRuined`
**Type:** Integer (seconds)  
How long ruined items (condition = ruined) persist before CE removes them.

**Default:** 330  
**Lowering this:** Ruined gear clears faster — keeps the world cleaner.  
**Raising this:** Ruined gear sticks around longer.

---

### Base and Flag Persistence

---

#### `FlagRefreshFrequency`
**Type:** Integer (seconds)  
How often the flag/territory persistence refresh cycle runs. Flags keep player bases protected from decay. This controls how frequently the refresh check happens.

**Default:** 432000 (5 days)

---

#### `FlagRefreshMaxDuration`
**Type:** Integer (seconds)  
Maximum duration a flag/territory can be refreshed and remain active without player interaction.

**Default:** 3456000 (40 days)  
**Note:** This is the maximum persistence window for player-placed flags.

---

### Loot Condition on Spawn

---

#### `LootDamageMin`
**Type:** Float (type="1")  
Minimum damage value applied to items when they spawn. `0.00` means items can spawn in perfect condition.

**Default:** 0.0  
**Range:** 0.00 to 1.00  
**Note:** Combined with LootDamageMax, this defines the damage range for spawned loot. Vanilla default of 0.0 means items can spawn pristine.

---

#### `LootDamageMax`
**Type:** Float (type="1")  
Maximum damage value applied to items when they spawn.

**Default:** 0.82 — vanilla loot spawns up to heavily worn condition.  
**Range:** 0.00 to 1.00  
**Setting both to 0.00:** All items spawn in perfect (pristine) condition — popular on boosted servers.  
**Setting high values:** Items spawn more damaged — for hardcore/scarcity servers.

---

#### `LootProxyPlacement`
**Type:** Integer (0 or 1)  
Enables/disables loot proxy placement behavior. Leave at `1` in normal operation.

---

#### `LootSpawnAvoidance`
**Type:** Integer (distance in meters)  
Distance from players within which CE avoids spawning new loot. Prevents items from appearing directly next to players.

**Default:** 100  

---

### Map Loot Respawn Controls

These three variables control how the CE respawns map loot — they have nothing to do with player spawning.

---

#### `RespawnAttempt`
**Type:** Integer  
How many placement attempts the CE makes per loot respawn cycle when trying to find valid spawn points for items. If all attempts fail (no valid loot point available), that respawn pass is skipped.

---

#### `RespawnLimit`
**Type:** Integer  
Cap on the number of loot items the CE will respawn in a single cycle pass. Prevents CE from trying to refill too aggressively in one pass, which would cause performance spikes.

---

#### `RespawnTypes`
**Type:** Integer  
Controls how many item types the CE processes per loot respawn cycle. Higher values mean CE evaluates more item types per pass — useful for large economies but increases per-cycle processing load.

---

### Timing and Anti-Exploit

---

#### `TimeLogin`
**Type:** Integer (seconds)  
How long a player must remain stationary during the login sequence before full world entry.

**Default:** 15

---

#### `TimeLogout`
**Type:** Integer (seconds)  
How long the logout countdown takes when a player manually disconnects.

**Default:** 15  
**Raising this:** Players must stay in place longer to safely log out — reduces combat logging.

---

#### `TimeHopping`
**Type:** Integer (minutes)  
Penalty timer applied to players who switch servers frequently (server hopping). During this period their character has limited interaction capability.

**Default:** 60 minutes  
**Raising this:** Stronger deterrent against server hopping for loot.

---

#### `TimePenalty`
**Type:** Integer  
General time penalty value applied in certain anti-exploit situations.

---

### World Simulation

---

#### `WorldWetTempUpdate`
**Type:** Integer (0 or 1)  
Enables/disables wetness and temperature update simulation for world objects. Leave at `1` unless you have a specific reason to disable.

---

#### `ZoneSpawnDist`
**Type:** Integer (meters)  
Distance control used for zone spawn separation logic. Affects spacing behavior in zone-based spawning.

---

### Idle Mode

---

#### `IdleModeStartup`
**Type:** Integer (0 or 1)  
Whether idle mode is enabled on server startup. When idle, the server reduces simulation fidelity when no players are connected.

---

#### `IdleModeCountdown`
**Type:** Integer (seconds)  
How long after all players disconnect before the server enters idle mode.

---

### Food Decay

---

#### `FoodDecay`
**Type:** Integer (0 or 1)  
Enables or disables food decay across the entire server.

**Default:** 1 (food decays normally in vanilla)  
**`0`:** Disables food decay — popular on console servers to reduce gear maintenance frustration.  
**`1`:** Food decays normally over time.

---

## What Console Admins Typically Adjust

1. **`AnimalMaxCount` and `ZombieMaxCount`** — raise to increase world population density, lower to reduce performance load.
2. **`CleanupLifetimeDeadPlayer`** — raise to give players more time to recover their body.
3. **`CleanupLifetimeDefault`** — lower to keep the world cleaner.
4. **`LootDamageMin` and `LootDamageMax`** — set both to 0.00 on boosted servers for pristine loot.
5. **`TimeLogin` and `TimeLogout`** — raise TimeLogout to reduce combat logging.
6. **`TimeHopping`** — raise to deter server hopping.
7. **`FoodDecay`** — set to 0 on servers that want a more casual food experience.

---

## Safe Edit Pattern

1. Change population (AnimalMaxCount, ZombieMaxCount) first and test.
2. Then change cleanup timers as a group.
3. Then change loot damage if desired.
4. Never change `type` attribute on any line.
5. Never put a decimal in a `type="0"` field.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Putting decimal in `type="0"` field | File parse error, server may fail to load config |
| Changing `type` attribute | Breaks that variable's interpretation |
| Raising MaxCount without cleanup capacity | CE overtime pressure, lag, and overtime errors in RPT logs |
| Setting CleanupLifetimeDeadPlayer very high on active server | Body accumulation, performance degradation |
| Editing many unrelated variables at once | When something breaks, impossible to identify which change caused it |
