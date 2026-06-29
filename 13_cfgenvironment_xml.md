---
title: "cfgenvironment.xml"
nav_order: 8
parent: "Mission Root Config"
---

# cfgenvironment.xml

## Purpose

`cfgenvironment.xml` is the wiring file that connects all territory files to the engine. It does not define zones itself — it points the engine to the territory files in the `env/` folder and tells it what behavior and AI type each file governs.

If this file has an incorrect path, the entire territory category for that creature type silently stops working — no zombies in a zone, no wolves, etc. — with no obvious error.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgenvironment.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgenvironment.xml    (PlayStation)
```

## Connects To

- **env/*_territories.xml** — every `<file path="...">` reference here points to one of these files
- **types.xml (db/)** — creature classnames referenced here must exist there

---

## Full Block Structure

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<environment>

  <territory type="spawn" name="InfectedZone" behavior="InfectedBehavior">
    <file path="env/zombie_territories.xml" />
    <agent type="ZmbM_CitizenASkinny_Brown" chance="0.15">
      <spawn configName="Village" chance="1.0" />
    </agent>
    <agent type="ZmbM_CitizenBSkinny_Brown" chance="0.10">
      <spawn configName="Village" chance="1.0" />
    </agent>
    <item name="maxCount" val="500" />
    <item name="spawnRadius" val="15" />
    <item name="spawnDist" val="80" />
    <item name="activeRadius" val="60" />
  </territory>

  <territory type="spawn" name="AnimalWolf" behavior="WildAnimalBehavior">
    <file path="env/wolf_territories.xml" />
    <agent type="Wolf_Grey" chance="0.60">
      <spawn configName="Hunting" chance="1.0" />
    </agent>
    <agent type="Wolf_Black" chance="0.40">
      <spawn configName="Hunting" chance="1.0" />
    </agent>
    <item name="minCount" val="2" />
    <item name="maxCount" val="4" />
    <item name="spawnRadius" val="10" />
  </territory>

</environment>
```

---

## Field-by-Field Reference

### `<territory type="..." name="..." behavior="...">`

One wired territory system.

| Attribute | What it controls |
|---|---|
| `type` | Territory system type. `spawn` is standard for AI population territories. |
| `name` | Identifier for this territory block. Used in logs. |
| `behavior` | AI behavior controller. `InfectedBehavior` for zombies, `WildAnimalBehavior` for animals. |

---

### `<file path="..." />`

**This is the most critical element in the file.**

Points to the territory file containing the zone definitions. Path is relative to the mission root.

```xml
<file path="env/zombie_territories.xml" />
```

**If this path is wrong** (wrong filename, wrong folder, typo), the entire territory block loads with no zones — that creature type will not spawn from territories at all. There is no visible error in the game; it simply does not work.

---

### `<agent type="..." chance="...">`

Defines which specific creature classnames can spawn from this territory and with what probability.

| Attribute | What it controls |
|---|---|
| `type` | Exact creature classname. Must exist in types.xml. |
| `chance` | Probability this agent variant is selected when the territory spawns a creature (0.0–1.0) |

Multiple `<agent>` entries allow variety — e.g. brown wolves and black wolves both spawn from the same wolf territory, each with their own chance.

Chances across all agents in a territory block don't need to sum to 1.0. Each agent is evaluated independently.

#### `<spawn configName="..." chance="..." />`

Inside each `<agent>`, this defines which spawn configuration the creature uses and with what probability.
- `configName` — spawn context (matches usage contexts like `Village`, `Hunting`, `Military`, etc.)
- `chance` — probability of using this spawn config when this agent fires

---

### `<item name="..." val="..." />`

Behavior configuration values for this territory system. Common items:

| name | What it controls |
|---|---|
| `maxCount` | Maximum simultaneous creatures in all zones of this territory type |
| `minCount` | Minimum creatures maintained in zones |
| `spawnRadius` | Radius around a zone center point where creatures spawn (meters) |
| `spawnDist` | Minimum distance from players for spawn to trigger (meters) |
| `activeRadius` | Radius within which creatures stay active (meters) |

---

## What Console Admins Typically Adjust

1. **Agent `chance` values** — adjust distribution between creature variants (e.g. more grey wolves, fewer black).
2. **`<item name="maxCount">`** — cap total active population for this territory type.
3. **`<file path="...">`** — if you rename a territory file, update this to match.
4. **Adding new `<agent>` entries** — add a new creature variant to an existing territory.

---

## Safe Edit Pattern

1. Never rename a territory file without simultaneously updating this file's `<file path="...">`.
2. Verify classnames in `type=` exist in types.xml before adding new agents.
3. Test after every path change by checking the RPT log for territory load errors.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Wrong `<file path="...">` | Entire territory category silently disabled |
| Agent `type=` not in types.xml | That variant never spawns |
| Path uses backslashes instead of forward slashes | May fail to resolve on server |
| Removing an agent without adjusting other chances | Remaining agents still work but variety is reduced |
