# CB — In-Game Menus

## Overview

There are two possible in-game menu states depending on whether Meghan has her phone. Both are accessed via the same hardware input. The game detects phone status and opens the appropriate state automatically.

Full diegetic and design detail is in [UI/UX](../gdd/ui-ux.md). This brief defines the two menu states, their contents, and the structure of the phone menu sections.

---

## Two Menu States

### State 1 — Lost Phone Menu

Meghan does not have her phone. Only out-of-character options are available. These are the only elements that break diegesis and are intentionally minimal.

| Option | Function |
|--------|----------|
| **Save** | Manual save to current playthrough folder |
| **Load** | Open the playthrough and save browser |
| **Settings** | Open the settings menu |
| **Tutorials** | Browse unlocked OOC tutorial entries — game mechanics reference |
| **Exit to Desktop** | Close the game |

This menu is always reachable regardless of phone status. It exists as a hardware-level input — Meghan's phone being absent does not prevent the player from saving, adjusting settings, or reviewing how a system works. The Tutorials entry provides access to the OOC mechanics encyclopedia — see [CB-tutorials](CB-tutorials.md).

---

### State 2 — Phone Menu

Meghan has her phone. The phone menu contains all out-of-character options plus the full in-fiction menu. Meghan takes out her phone in-world; time does not pause.

#### Out-of-Character Section
Same five options as the Lost Phone Menu — Save, Load, Settings, Tutorials, Exit to Desktop — accessible from within the phone menu so the player never needs to dismiss the phone to reach them.

#### In-Fiction Sections

| Section | Contents | Notes |
|---------|----------|-------|
| **HUD Modification** | Configure active widget sets for eye implants and CAMERA drone | The most complex section — see [CB-hud-modification](CB-hud-modification.md) *(forthcoming)* |
| **Map** | City map, points of interest, fast travel if unlocked | — |
| **Notes** | Meghan's collected notes, lore entries, observations, and app guides | See detail below |
| **Missions** | Active and completed quest/job log; mission tracking | — |
| **Skills** | Full skill and attribute view; progression tracking | — |
| **Inventory** | Equipment, items, consumables | See [Inventory](../gdd/inventory.md) |
| **Contacts** | NPCs Meghan has relationships with; communication log | — |
| **Delivery App** | Courier job dispatch | Unlocked Chapter 1 |

---

## Notes Section

Meghan's Notes section contains four categories:

- **Field notes** — observations Meghan has written or recorded during missions; personal commentary on events
- **Lore entries** — world information collected through exploration, conversation, and items; encyclopaedia-style reference
- **Quest notes** — contextual notes attached to missions (separate from the mission log itself — the mission log tracks objectives; notes track context, leads, and Meghan's read of a situation)
- **App Guides** — built-in help files for each phone app; unlock progressively as Meghan gains access to apps and features; diegetic — Meghan reading her own phone's documentation. See [CB-tutorials](CB-tutorials.md) for the full entry list and unlock triggers

Notes are read-only from the phone menu — Meghan adds them through play, not by the player typing. They serve as an in-world substitute for an external wiki: players who pay attention to what Meghan collects have the context they need without leaving the game.

The Notes section is organised into the four categories above, searchable by keyword.

---

## Phone Access and Time

The phone is accessed in-world — Meghan takes it out, and the world continues around her. Accessing the phone in an unsafe environment is a player choice with real consequences.

By default, world time continues at full speed while the phone is open. The game's pacing is designed around this — players should not miss critical events while managing their menus under normal play conditions.

Players who prefer a safety net can enable **Pause in In-Game Menu** in Gameplay Settings (see [CB-settings-menu](CB-settings-menu.md)). This covers both the phone menu and the out-of-character menu. Opt-in, off by default.

---

## Lost Phone Scenarios

If Meghan's phone is taken or lost:
- All in-fiction sections become unavailable until the phone is recovered
- Eye implant widgets continue to function (loaded into implants, not streamed from phone)
- Widgets cannot be reconfigured without the phone
- The out-of-character options (Save, Load, Settings, Tutorials, Exit) remain available via the Lost Phone Menu

Lost phone scenarios may be scripted mission events or may occur organically through the pipeline capture system. See [UI/UX open items](../gdd/ui-ux.md).

---

## Open Items

- Time behaviour when phone is open — full speed / slowed / paused
- Phone menu navigation layout — tab-based, radial, or list; visual design TBD
- Whether the out-of-character options in the phone menu appear as a dedicated section or are accessible via a separate hardware shortcut even while the phone is open
- Notes section — how entries are triggered (automatic on discovery, manual flag, both?)
- HUD Modification detail — covered in CB-hud-modification *(forthcoming)*
