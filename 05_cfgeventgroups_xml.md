---
title: "cfgeventgroups.xml"
nav_order: 3
parent: "Mission Root Config"
---

# cfgeventgroups.xml

## Purpose

`cfgeventgroups.xml` defines what physically appears at an event site — the objects, vehicles, structures, and loot that compose it. When an event fires and places itself at a coordinate, it uses the group definitions here to decide exactly what to place and where relative to the event origin.

This file is the "what" of events. `events.xml` says how many and when. `cfgeventspawns.xml` says where. This file says what the player actually finds when they get there.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgeventgroups.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgeventgroups.xml    (PlayStation)
```

## Connects To

- **events.xml (db/)** — events reference group names in their `<child type="...">` entries
- **types.xml (db/)** — items placed by groups must exist in types.xml

---

## Full Block Structure

```xml
<eventgroups>

  <group name="Land_Wreck_Car3_Blue">
    <child type="Land_Wreck_Car3_Blue"
           lootmax="0" lootmin="0"
           x="0" y="0" z="0" a="0"
           deloot="0"/>
    <child type="PoliceCap"
           lootmax="1" lootmin="0"
           x="0.6" y="0.2" z="-0.4" a="10"
           deloot="0"/>
    <child type="PoliceJacket_Black"
           lootmax="1" lootmin="0"
           x="-0.5" y="0.1" z="0.3" a="0"
           deloot="0"/>
  </group>

  <group name="StaticHeliCrashSite">
    <child type="Land_Wreck_Mi8"
           lootmax="0" lootmin="0"
           x="0" y="0" z="0" a="0"
           deloot="0"/>
    <child type="M4A1"
           lootmax="1" lootmin="0"
           x="2.0" y="0.5" z="1.0" a="45"
           deloot="0"/>
    <child type="ACR"
           lootmax="1" lootmin="0"
           x="-1.5" y="0.3" z="0.8" a="90"
           deloot="0"/>
  </group>

</eventgroups>
```

---

## Field-by-Field Reference

### `<group name="...">`

The group identity. Referenced by events.xml in `<child type="...">` where type matches this name. Case-sensitive.

One group can be referenced by multiple events. An event can reference multiple different groups via multiple `<child>` entries.

---

### `<child ...>`

One object or item placed when this group is spawned. A group can have unlimited children.

| Attribute | Type | What it controls |
|---|---|---|
| `type` | String | Classname of the object or item to place. Must exist in types.xml if it is a loot item. |
| `x` | Float (meters) | Local X offset from the group origin |
| `y` | Float (meters) | Local Y offset from the group origin — vertical height |
| `z` | Float (meters) | Local Z offset from the group origin |
| `a` | Float (degrees) | Local rotation of this child relative to group origin |
| `lootmin` | Integer | Minimum quantity of this child placed per group spawn |
| `lootmax` | Integer | Maximum quantity of this child placed per group spawn |
| `deloot` | Integer (0 or 1) | Whether CE handles this child as dynamic loot (1) or a persistent world object (0) |

---

#### `x`, `y`, `z` offsets

These are local offsets from the group's spawn origin (the coordinate from cfgeventspawns.xml). They position each child relative to the center of the group.

- `x` and `z` move the child horizontally
- `y` moves it vertically

**Most common mistake:** Setting `y` too high causes items to float above the ground. Setting it negative buries them underground. For items meant to be on the ground near a wreck, `y` values between `0.0` and `0.5` are typical. For items inside/on structures, adjust based on the structure's geometry.

---

#### `lootmin` and `lootmax`

For structural objects like vehicle wrecks or building shells, set both to `0` — they are placed once as scenery, not as variable-quantity loot.

For items that should spawn in variable quantities (ammo piles, gear scattered around a site), set `lootmin` lower and `lootmax` higher. The engine picks a random number in this range.

```xml
<!-- Wreck - always place exactly once, no loot quantity -->
<child type="Land_Wreck_Mi8" lootmax="0" lootmin="0" x="0" y="0" z="0" a="0" deloot="0"/>

<!-- Ammo - randomly 1 to 3 piles placed nearby -->
<child type="AmmoBox_556x45_20rnd" lootmax="3" lootmin="1" x="1.5" y="0.1" z="0.5" a="0" deloot="0"/>
```

---

#### `deloot`

**This is a three-state value, not a plain boolean.** The native loader (`0x1407fe370`) preserves and accepts `-1` in addition to `0` and `1`:

- `0` — object is treated as a persistent world object, not subject to CE loot cycling
- `1` — object is treated as dynamic loot, CE may cycle it through cleanup/respawn
- `-1` — accepted by the loader as a distinct state from `0`/`1`; treat as "unset/inherit" rather than assuming it behaves identically to `0`

For structural objects (wrecks, buildings) use `0`. For loose loot items, use `1`. Don't assume `-1` and `0` are interchangeable without testing — the loader keeps them as separate values.

---

#### `spawnsecondary` and `trace`

Two additional per-child fields the native loader reads, in addition to the attributes above:

| Attribute | Type | What it controls |
|---|---|---|
| `spawnsecondary` | Integer (0 or 1) | Whether this child can trigger a secondary spawner (used for NPC/AI-linked children within an event group, paired with the `sec_spawner` flag in events.xml) |
| `trace` | Integer (0 or 1) | Whether this child's placement is terrain-traced (adjusted to sit correctly on uneven ground) rather than placed at a flat offset |

```xml
<child type="Wolf_Grey" lootmax="0" lootmin="0" x="1.0" y="0" z="0.5" a="0" deloot="0" spawnsecondary="1" trace="1"/>
```

---

## What Console Admins Typically Adjust

1. **`lootmax` on item children** — increase to make event sites drop more loot.
2. **`lootmin` on item children** — set above 0 to guarantee a minimum drop.
3. **`x/z` offsets** — adjust item placement positions relative to the wreck/structure.
4. **Adding new `<child>` entries** — add additional items to an existing event group.
5. **`y` values** — fix items floating or buried underground.

---

## Safe Edit Pattern

1. Change `lootmax` first and test before adjusting placement offsets.
2. Keep `y` values small (0.0–0.8) for ground-placed items.
3. When adding a new `<child>`, confirm the classname exists in types.xml.
4. Avoid setting `lootmax` very high on rare/military items — creates extremely rewarding hotspots.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| `type=` classname not in types.xml | Item silently fails to spawn |
| `y` offset too high | Items float above the ground visibly |
| `y` offset negative/too low | Items buried underground and unreachable |
| `lootmax` much higher than `lootmin` on weapons | Event sites become loot bombs |
| Group name doesn't match what events.xml references | Group is never used |
