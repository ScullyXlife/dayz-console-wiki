---
title: "cfgplayerspawnpoints.xml"
nav_order: 7
parent: "Mission Root Config"
---

# cfgplayerspawnpoints.xml

## Purpose

`cfgplayerspawnpoints.xml` defines where players appear when they spawn or respawn. It supports three spawn categories — fresh spawns (new/freshly dead players), hop spawns (server switching from same map), and travel spawns (switching from a different map). On console servers, only `fresh` is typically used.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgplayerspawnpoints.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgplayerspawnpoints.xml    (PlayStation)
```

---

## Structure: Two Supported Formats

### Format 1 — Simple (most common on console)

Direct `<pos>` entries at the root of each spawn category. The server picks randomly from the list.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<playerspawnpoints>
  <fresh>
    <pos x="3692.5" z="2376.2" />
    <pos x="4821.1" z="1983.7" />
    <pos x="2914.8" z="3201.4" />
    <pos x="5102.3" z="4677.9" />
  </fresh>
</playerspawnpoints>
```

### Format 2 — Generator Posbubbles (grouped spawns)

Spawn points organized into named groups. Players are assigned to groups and spawn within their group's point pool. Used for more sophisticated spawn systems.

```xml
<playerspawnpoints>
  <fresh>
    <spawn_params>
      <min_dist_infected>50</min_dist_infected>
      <max_dist_infected>500</max_dist_infected>
      <min_dist_player>100</min_dist_player>
      <max_dist_player>1000</max_dist_player>
    </spawn_params>
    <group_params>
      <enablegroups>true</enablegroups>
      <lifetime>300</lifetime>
      <counter>3</counter>
      <groups_as_regular>true</groups_as_regular>
    </group_params>
    <generator_params>
      <grid_density>50</grid_density>
      <grid_width>14000</grid_width>
      <grid_height>14000</grid_height>
      <min_dist_static>0</min_dist_static>
      <max_dist_static>1000</max_dist_static>
      <min_steepness>0</min_steepness>
      <max_steepness>30</max_steepness>
    </generator_params>
    <generator_posbubbles>
      <group name="CoastNorth" lifetime="300" counter="3">
        <pos x="3692.5" z="2376.2" />
        <pos x="3710.0" z="2390.0" />
      </group>
      <group name="CoastSouth">
        <pos x="5102.3" z="4677.9" />
        <pos x="5120.0" z="4695.0" />
      </group>
    </generator_posbubbles>
  </fresh>
</playerspawnpoints>
```

---

## Field-by-Field Reference

### Spawn Categories

#### `<fresh>`
Spawn points for freshly dead or brand new players. This is the primary category used on console servers.

#### `<hop>`
Spawn points for players switching servers on the same map. Only relevant on official/linked server networks. Typically not configured on private console servers.

#### `<travel>`
Spawn points for players switching from a different map. Only relevant on official/linked server networks. Typically not configured on private console servers.

---

### `<pos x="..." z="..." y="..." />`

One spawn location.

| Attribute | Type | What it controls |
|---|---|---|
| `x` | Float (≥0) | World X coordinate |
| `z` | Float (≥0) | World Z coordinate |
| `y` | Float (optional) | Explicit height override. If omitted, engine uses terrain height at X/Z. Usually omitted. |

The engine selects randomly from all `<pos>` entries when placing a fresh spawn. More entries = more variety in spawn locations.

---

### `<spawn_params>` (Format 2 only)

Controls how the engine evaluates spawn points for quality. Points are rated against nearby infected, players, and buildings.

| Element | What it controls |
|---|---|
| `min_dist_infected` | Minimum distance from infected for a spawn point to be rated positively (meters) |
| `max_dist_infected` | Maximum distance from infected for evaluation range (meters) |
| `min_dist_player` | Minimum distance from other players for positive rating (meters) |
| `max_dist_player` | Maximum evaluation distance from other players (meters) |
| `min_dist_static` | Minimum distance from buildings (meters) |
| `max_dist_static` | Maximum evaluation distance from buildings (meters) |

These don't hard-block spawns at those points — they rate the quality of each candidate point and the engine prefers higher-rated points.

---

### `<generator_params>` (Format 2 only)

Controls the generator used to lay out the grid of candidate spawn points before group/quality scoring is applied.

| Element | What it controls |
|---|---|
| `grid_density` | Spacing/density of the generated candidate point grid |
| `grid_width` / `grid_height` | Dimensions of the area the generator covers (meters) |
| `min_dist_static` / `max_dist_static` | Minimum/maximum distance from static buildings for a generated point to be considered |
| `min_steepness` / `max_steepness` | Minimum/maximum terrain steepness a generated point can sit on |

The exact scoring/generator formula that turns these inputs into the final selected spawn points is not fully confirmed — treat this as the field shape, not a proven algorithm.

---

### `<group_params>` (Format 2 only)

| Element | Type | What it controls |
|---|---|---|
| `enablegroups` | Boolean | If `true`, uses group logic. If `false`, treats all points as a flat list |
| `lifetime` | Integer (seconds) | How long a group stays "active" (used for spawn clustering) |
| `counter` | Integer | Number of players that can spawn in a group before it resets its lifetime |
| `groups_as_regular` | Boolean | If `true`, group points are also available as regular spawn points when groups are disabled |

---

### `<group name="..." lifetime="..." counter="...">` (Format 2 only)

A named cluster of spawn points. Players assigned to a group spawn within its points only.

- `name` — identifier for this spawn cluster
- `lifetime` — overrides group_params lifetime for this specific group (optional)
- `counter` — overrides group_params counter for this specific group (optional)

---

## What Console Admins Typically Adjust

1. **Adding/removing `<pos>` entries** — expand or restrict where players can spawn.
2. **Moving spawn points** — change coordinates to place spawns at specific coastal or inland locations.
3. **Using Format 1 (simple)** — most console servers use the simple format for straightforward spawn control.

---

## Getting Spawn Coordinates

Spawn coordinates are world-space X/Z values. Same coordinate system as cfgeventspawns.xml. Obtain them from:
- In-game admin tools showing your current position
- Map tools with coordinate display
- Copying and adjusting existing known points

**Console note:** Ensure spawn points are on valid terrain — not in water, not on extreme slopes, not inside buildings that may have changed. Test by spawning at the location.

---

## Safe Edit Pattern

1. Maintain at least 5–10 spread spawn points for variety.
2. Avoid clustering all spawn points in one small area — players will fight on spawn.
3. Keep spawns away from known high-traffic military zones unless you want hot-drop gameplay.
4. After editing, test by dying and spawning to confirm points are valid.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Too few spawn points (1–2) | Players repeatedly spawn in same spot |
| Spawn point in water | Player spawns and immediately starts drowning |
| Spawn point inside a building | Player may spawn inside geometry |
| `y` set incorrectly | Player spawns underground or floating |
| Missing `<fresh>` section entirely | Server falls back to default spawn behavior |
