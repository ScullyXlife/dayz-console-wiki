---
title: "territories.xml"
nav_order: 1
parent: "Territory System"
---

# territories.xml (env/ files)

## Purpose

The territory files in the `env/` folder define circular zones on the map where specific AI populations can spawn. Each file covers one creature type. The engine reads these to know where on the map that creature is allowed to appear and in what density.

These files do not control whether a creature type exists — that is `types.xml`. They control *where on the map* the creature population lives.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/env/<name>_territories.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/env/<name>_territories.xml    (PlayStation)
```

**Standard territory files on console:**
- `zombie_territories.xml`
- `wolf_territories.xml`
- `bear_territories.xml`
- `cattle_territories.xml`
- `hare_territories.xml`
- `hen_territories.xml`
- `pig_territories.xml`
- `red_deer_territories.xml`
- `roe_deer_territories.xml`
- `sheep_goat_territories.xml`
- `wild_boar_territories.xml`

## Connects To

- **cfgenvironment.xml** — wires these files to the engine via file path references. If cfgenvironment.xml references the wrong path, the territory file is never loaded.

---

## Full Block Structure

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<territory-type>
  <territory color="0xffff0000">
    <zone name="WolfZone_NW_Forest"
          smin="2" smax="6"
          dmin="1" dmax="3"
          x="3450.0" z="9100.0" r="400.0" />
    <zone name="WolfZone_Military_East"
          smin="3" smax="8"
          dmin="2" dmax="4"
          x="7820.5" z="4310.2" r="300.0" />
  </territory>
</territory-type>
```

---

## Field-by-Field Reference

### `<territory color="...">`

A logical grouping of zones for this creature type. The `color` attribute is used for map visualization in editor tools — it does not affect gameplay. Multiple `<territory>` blocks can exist in one file.

---

### `<zone ...>`

One circular spawn zone for this creature type.

| Attribute | Type | What it controls |
|---|---|---|
| `name` | String | Identifier for this zone — used in logs and editor tools. Must be unique within the file. |
| `smin` | Integer | Static minimum population within this zone |
| `smax` | Integer | Static maximum population within this zone |
| `dmin` | Integer | Dynamic minimum spawn count per cycle |
| `dmax` | Integer | Dynamic maximum spawn count per cycle |
| `x` | Float | World X coordinate of the zone center |
| `z` | Float | World Z coordinate of the zone center |
| `r` | Float (meters) | Radius of the zone circle |

---

#### `smin` and `smax` — Static Population

These define the persistent baseline population the engine tries to maintain within this zone at all times. The engine will actively respawn to keep the zone populated within this range.

- `smin` — floor: the engine works to keep at least this many active in the zone
- `smax` — ceiling: the engine will not exceed this count in the zone

**Example:** `smin="3" smax="8"` — the zone always tries to have 3–8 wolves active within it.

---

#### `dmin` and `dmax` — Dynamic Spawn Count

Controls how many creatures are spawned per active spawn cycle event within this zone. This is per-event, not the total zone population.

- `dmin` — minimum spawned in one event
- `dmax` — maximum spawned in one event

**Note:** The total population is still capped by `smax` and the global `AnimalMaxCount` / `ZombieMaxCount` in globals.xml.

---

#### `x`, `z`, `r` — Zone Position and Size

- `x` and `z` define the center of the circular zone in world coordinates
- `r` defines the radius in meters — how large the spawn circle is

**Effect of radius:**
- Small `r` (100–200m) — tight, concentrated population cluster
- Large `r` (500–1000m+) — spread-out roaming population

---

## What Console Admins Typically Adjust

1. **`smin` and `smax`** — raise to increase creature density in a zone, lower to reduce it.
2. **`r`** — increase radius to give creatures more roaming area.
3. **Adding `<zone>` entries** — add new spawn zones to expand creature coverage across the map.
4. **Removing `<zone>` entries** — remove zones to clear creatures from specific areas.
5. **`x/z`** — move a zone to a different map location.

---

## Important: cfgenvironment.xml Must Wire These Files

Editing a territory file does nothing if `cfgenvironment.xml` does not correctly reference it. If you rename a territory file or change its path, you must update `cfgenvironment.xml` to match. See the [cfgenvironment.xml article](13_cfgenvironment_xml.md) for details.

---

## Safe Edit Pattern

1. Change `smax` first and test before adding new zones.
2. Raise in steps — doubling smax or dmax in one edit can spike population unexpectedly.
3. Remember the global cap in globals.xml is the hard ceiling regardless of territory settings.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| `smin > smax` | Engine behavior undefined for that zone |
| Zone radius too small (under 50m) | Creatures can't roam and bunch at center point |
| No zones defined | That creature type has no valid spawn territory |
| cfgenvironment.xml file path mismatch | File is never loaded, creature type doesn't spawn |
| Total smax across all zones far exceeds global cap | Extra zones are wasted, global cap enforces ceiling |
