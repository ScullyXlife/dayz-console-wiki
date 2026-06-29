---
title: "types.xml"
nav_order: 1
parent: "Core Economy — db/"
---

# types.xml

## Purpose

`types.xml` is the master item registry for the Central Economy (CE). It tells the engine what items exist in the world, how many should be present at any given time, how long they stay before despawning, and what context they belong to (which locations and tiers they can appear in).

Every item that can spawn naturally in the world must have an entry here. If an item is missing from this file, it does not spawn — regardless of what other files say.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/db/types.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/db/types.xml    (PlayStation)
```

## Connects To

- **cfglimitsdefinition.xml** — category, usage, and value names used in types.xml must be defined there
- **cfgspawnabletypes.xml** — defines what spawns *with* each item (attachments, cargo)
- **mapgroupproto.xml** — building loot spots filter by category/tag/usage; if an item's usage doesn't match a building's filter, it can't appear there
- **events.xml / cfgeventgroups.xml** — items referenced in event groups must exist here

---

## Full Block Structure

```xml
<type name="BandageDressing">
  <nominal>40</nominal>
  <lifetime>10800</lifetime>
  <restock>0</restock>
  <min>20</min>
  <quantmin>-1</quantmin>
  <quantmax>-1</quantmax>
  <cost>100</cost>
  <flags count_in_cargo="1" count_in_hoarder="1" count_in_map="1" count_in_player="1" crafted="0" deloot="0" />
  <category name="tools"/>
  <usage name="Village"/>
  <usage name="Town"/>
  <value name="Tier1"/>
  <value name="Tier2"/>
</type>
```

---

## Field-by-Field Reference

### `<type name="...">`

The exact item classname. Case-sensitive. If this doesn't exactly match the engine's internal classname for the item, the entire entry is ignored silently. No error is produced — the item simply doesn't exist in the economy.

---

### `<nominal>`

**Type:** Integer  
**What it does:** The target count CE tries to maintain across the entire map at any given time. This is not a cap — it is a target the CE actively works toward.

- Setting this to `0` disables natural world spawning for the item. The item can still appear via event groups or object spawners.
- Higher values increase spawn pressure. CE will work harder to keep that many in the world.
- Raising nominal without considering building loot point capacity (in mapgroupproto.xml) or usage routing may not produce the expected result — the CE has nowhere valid to place extras.

**Common starting range:** 10–150 depending on item type and desired frequency.

---

### `<lifetime>`

**Type:** Integer (seconds)  
**What it does:** How long an item persists in the world before CE marks it for cleanup.

- This applies to items that are spawned but not interacted with.
- Items picked up and dropped are governed by different persistence rules depending on storage type.
- Higher values keep more "old" loot clutter on the map, which can increase CE cleanup pressure.
- Lower values force more frequent respawn cycles, which can make loot feel fresh but also increases CE workload.

**Common values:**
| Value | Duration |
|---|---|
| 3600 | 1 hour |
| 10800 | 3 hours |
| 21600 | 6 hours |
| 43200 | 12 hours |
| 86400 | 24 hours |
| 604800 | 7 days |

**Console note:** Dead body cleanup and persistence are controlled in globals.xml, not here.

---

### `<restock>`

**Type:** Integer (seconds)  
**What it does:** Delay before CE attempts to refill this item after the count drops below `min`.

- `0` means CE can attempt refill immediately when conditions allow (next CE cycle).
- A positive value forces a wait period before refill.
- Rarely adjusted in normal console operations. Most servers leave this at `0`.

---

### `<min>`

**Type:** Integer  
**What it does:** The refill trigger threshold. When the item count in the world drops below this number, CE schedules a refill attempt.

**Critical rule:** `min` must always be less than `nominal`. If `min >= nominal`, CE behavior becomes undefined and the item may not spawn correctly.

**Typical pattern:** Set `min` to roughly 50% of `nominal`. Example: nominal 40, min 20.

---

### `<quantmin>` and `<quantmax>`

**Type:** Integer  
**What they do:** Define the quantity range for stack-based or charge-based items (ammo, food servings, water, etc.).

- `-1` means use the item's default quantity behavior (engine default).
- For non-stackable items (most weapons, clothing), leave both at `-1`.
- For ammo: setting `quantmin="1"` and `quantmax="30"` means a spawned magazine contains 1–30 rounds.

**Example uses:**
- Ammo: control how loaded magazines spawn
- Food: control how many uses/servings spawn
- Gasoline canister: control how full it spawns

---

### `<cost>`

**Type:** Integer (0–100)  
**What it does:** CE priority weight. Items with higher cost are given higher priority in CE scheduling decisions when spawn slots are contested.

- Default is usually `100`.
- Rarely adjusted in console operations unless you are doing deep economy balancing.
- Changing this incorrectly can de-prioritize important items in favor of junk.

---

### `<flags ...>`

All flags are `0` (false) or `1` (true).

| Flag | What it controls |
|---|---|
| `count_in_map` | Counts world-placed instances of this item toward the nominal total |
| `count_in_player` | Counts items carried by players toward the nominal total |
| `count_in_cargo` | Counts items inside containers (crates, barrels, tents) toward nominal |
| `count_in_hoarder` | Counts items in stash-type storage (buried stashes, large containers) toward nominal |
| `crafted` | Marks this item as craft-generated; affects certain CE handling |
| `deloot` | Marks item for deloot behavior; affects cleanup behavior in certain contexts |

**Important:** All four count flags affect how the CE perceives "how many exist." If `count_in_player=0`, items held by players don't count against nominal — meaning CE keeps spawning more even if players are loaded with them. For high-demand items, all four count flags are usually `1`.

---

### `<category name="...">`

**Type:** String — must match a `<category>` entry in `cfglimitsdefinition.xml`  
**What it does:** Assigns the item to a category group. Categories are used by mapgroupproto.xml loot containers to filter which items can appear inside them.

**Standard categories:**
- `tools`
- `containers`
- `clothes`
- `food`
- `weapons`
- `books`
- `explosives`
- `lootdispatch`

Only one category per item.

---

### `<usage name="..."/>`

**Type:** String — must match a `<usage>` entry in `cfglimitsdefinition.xml`  
**What it does:** Assigns the item to one or more spawn context channels. These channels determine which buildings and locations the item is eligible to appear in.

Multiple `<usage>` lines are allowed and mean "this item can appear in any of these contexts."

**Standard usage values:**
| Usage | Where it routes |
|---|---|
| `Military` | Military bases, barracks, checkpoints |
| `Police` | Police stations |
| `Medic` | Medical buildings |
| `Firefighter` | Fire stations |
| `Industrial` | Factories, warehouses |
| `Farm` | Barns, farm buildings |
| `Coast` | Coastal structures |
| `Town` | Town buildings (shops, houses) |
| `Village` | Small village buildings |
| `Hunting` | Hunting cabins, stands |
| `Office` | Office buildings |
| `School` | Schools |
| `Prison` | Prison buildings |
| `Lunapark` | Amusement park structures |
| `SeasonalEvent` | Seasonal content |
| `ContaminatedArea` | Static contaminated zones |
| `ContaminatedArea_Dynamic` | Dynamic contaminated zones |
| `Underground` | Underground areas |
| `Special` | Special placement contexts |

**Most common mistake:** Item has high nominal but wrong or missing usage — it either never spawns or only spawns in wrong locations.

---

### `<value name="Tier..."/>`

**Type:** String — must match a `<value>` entry in `cfglimitsdefinition.xml`  
**What it does:** Restricts which map tiers an item can appear in. Tiers correspond to geographic zones on each map — Tier1 being the least dangerous and Tier4 being the most dangerous (deep military).

| Value | Typical Zone |
|---|---|
| `Tier1` | Coastal/starter areas |
| `Tier2` | Inland civilian areas |
| `Tier3` | Military adjacent / mid-tier |
| `Tier4` | Deep military / high-end loot |
| `Unique` | Special items, typically only one location |

Multiple `<value>` lines are allowed and mean "this item can appear in any of these tiers."

**Most common mistake:** Military-grade item has only `Tier1` assigned — it appears in civilian areas or the map's loot zoning pushes it to wrong buildings. Conversely, a common item assigned only `Tier4` will only appear in deep military zones.

---

## What Console Admins Typically Adjust

1. **`nominal` and `min`** — increasing them to boost how often something appears. Always keep min < nominal.
2. **`lifetime`** — reducing it for items that clutter the map, increasing it for items you want to persist longer.
3. **`usage`** — adding or removing usage channels to change where items can appear.
4. **`value`** — adding or removing tier assignments to change which zones items appear in.
5. **`quantmin` / `quantmax`** — tuning ammo count in spawned magazines.

---

## Safe Edit Pattern

1. Start with `nominal` only. Change nothing else first.
2. Increase in 10–25% steps and test between changes.
3. Never set `min` equal to or greater than `nominal`.
4. When adding a new `usage`, verify that usage name exists in cfglimitsdefinition.xml.
5. When adding a new `value`, verify that tier name exists in cfglimitsdefinition.xml.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Misspelled classname in `name=` | Item is silently ignored, never spawns |
| `min >= nominal` | CE behavior breaks for this item |
| Wrong or missing `<usage>` | Item has high nominal but doesn't appear where expected |
| Wrong `<value>` tier | Item appears in wrong zones or not at all |
| Usage/category names that don't exist in cfglimitsdefinition.xml | Entry is ignored |
| Setting all count flags to 0 | CE thinks the world is empty of this item and keeps spawning endlessly |
| Raising nominal very high without enough loot points in mapgroupproto.xml | CE can't place extras, behavior appears unchanged |
