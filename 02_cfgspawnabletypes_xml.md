---
title: "cfgspawnabletypes.xml"
nav_order: 1
parent: "Mission Root Config"
---

# cfgspawnabletypes.xml

## Purpose

`cfgspawnabletypes.xml` defines what spawns *with* an item when it appears in the world — its attachments, the contents of its cargo, and the damage state it spawns in. It does not control whether an item spawns or how many exist; that is `types.xml`. This file controls the *quality and loadout* of items when they do spawn.

If a weapon has no entry here, it spawns completely bare with no attachments. If a container has no cargo entry, it spawns empty.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgspawnabletypes.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgspawnabletypes.xml    (PlayStation)
```

## Connects To

- **types.xml (db/)** — every `name=` in this file must match a classname in types.xml
- **cfgrandompresets.xml** — `preset=` references point to named presets defined there

---

## Full Block Structure

```xml
<spawnabletypes>

  <!-- Weapon with attachments and condition -->
  <type name="M4A1">
    <attachments chance="0.50">
      <item name="M4_RISHndgrd" chance="0.35" />
      <item name="M4_OEBttstck" chance="0.25" />
      <item name="M4_CQBBttstck" chance="0.20" />
    </attachments>
    <attachments chance="0.30">
      <item name="ACOGOptic" chance="0.10" />
      <item name="ReflexOptic" chance="0.20" />
    </attachments>
    <damage min="0.0" max="0.3" />
  </type>

  <!-- Container with cargo preset -->
  <type name="GunCase_Medium">
    <cargo chance="0.70" preset="MilitaryWeaponsAmmo" />
    <damage min="0.0" max="0.2" />
  </type>

  <!-- Container with inline cargo items -->
  <type name="Backpack_CoatPack">
    <cargo chance="0.60">
      <item name="BandageDressing" chance="0.50" />
      <item name="Morphine" chance="0.20" />
    </cargo>
  </type>

</spawnabletypes>
```

---

## Field-by-Field Reference

### `<type name="...">`

The classname of the item whose spawn package you are configuring. Must exactly match the classname in types.xml. Case-sensitive.

Only one `<type>` block per classname. If an item does not have an entry here, it spawns bare with engine defaults.

---

### `<attachments chance="...">`

**`chance`:** Float 0.0–1.0  
The probability this entire attachment slot group is evaluated at all. `0.50` means there is a 50% chance this block of possible attachments is even considered when the item spawns.

Multiple `<attachments>` blocks on the same item represent different attachment slot groups (e.g. one block for handguard/stock, another for optics). Each block is evaluated independently with its own chance.

```xml
<attachments chance="0.50">
  <item name="M4_RISHndgrd" chance="0.35" />
  <item name="M4_OEBttstck" chance="0.25" />
</attachments>
```

When this block fires (50% chance), the engine then rolls against each `<item>` inside it.

---

### `<item name="..." chance="...">` (inside attachments)

| Attribute | Type | What it controls |
|---|---|---|
| `name` | String | Classname of the attachment candidate |
| `chance` | Float 0.0–1.0 | Probability this specific attachment is selected |

The items inside an `<attachments>` block are rolled against each other. A higher chance item is more likely to be selected. The engine selects at most one item per attachment block.

**Note:** The chances inside a block do not need to sum to 1.0. If no item is selected (all chances fail), the slot spawns empty.

---

### `<cargo chance="..." preset="...">`

Defines what spawns inside a container when it appears in the world.

**Two forms:**

**1. Preset reference:**
```xml
<cargo chance="0.70" preset="MilitaryWeaponsAmmo" />
```
- `chance` — probability this cargo block fires at all (0.0–1.0)
- `preset` — name of a cargo preset defined in `cfgrandompresets.xml`

**2. Inline items:**
```xml
<cargo chance="0.60">
  <item name="BandageDressing" chance="0.50" />
  <item name="Morphine" chance="0.20" />
</cargo>
```
Each `<item>` inside is rolled independently. Multiple items can be selected in the same cargo block.

Multiple `<cargo>` blocks can exist on one type. Each is evaluated with its own `chance`.

---

### `<damage min="..." max="...">`

**`min`:** Float 0.0–1.0 — minimum damage applied on spawn  
**`max`:** Float 0.0–1.0 — maximum damage applied on spawn

`0.0` = pristine condition  
`1.0` = completely ruined

The engine picks a random value between min and max and applies it as the item's damage state on spawn.

```xml
<damage min="0.0" max="0.3" />
```
This spawns the item between pristine and lightly worn.

**Note:** Setting both to `0.0` forces pristine spawns for that specific item, overriding the global `LootDamageMin`/`LootDamageMax` in globals.xml.

---

### `<tag name="...">` and `<hoarder>`

Less commonly edited. `<tag>` assigns context tags that interact with loot point filtering. `<hoarder>` marks the item as a hoarder-class item for CE counting purposes. Leave these at their defaults in normal operations.

---

## What Console Admins Typically Adjust

1. **`<damage min="..." max="..."/>`** — set both to `0.0` on boosted servers for pristine spawning items.
2. **`<attachments chance="...">`** — raise to make fully kitted weapons more common.
3. **`<item chance="...">`** inside attachments — adjust individual attachment selection rates.
4. **`<cargo chance="...">`** — raise to make containers spawn more reliably pre-filled.

---

## Safe Edit Pattern

1. Change damage range first — safest, lowest impact.
2. Raise attachment chances in small steps (e.g. 0.40 → 0.60).
3. Never set attachment chance to `1.0` on every item — creates guaranteed full-kit spawns across the board.
4. Always match the `name=` to an existing classname. Mismatched names are silently ignored.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| `name=` doesn't match types.xml classname | Entry silently ignored |
| Attachment chance `1.0` on everything | Every weapon spawns fully kitted — economy breaks |
| Referenced `preset=` doesn't exist in cfgrandompresets.xml | Cargo block is silently ignored |
| Multiple blocks with combined high chances | Near-guaranteed full loadout items on every spawn |
