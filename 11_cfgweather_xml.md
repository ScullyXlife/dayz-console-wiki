---
title: "cfgweather.xml"
nav_order: 6
parent: "Mission Root Config"
---

# cfgweather.xml

## Purpose

`cfgweather.xml` controls the server's weather simulation — overcast, fog, rain, wind, snowfall, and storm behavior. Each weather phenomenon has a current starting state, boundary limits it can fluctuate within, how quickly it changes, and how long each weather state lasts.

The vanilla file ships with `enable="0"` — meaning Bohemia's default is for the server to run its internal weather system unless you explicitly enable this file. Set `enable="1"` to activate your custom weather configuration.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgweather.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgweather.xml    (PlayStation)
```

---

## Full Block Structure

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<weather enable="true" reset="false">

  <overcast>
    <current actual="0.5" time="0" duration="0" />
    <limits min="0.0" max="1.0" />
    <timelimits min="600" max="3600" />
    <changelimits min="0.0" max="0.3" />
  </overcast>

  <fog>
    <current actual="0.0" time="0" duration="0" />
    <limits min="0.0" max="0.3" />
    <timelimits min="600" max="7200" />
    <changelimits min="0.0" max="0.1" />
  </fog>

  <rain>
    <current actual="0.0" time="0" duration="0" />
    <limits min="0.0" max="0.8" />
    <timelimits min="1800" max="7200" />
    <changelimits min="0.0" max="0.2" />
    <thresholds min="0.5" max="0.7" end="0" />
  </rain>

  <windMagnitude>
    <current actual="2.0" time="0" duration="0" />
    <limits min="0.0" max="10.0" />
    <timelimits min="300" max="3600" />
    <changelimits min="0.0" max="3.0" />
  </windMagnitude>

  <windDirection>
    <current actual="0.0" time="0" duration="0" />
    <limits min="-3.14" max="3.14" />
    <timelimits min="300" max="3600" />
    <changelimits min="0.0" max="1.0" />
  </windDirection>

  <snowfall>
    <current actual="0.0" time="0" duration="0" />
    <limits min="0.0" max="0.5" />
    <timelimits min="1800" max="7200" />
    <changelimits min="0.0" max="0.1" />
    <thresholds min="0.3" max="0.5" end="0" />
  </snowfall>

  <storm density="0.5" threshold="0.7" timeout="1800" />

</weather>
```

---

## Root Element Attributes

### `enable="true/false"`

Enables or disables the custom weather configuration.
- `true` — this file controls weather behavior
- `false` — server uses default weather cycle, this file is ignored

### `reset="true/false"`

When `true`, resets all weather to default before applying config. Leave as `false` in most configurations.

---

## Shared Sub-Element Structure

Every weather phenomenon (overcast, fog, rain, wind, snowfall) uses the same four sub-elements:

### `<current actual="..." time="..." duration="..." />`

| Attribute | What it controls |
|---|---|
| `actual` | Starting value of this phenomenon when server initializes |
| `time` | How many seconds into the current weather state we start (usually `0`) |
| `duration` | Duration of the initial state in seconds (usually `0` to let timelimits control it) |

For unit phenomena (overcast, fog, rain, snowfall): `actual` is 0.0–1.0  
For wind magnitude: `actual` is 0.0–20.0  
For wind direction: `actual` is -3.14–3.14 (radians)

---

### `<limits min="..." max="..." />`

The hard floor and ceiling for this phenomenon. The weather simulation will never go outside these bounds.

- `min` and `max` are the same type as `actual` for each phenomenon
- Setting `min="0.5" max="0.5"` locks the value at exactly 0.5 (no variation)
- Setting `min="0.0" max="0.0"` disables the phenomenon entirely

---

### `<timelimits min="..." max="..." />`

**Unit:** Seconds  
How long each weather state persists before the simulation transitions to a new target value. The engine picks a random duration between `min` and `max` for each weather state.

Short timelimits = rapidly changing weather  
Long timelimits = slow, stable weather that holds for a long time

---

### `<changelimits min="..." max="..." />`

How much the value can change each time the weather transitions. A small changelimit means gradual shifts; a large one allows dramatic swings in a single transition.

---

## Phenomenon Reference

### `<overcast>`

**Range:** 0.0–1.0  
Controls cloud cover and general sky brightness.
- `0.0` = clear sky
- `0.5` = partly cloudy
- `1.0` = fully overcast

Overcast also affects how quickly rain and storms can occur — high overcast is typically required for rain to activate.

---

### `<fog>`

**Range:** 0.0–1.0  
Controls visibility-reducing fog.
- `0.0` = no fog
- `1.0` = maximum fog (extremely low visibility)

**Console note:** Heavy fog significantly impacts player experience. Keep `max` at 0.3–0.5 for manageable fog. Fog above 0.7 can make navigation nearly impossible.

---

### `<rain>`

**Range:** 0.0–1.0  
Controls rainfall intensity.
- `0.0` = no rain
- `1.0` = maximum rain

Requires sufficient overcast to activate. Rain affects player warmth and wetness.

#### `<thresholds min="..." max="..." end="..." />`

Overcast thresholds required for rain to start (`min`) and stop (`max`). `end` controls end behavior.

---

### `<wind>` (legacy) and `<windMagnitude>`

**`windMagnitude` range:** 0.0–20.0 (meters per second)  
Controls wind speed. Affects player temperature, sound, and some physics.

- `0.0` = calm
- `5.0` = moderate wind
- `15.0`+ = strong wind

`<wind>` is a legacy element. Use `<windMagnitude>` and `<windDirection>` in current configs.

---

### `<windDirection>`

**Range:** -3.14–3.14 (radians)  
Controls the direction wind is blowing. `-3.14` to `3.14` covers a full circle in radians.

- `0.0` = north
- `1.57` = east (π/2)
- `3.14` / `-3.14` = south
- `-1.57` = west (-π/2)

---

### `<snowfall>`

**Range:** 0.0–1.0  
Controls snowfall intensity. Primarily relevant for Sakhal (the snow map). On Chernarus and Livonia this has limited visual effect depending on season.

- `0.0` = no snow
- `1.0` = heavy snowfall

Like `<rain>`, `<snowfall>` supports a `<thresholds min="..." max="..." end="..." />` sub-element with the same meaning as the rain thresholds: `min`/`max` govern the overcast level needed for snowfall to start/stop, and `end` controls end behavior.

---

### `<storm density="..." threshold="..." timeout="..." />`

| Attribute | What it controls |
|---|---|
| `density` | Intensity of storm effects (0.0–1.0) |
| `threshold` | Overcast level required to trigger storm behavior (0.0–1.0) |
| `timeout` | Seconds between storm evaluations |

---

## What Console Admins Typically Adjust

1. **`<overcast limits max="...">`** — lower max to keep skies brighter and less gloomy.
2. **`<fog limits max="...">`** — lower max to reduce extreme fog events.
3. **`<rain limits max="...">`** — lower max to reduce heavy rain events.
4. **`<overcast current actual="...">`** — set starting sky state.
5. **`<windMagnitude limits max="...">`** — cap wind speed.

---

## Example: Clear-Weather Server

```xml
<overcast>
  <current actual="0.1" time="0" duration="0" />
  <limits min="0.0" max="0.4" />
  <timelimits min="1800" max="7200" />
  <changelimits min="0.0" max="0.1" />
</overcast>
<fog>
  <current actual="0.0" time="0" duration="0" />
  <limits min="0.0" max="0.1" />
  <timelimits min="3600" max="14400" />
  <changelimits min="0.0" max="0.05" />
</fog>
<rain>
  <current actual="0.0" time="0" duration="0" />
  <limits min="0.0" max="0.2" />
  <timelimits min="3600" max="14400" />
  <changelimits min="0.0" max="0.1" />
</rain>
```

---

## Common Mistakes

| Mistake | Result |
|---|---|
| `fog limits max="1.0"` | Server can enter near-zero visibility conditions |
| `timelimits min` and `max` both very low | Weather changes every few seconds — disorienting |
| `limits min="X" max="Y"` where min > max | File validation error |
| `enable="false"` but expecting custom weather | File is ignored entirely |
| Setting `changelimits max` very high | Jarring sudden weather jumps |
