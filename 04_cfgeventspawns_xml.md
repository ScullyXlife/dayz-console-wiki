---
title: "cfgeventspawns.xml"
nav_order: 2
parent: "Mission Root Config"
---

# cfgeventspawns.xml

## Purpose

`cfgeventspawns.xml` is the coordinate pool for every dynamic event. Each event defined in `events.xml` draws its spawn locations exclusively from this file. If an event has no entries here, it has nowhere to place itself and will never appear on the map — regardless of how its rules are configured in events.xml.

Think of this file as the "where" to events.xml's "how many and how long."

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgeventspawns.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgeventspawns.xml    (PlayStation)
```

## Connects To

- **events.xml (db/)** — every `name=` here must match an event name in events.xml

---

## Full Block Structure

```xml
<eventposdef>

  <event name="StaticHeliCrash">
    <pos x="4523.3" z="9241.7" a="0.0" />
    <pos x="7812.1" z="3409.6" a="45.0" />
    <pos x="2198.4" z="6754.2" a="180.0" />
  </event>

  <event name="AnimalWolf">
    <pos x="3421.0" z="8901.2" a="0.0" />
    <pos x="6201.5" z="4321.8" a="0.0" />
  </event>

</eventposdef>
```

---

## Field-by-Field Reference

### `<event name="...">`

The event name. Must exactly match the `name=` of an event in `events.xml`. Case-sensitive.

If the name doesn't match any event, the coordinates exist in this file but are never used. If an event in events.xml has no matching block here, it cannot spawn.

---

### `<pos x="..." z="..." a="..." />`

One spawn location candidate. The event scheduler draws from all `<pos>` entries in a block when placing instances.

| Attribute | Type | What it controls |
|---|---|---|
| `x` | Float | World X coordinate |
| `z` | Float | World Z coordinate |
| `a` | Float (degrees) | Rotation/angle of the placed object. `0.0` for most events. Used to orient vehicles/structures. |

**Coordinate system:** DayZ uses an X/Z plane (not X/Y). Y is vertical (height) and is determined by the terrain at the X/Z position automatically. You do not set Y here.

**How many positions:** The event will randomly select from available positions when spawning a new instance, subject to the `saferadius` and `distanceradius` rules in events.xml. Having more positions gives the event scheduler more flexibility and makes the event feel less predictable in location.

**Minimum recommended:** At least as many positions as the event's `nominal` value. If nominal is 4 and there are only 2 positions, the event can never reach its target count simultaneously.

---

### `<pos ... group="..." />` (group-linked positions)

A `<pos>` entry can optionally carry a `group` attribute, linking it to a named group defined in `cfgeventgroups.xml`:

```xml
<pos x="4523.3" z="9241.7" a="0.0" group="StaticHeliCrashSite" />
```

If `group` is set, the engine validates that the referenced group exists before using the position. A dangling `group` reference (name not found in cfgeventgroups.xml) is rejected — the position is not used for that event.

---

### Dynamic zones

An event can also carry dynamic zone records inside its `cfgeventspawns.xml` block, controlling a radius-based population zone independent of the fixed `<pos>` list:

```xml
<event name="AnimalWolf">
  <zone r="30" smin="0" smax="0" dmin="1" dmax="5" />
  <pos x="3421.0" z="8901.2" a="0.0" />
</event>
```

| Attribute | What it controls |
|---|---|
| `r` | Zone radius (meters) |
| `smin` / `smax` | Static minimum/maximum count for the zone |
| `dmin` / `dmax` | Dynamic minimum/maximum count for the zone |

An installed mission carries 30 dynamic zone records. Negative radius/minima and inverted maxima (`smax` < `smin`, etc.) are rejected by the loader.

---

## What Console Admins Typically Adjust

1. **Adding `<pos>` entries** — expand the spawn pool to make events appear in more locations.
2. **Removing `<pos>` entries** — restrict where events appear (e.g. remove unsafe terrain coords).
3. **Adjusting `a=`** — fix visual orientation of vehicle wrecks or structures that spawn facing wrong direction.

---

## Getting Coordinates

Coordinates are world-space X/Z values from the DayZ map. Common methods to obtain them:
- In-game admin tools showing position
- Map tools that display coordinates (IC ToolSuite Event Spawns Editor shows a map view)
- Copying from existing entries and adjusting

---

## Safe Edit Pattern

1. Before removing a coordinate, check that the event can still reach its nominal with remaining positions.
2. When adding new positions, verify terrain is flat/valid for the event type — bad terrain can cause floating/buried objects.
3. Do not add the same coordinate twice — the scheduler may repeatedly place the event at that spot.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Event name doesn't match events.xml | Coordinates exist but event never uses them |
| Fewer positions than nominal value | Event can never reach its nominal count |
| Duplicate or very nearby coordinates | Event always appears in same cluster of spots |
| Wrong map coordinates (from different map) | Event tries to place but position is invalid |
| Large `a=` rotation on an event type that ignores rotation | No visible effect, but not harmful |
