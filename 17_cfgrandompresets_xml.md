---
title: "cfgrandompresets.xml"
nav_order: 13
parent: "Mission Root Config"
---

# cfgrandompresets.xml

## Purpose

`cfgrandompresets.xml` defines named reusable presets for cargo contents and attachments. Instead of writing the same item list repeatedly in `cfgspawnabletypes.xml`, you define it once here as a named preset and reference it by name wherever you need it.

This file is a library. It does nothing on its own — it only activates when `cfgspawnabletypes.xml` references a preset name from it.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgrandompresets.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgrandompresets.xml    (PlayStation)
```

## Connects To

- **cfgspawnabletypes.xml** — `<cargo preset="...">` and `<attachments preset="...">` reference names defined here

---

## Full Block Structure

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<randompresets>

  <!-- Cargo preset — defines what can be inside a container -->
  <cargo name="MedKit" chance="1.0">
    <item name="BandageDressing" chance="0.80" />
    <item name="Morphine" chance="0.40" />
    <item name="BloodBagEmpty" chance="0.20" />
    <item name="Epinephrine" chance="0.15" />
    <item name="SalineBagIV" chance="0.10" />
  </cargo>

  <cargo name="MilitaryAmmo" chance="1.0">
    <item name="Mag_AK101_30Rnd" chance="0.50" />
    <item name="Mag_AKM_Drum75Rnd" chance="0.10" />
    <item name="Mag_M4A1_STANAG30Rnd" chance="0.40" />
    <item name="Mag_FAL_20Rnd" chance="0.20" />
  </cargo>

  <!-- Attachments preset — defines attachment candidates -->
  <attachments name="SniperOptics" chance="0.60">
    <item name="PUScopeOptic" chance="0.30" />
    <item name="KashtanOptic" chance="0.25" />
    <item name="ATOG4xOptic" chance="0.20" />
  </attachments>

</randompresets>
```

---

## Field-by-Field Reference

### `<cargo name="..." chance="...">`

A named cargo preset. When referenced in cfgspawnabletypes.xml with `<cargo preset="MedKit">`, this block is evaluated.

| Attribute | What it controls |
|---|---|
| `name` | Preset identifier — must match the `preset=` value in cfgspawnabletypes.xml exactly |
| `chance` | Probability this preset fires when selected. Usually `1.0` (always fires when selected). |

---

### `<attachments name="..." chance="...">`

A named attachments preset. Used the same way as cargo but for attachment slots.

| Attribute | What it controls |
|---|---|
| `name` | Preset identifier |
| `chance` | Probability this preset fires when selected |

---

### `<item name="..." chance="..." />` (inside presets)

One candidate item within the preset.

| Attribute | Type | What it controls |
|---|---|---|
| `name` | String | Classname of the item. Must exist in types.xml. |
| `chance` | Float 0.0–1.0 | Probability this specific item is selected |

For **cargo presets:** Each item is rolled independently. Multiple items can be selected in one cargo pass.

For **attachments presets:** Items compete — typically only one is selected per slot.

**Preset names must be unique** — you cannot have two `<cargo>` blocks with the same `name`. The validator will reject duplicates.

---

## How This Connects to cfgspawnabletypes.xml

In cfgspawnabletypes.xml:
```xml
<type name="FirstAidKit">
  <cargo chance="0.90" preset="MedKit" />
</type>
```

When a FirstAidKit spawns:
1. `chance="0.90"` — 90% chance the cargo block fires at all
2. If it fires, the engine looks up `MedKit` in cfgrandompresets.xml
3. Each item in the MedKit preset is rolled against its individual chance
4. Matching items are placed inside the spawned FirstAidKit

---

## What Console Admins Typically Adjust

1. **Item `chance` values inside presets** — tune how often specific items appear in a cargo group.
2. **Adding new items to an existing preset** — expand what can appear in a container type.
3. **Creating new presets** — define a cargo mix for a new container type you want to add to cfgspawnabletypes.xml.
4. **Removing items from a preset** — remove unwanted items from auto-spawned cargo.

---

## Safe Edit Pattern

1. Change one preset at a time.
2. Keep item classnames exact — wrong names are silently ignored.
3. Preset names are case-sensitive — `MedKit` and `medkit` are different presets.
4. Total combined chances per cargo preset don't need to sum to 1.0 — each item rolls independently.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Preset `name=` doesn't match what cfgspawnabletypes.xml references | Preset is defined but never used; or cfgspawnabletypes reference silently fails |
| Duplicate preset names | File validation error |
| Item classname not in types.xml | Item silently never spawns in that cargo |
| Very high chance on every item in a cargo preset | Containers always spawn nearly full |
