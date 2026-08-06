---
title: "events.xml"
nav_order: 3
parent: "Core Economy — db/"
---

# events.xml

## Purpose

`events.xml` defines the rules for every dynamic event on the server — how many should exist simultaneously, how long they last, how far apart they must be, and what objects they spawn. Think of it as the scheduler and rules engine for all world events (vehicle wrecks, helicopter crashes, animal groups, static military camps, etc.).

An event without valid entries in `cfgeventspawns.xml` cannot place itself — it has nowhere to go. An event without a valid group in `cfgeventgroups.xml` cannot place anything when it fires.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/db/events.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/db/events.xml    (PlayStation)
```

## Connects To

- **cfgeventspawns.xml** — provides the coordinate pool this event draws from
- **cfgeventgroups.xml** — defines what objects/loot are composed at the event site
- **types.xml** — items referenced by event groups must have types.xml entries

---

## Full Block Structure

```xml
<event name="MilitaryConvoy">
  <nominal>2</nominal>
  <min>0</min>
  <max>0</max>
  <lifetime>1800</lifetime>
  <restock>0</restock>
  <saferadius>1000</saferadius>
  <distanceradius>250</distanceradius>
  <cleanupradius>1000</cleanupradius>
  <secondary>InfectedArmy</secondary>
  <flags deletable="1" init_random="0" remove_damaged="1" sec_spawner="0" />
  <position>fixed</position>
  <limit>child</limit>
  <active>1</active>
  <children>
    <child lootmax="3" lootmin="1" max="0" min="0" type="Wreck_Military" />
  </children>
</event>
```

---

## Field-by-Field Reference

### `<event name="...">`

The event identity. Must match the name referenced in `cfgeventspawns.xml` exactly. Case-sensitive. If the name doesn't match, the event has no spawn coordinate pool and cannot place.

---

### `<nominal>`

**Type:** Integer  
Target concurrent count of this event type active on the map simultaneously. CE works to maintain this many active instances.

- `0` means the event is defined but the scheduler does not try to maintain any active instances.
- For most events this is 1–10 depending on event type.

---

### `<min>` and `<max>`

**Type:** Integer  
Runtime floor and ceiling controls used by the scheduler. In most standard configs these are `0`, meaning the scheduler uses nominal as the primary target without a separate min/max floor/ceiling.

When non-zero:
- `min` — scheduler treats this as the minimum the event must maintain
- `max` — hard cap above nominal the scheduler will not exceed

---

### `<lifetime>`

**Type:** Integer (seconds)  
How long an event instance can persist before the cleanup cycle considers it eligible for removal and replacement.

**Common values:**
| Value | Duration |
|---|---|
| 900 | 15 minutes |
| 1800 | 30 minutes |
| 3600 | 1 hour |
| 7200 | 2 hours |
| 86400 | 24 hours |

**Note:** If a player is interacting with an event site (nearby, looting), cleanup avoidance in globals.xml may delay its removal past the nominal lifetime.

---

### `<restock>`

**Type:** Integer (seconds)  
Delay before the scheduler attempts to replace this event after cleanup or natural expiry.

- `0` means the scheduler can replace it immediately on the next CE cycle.
- A value like `1800` means 30 minutes must pass before CE tries to replace the event.

---

### `<saferadius>`

**Type:** Integer (meters)  
Distance from certain protected conditions (players, other events depending on type) within which this event will not spawn. The event skips any coordinate that would place it within this radius of a disqualifying condition.

**Effect:** Large saferadius with few valid coordinates can starve spawn opportunities — the event exists in config but has no valid placement.

---

### `<distanceradius>`

**Type:** Integer (meters)  
Minimum spacing between active instances of this same event type. CE will not place two instances of the same event within this distance of each other.

**Combined with saferadius:** If saferadius is 1000 and distanceradius is 500 and the map only has 10 valid coordinates all within 1500m of each other, the event will have trouble reaching its nominal target.

---

### `<cleanupradius>`

**Type:** Integer (meters)  
Radius used for cleanup evaluation around the event origin point. Objects within this radius that belong to this event are considered part of it for cleanup purposes.

---

### `<secondary>`

**Type:** String (event name)  
Optional. When defined, links a secondary event profile to fire alongside or in relation to this event. Used for pairing an environment spawn (like an army zombie cluster) with a physical event site (like a military wreck).

Not all events use this.

---

### `<flags ...>`

| Flag | Values | What it controls |
|---|---|---|
| `deletable` | 0 or 1 | Whether this event's objects can be deleted/cleaned up by CE during its lifetime |
| `init_random` | 0 or 1 | Randomize population placement at initial startup |
| `remove_damaged` | 0 or 1 | Whether damaged/ruined objects from this event are removed during cleanup |
| `sec_spawner` | 0 or 1 | Uses secondary spawner behavior when set to 1 — used for NPC/AI-linked events |

---

### `<position>`

**Type:** String  
Spawn placement mode.

- `fixed` — event spawns at specific coordinates from cfgeventspawns.xml
- `player` — positions relative to player location (less common, some special events)
- `uniform` — positions are drawn more evenly across the available coordinate pool rather than fully random selection

Most events use `fixed`.

---

### `<limit>`

**Type:** String  
Limiter mode controlling how the nominal cap is interpreted.

- `child` — cap applies to child objects within the event
- `mixed` — mixed limiter mode, used by animal events
- `parent` — cap applies at the parent/event level rather than per-child
- `custom` — custom limiter logic, used by specific event types outside the standard child/parent/mixed patterns

If `<limit>` is set to `child` or `mixed` but a specific child's `max` is left at `0`, the event is defined without a usable child cap — check the RPT log for a related diagnostic if children aren't appearing as expected.

---

### `<active>`

**Type:** Integer (0 or 1)  
Enables or disables this event entirely without deleting its config block.

- `1` — event is active, scheduler manages it
- `0` — event is disabled, scheduler ignores it completely

**This is the cleanest way to disable an event** — set `active` to `0` rather than deleting the entry. Deleting an entry that is referenced by other files can cause errors.

---

### `<children>` / `<child ...>`

Defines the objects this event spawns and their loot constraints.

```xml
<children>
  <child lootmax="3" lootmin="1" max="0" min="0" type="Wreck_Military" />
</children>
```

| Attribute | What it controls |
|---|---|
| `type` | Object or group classname this event places — must match cfgeventgroups.xml group name or a direct object classname |
| `lootmin` | Minimum number of loot items generated at this child |
| `lootmax` | Maximum number of loot items generated at this child |
| `min` | Minimum count of this child object per event instance |
| `max` | Maximum count of this child object per event instance |

---

## What Console Admins Typically Adjust

1. **`nominal`** — increase to make events more common on the map simultaneously.
2. **`lifetime`** — increase to make event sites persist longer before despawning.
3. **`active`** — set to `0` to disable an event you don't want on your server.
4. **`distanceradius`** — lower to allow events to spawn closer together (more density).
5. **`lootmax` on children** — increase to make event sites more rewarding.

---

## Safe Edit Pattern

1. Adjust `nominal` first. Test. Then adjust `lifetime`.
2. Never raise `nominal` without checking how many valid coordinates exist in `cfgeventspawns.xml` for this event — if there are 3 coordinates and nominal is 5, the event can never reach its target.
3. When adding a new event, add matching entries in both `cfgeventspawns.xml` and `cfgeventgroups.xml`.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Raising nominal without enough coordinates in cfgeventspawns.xml | Event never reaches target count |
| Large saferadius + large distanceradius | Event starved of valid spawn positions |
| Event name mismatch with cfgeventspawns.xml | Event fires but has no spawn pool |
| Deleting an event that cfgeventgroups.xml references | Broken reference, error in RPT logs |
| Setting `active=1` on a new event with no cfgeventspawns.xml entries | Event defined but never places |
