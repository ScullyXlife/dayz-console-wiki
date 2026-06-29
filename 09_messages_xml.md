---
title: "messages.xml"
nav_order: 5
parent: "Core Economy — db/"
---

# messages.xml

## Purpose

`messages.xml` controls automated text broadcasts that appear in server chat. These are the server tips, rules reminders, Discord links, and restart countdown warnings that players see periodically while playing.

## Console Path

```
dayzxb_missions/dayzOffline.<mapname>/db/messages.xml    (Xbox)
dayzps_missions/dayzOffline.<mapname>/db/messages.xml    (PlayStation)
```

---

## Full Block Structure

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<messages>

  <!-- Recurring welcome message -->
  <message>
    <delay>0</delay>
    <repeat>15</repeat>
    <onconnect>1</onconnect>
    <text>Welcome to #name — Boosted Loot | Active Admins</text>
  </message>

  <!-- Recurring Discord line -->
  <message>
    <delay>0</delay>
    <repeat>30</repeat>
    <onconnect>1</onconnect>
    <text>Join our Discord: discord.gg/yourlink</text>
  </message>

  <!-- Recurring rules reminder -->
  <message>
    <delay>5</delay>
    <repeat>60</repeat>
    <onconnect>0</onconnect>
    <text>No cheating. No harassment. Respect all players.</text>
  </message>

  <!-- Restart countdown warning -->
  <message>
    <delay>0</delay>
    <deadline>10</deadline>
    <onconnect>0</onconnect>
    <text>Server restart in #tmin minutes. Find cover!</text>
  </message>

</messages>
```

---

## Field-by-Field Reference

### `<message>`

One message rule. Each rule is independent. A file can have as many rules as needed.

---

### `<delay>`

**Type:** Integer (minutes)  
How many minutes after server start (or after the previous message in some implementations) before this rule begins evaluating.

- `0` — rule starts evaluating immediately at server start
- `5` — rule waits 5 minutes before first evaluation

Use `delay` to stagger messages so they don't all fire at the same time when the server starts.

---

### `<repeat>`

**Type:** Integer (minutes)  
How often this message repeats, in minutes.

- `15` — message appears every 15 minutes
- `30` — message appears every 30 minutes
- `0` — message fires once and does not repeat

**Note:** `repeat` and `deadline` should not be used on the same `<message>` block. Use one or the other.

---

### `<deadline>`

**Type:** Integer (minutes)  
Used for countdown-style messages — typically restart warnings. The message fires at this many minutes before a scheduled restart/shutdown.

`#tmin` in the text is replaced with the remaining minutes, counting down.

```xml
<deadline>10</deadline>
<text>Server restart in #tmin minutes.</text>
```

This message fires when 10 minutes remain, showing "Server restart in 10 minutes", then again at 9, 8, etc., down to 0.

**Note:** `deadline` is used independently of `repeat`. Do not use both in the same message block.

---

### `<onconnect>`

**Type:** Integer (0 or 1) or boolean  
Whether this message is shown to a player immediately when they connect to the server, in addition to its normal schedule.

- `1` — message appears in chat immediately when a player joins (good for welcome and rules messages)
- `0` — message only fires on its normal schedule, not on connect

---

### `<shutdown>`

**Type:** Integer (0 or 1) or boolean  
Used with `<deadline>` to trigger a server shutdown when the countdown reaches zero. Rarely used in normal console operations since Nitrado manages restarts on its own schedule.

---

### `<text>`

The message string shown in game chat.

**Token substitutions:**

| Token | What it becomes |
|---|---|
| `#name` | The server's hostname as configured in Nitrado settings |
| `#tmin` | Remaining minutes (used with `<deadline>`) |

**XML character escaping:** If your message contains any of these characters, they must be escaped:

| Character | Escaped form |
|---|---|
| `&` | `&amp;` |
| `<` | `&lt;` |
| `>` | `&gt;` |

**Character limit:** Keep messages short. Long messages truncate in the in-game chat window.

---

## What Console Admins Typically Adjust

1. **`<text>`** — update server name, Discord link, rules text
2. **`<repeat>`** — tune how often messages appear (avoid spam)
3. **`<onconnect>`** — add for messages that should greet new joiners
4. **Adding/removing `<message>` blocks** — add a new broadcast, remove one you no longer want

---

## Practical Structure for Clean Server Messaging

```
1 welcome line       → repeat 10-20, onconnect 1
1 Discord line       → repeat 20-30, onconnect 1
1 rules reminder     → repeat 30-60, onconnect 0
1 restart warning    → deadline 10-15, onconnect 0
```

---

## Safe Edit Pattern

1. Stagger `delay` values across recurring messages so they don't fire simultaneously.
2. Stagger `repeat` intervals so they don't land on the same minute every cycle.
3. Keep `repeat` intervals at 15 minutes minimum — lower than that becomes chat spam.
4. Change one message at a time and rejoin to verify.

---

## Common Mistakes

| Mistake | Result |
|---|---|
| `repeat` set very low (1–5 minutes) on multiple messages | Chat spam, players mute server messages |
| Using both `repeat` and `deadline` on same block | Undefined behavior |
| `#name` used but hostname not set in Nitrado settings | Token shows placeholder or blank |
| Special characters (`&`, `<`) unescaped in text | File parse error, messages may not load |
| Long text strings | Text truncates mid-sentence in game chat |
