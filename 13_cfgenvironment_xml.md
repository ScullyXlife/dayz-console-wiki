---
title: "cfgenvironment.xml"
nav_order: 8
parent: "Mission Root Config"
---

# cfgenvironment.xml

## Purpose

`cfgenvironment.xml` registers territory files and their agent/spawn behavior with the engine. It does not define zone geometry itself — the zone shapes live in the `env/*_territories.xml` files this document registers.

If a registered path is wrong, the entire territory category for that creature type silently stops working — no zombies in a zone, no wolves, etc. — with no obvious in-game error.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/cfgenvironment.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/cfgenvironment.xml    (PlayStation)
```

## Connects To

- **env/\*\_territories.xml** — the file registry at the top of this document, plus the `<file usable="...">` links inside each territory, point to these files
- **types.xml (db/)** — creature classnames referenced here must exist there

---

## Full Block Structure

The installed root is `<env><territories>`, not the flat `<environment>` structure some older guides describe. There is a top-level file registry, and each territory block links to a file with a `usable` attribute rather than a bare `path`:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<env>
  <files>
    <file name="env/zombie_territories.xml" />
    <file name="env/wolf_territories.xml" />
    <!-- one <file> per env/*_territories.xml actually installed -->
  </files>

  <territories>
    <territory name="InfectedZone">
      <file usable="env/zombie_territories.xml" />
      <agent type="Male" chance="3">
        <spawn configName="Village" chance="1.0" />
      </agent>
      <agent type="Female" chance="1">
        <spawn configName="Village" chance="1.0" />
      </agent>
    </territory>

    <territory name="AnimalWolf">
      <file usable="env/wolf_territories.xml" />
      <agent type="Wolf_Grey" chance="10">
        <spawn configName="Hunting" chance="1.0" />
      </agent>
      <agent type="Wolf_Black" chance="3">
        <spawn configName="Hunting" chance="1.0" />
      </agent>
    </territory>
  </territories>
</env>
```

**This is a structural correction, not a cosmetic one.** The previous `<environment><territory type="spawn" name="..." behavior="...">` shape with a single `<file path="...">` per block, `<item name="..." val="...">` control values, and 0.0–1.0 agent `chance` was not confirmed against the installed file and has been removed.

---

## Field-by-Field Reference

### `<files>` / `<file name="...">`

Top-level registry of every territory file this mission actually uses. Add an entry here for every `env/*_territories.xml` file you want the engine to load.

---

### `<territory name="...">`

One wired territory system.

| Attribute | What it controls |
|---|---|
| `name` | Identifier for this territory block. Used in logs. |

---

### `<file usable="..." />`

Links this territory block to one of the files declared in the top-level `<files>` registry. Path is relative to the mission root.

```xml
<file usable="env/zombie_territories.xml" />
```

**If this reference is wrong**, the territory block loads with no zones — that creature type will not spawn from territories at all. There is no visible in-game error.

---

### `<agent type="..." chance="...">`

Defines which agent labels can be selected within this territory and their relative weight.

| Attribute | What it controls |
|---|---|
| `type` | An agent label (e.g. `Male`, `Female`), **not necessarily a direct creature classname**. The actual spawn class/configuration comes from the nested `<spawn configName="...">`. For territories using literal classnames (e.g. `Wolf_Grey`), the classname must exist in types.xml. |
| `chance` | A selection weight. **Installed files use values greater than 1** (observed values include 3, 10, and 20) — these are not normalized 0.0–1.0 probabilities. Treat them as relative weights until the native selection formula is separately confirmed. |

Multiple `<agent>` entries allow variety — e.g. male and female infected, or grey and black wolves, each with their own weight.

#### `<spawn configName="..." chance="..." />`

Inside each `<agent>`, this defines which spawn configuration is used and with what probability.
- `configName` — spawn context (matches usage contexts like `Village`, `Hunting`, `Military`, etc.)
- `chance` — probability of using this spawn config when this agent fires

---

## What Console Admins Typically Adjust

1. **Agent `chance` weights** — adjust distribution between agent variants. Remember these are weights, not 0–1 probabilities.
2. **`<file usable="...">`** — if you rename a territory file, update this and the top-level `<files>` registry to match.
3. **Adding new `<agent>` entries** — add a new variant to an existing territory.

---

## Safe Edit Pattern

1. Never rename a territory file without updating both the top-level `<files>` registry and every `<file usable="...">` reference to it.
2. Verify classnames used directly in `type=` exist in types.xml before adding new agents.
3. Test after every path change by checking the RPT log for territory load errors.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| Wrong `<file usable="...">` or missing top-level `<files>` entry | Entire territory category silently disabled |
| Treating `chance` as a 0.0–1.0 probability | Weighting comes out wrong versus what you intended — installed values go well above 1 |
| Agent `type=` classname not in types.xml (for territories using literal classnames) | That variant never spawns |
| Path uses backslashes instead of forward slashes | May fail to resolve on server |

---

## Confidence Note

The document root, top-level file registry, `<file usable="...">` link form, and non-normalized agent `chance` values are confirmed against an installed mission. The exact selection formula behind `chance` weighting (how the engine converts weights like 3/10/20 into a probability) is not independently confirmed — treat the weighting mechanism as observed, not proven.
