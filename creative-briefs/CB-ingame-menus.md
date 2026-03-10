# Creative Brief — In-Game Menus

---

## Overview

During an active game session, the menu button (Esc/Tab / Menu button) opens one of two menu states depending on whether Meghan has her phone. The game detects phone status and opens the appropriate state automatically.

These are two of the three menu contexts in MGA — the third is the [Title Menu](CB-main-menu.md), which appears when no game session is active.

Full diegetic and design detail is in [UI/UX](../gdd/ui-ux.md). This brief defines the two in-game menu states, their contents, and the structure of the phone menu sections.

**Related documents:**

- [Title Menu](CB-main-menu.md) — title menu (no active session)
- [Settings Menu](CB-settings-menu.md) — settings accessible from all three menus
- [CB-hud-modification](CB-hud-modification.md) — widget configuration detail
- [CB-notes-and-discovery](CB-notes-and-discovery.md) — Notes/Missions separation
- [CB-tutorials](CB-tutorials.md) — OOC tutorials and phone app guides
- [CB-cutscenes](CB-cutscenes.md) — cutscene tiers and recording rules

---

## Use Cases

### UC1 — Accessing the Phone Menu

**Actor:** The player
**Goal:** Open Meghan's phone to access in-world apps and information
**Trigger:** Player presses the menu button while Meghan has her phone

**Step by step:**

1. **Meghan takes out her phone in-world.** The phone menu is fully in-fiction — time does not pause by default.
2. **The phone displays in-fiction sections:**

   | Section | Contents | Notes |
   |---------|----------|-------|
   | **HUD Modification** | Configure active widget sets for eye implants and CAMERA drone | See [CB-hud-modification](CB-hud-modification.md) |
   | **Map** | City map, points of interest, fast travel if unlocked | — |
   | **Notes** | Meghan's collected notes, lore entries, observations, and app guides | See UC4 |
   | **Missions** | Active and completed job contracts; tracks bounties, deliveries, repairs, hacking jobs, and waitressing shifts only — story progression is not tracked here | See [CB-notes-and-discovery](CB-notes-and-discovery.md) |
   | **Skills** | Full skill and attribute view; progression tracking | See [CB-skills-menu](CB-skills-menu.md) |
   | **Inventory** | Equipment, items, consumables | See [Inventory](../gdd/inventory.md) |
   | **Contacts** | NPCs Meghan has relationships with; communication log | — |
   | **Delivery App** | Courier job dispatch | Unlocked Chapter 1 |

3. **A System button is always visible on the phone screen.** It opens the out-of-fiction options — the same six items available in the Lost Phone Menu (see UC3).

**Phone access and time:** The world continues around Meghan while the phone is open. Accessing the phone in an unsafe environment is a player choice with real consequences. Players who prefer a safety net can enable **Pause in In-Game Menu** in Gameplay Settings (see [CB-settings-menu](CB-settings-menu.md)). Opt-in, off by default.

**The System button is the one deliberate break from full diegesis** within the phone menu. The phone is Meghan's; the System button is the player's. The player never needs to dismiss the phone to reach out-of-fiction options — they tap System, handle their business, and return to the phone's in-fiction sections.

---

### UC2 — Using the Lost Phone Menu

**Actor:** The player
**Goal:** Access out-of-character options when Meghan does not have her phone
**Trigger:** Player presses the menu button while Meghan's phone is missing

**Step by step:**

1. **The Lost Phone Menu opens.** Only out-of-character options are available — these are the only elements that break diegesis and are intentionally minimal.

   | Option | Function |
   |--------|----------|
   | **Save** | Manual save to current playthrough folder |
   | **Load** | Open the playthrough and save browser |
   | **Settings** | Open the settings menu |
   | **Tutorials** | Browse unlocked OOC tutorial entries — game mechanics reference |
   | **Memories** | Rewatch previously-seen cutscenes — see UC5 |
   | **Exit to Desktop** | Close the game |

2. **The player selects the option they need.**

**This menu is always reachable** regardless of phone status. It exists as a hardware-level input — Meghan's phone being absent does not prevent the player from saving, adjusting settings, or reviewing how a system works. The Tutorials entry provides access to the OOC mechanics encyclopedia — see [CB-tutorials](CB-tutorials.md).

---

### UC3 — Accessing OOC Options from the Phone Menu

**Actor:** The player
**Goal:** Reach Save, Load, Settings, Tutorials, Memories, or Exit without dismissing the phone
**Trigger:** Player taps the System button on the phone interface

**Step by step:**

1. **Player taps the System button** on the phone screen.
2. **The same six out-of-fiction options** from the Lost Phone Menu appear: Save, Load, Settings, Tutorials, Memories, Exit to Desktop.
3. **Player handles their business and returns to the phone's in-fiction sections.**

---

### UC4 — Browsing Notes

**Actor:** The player
**Goal:** Read Meghan's collected notes to find leads, learn about people, or review world information
**Trigger:** Player opens the Notes section from the phone menu

**Step by step:**

1. **Player navigates to the Notes section.** Five categories are available:
   - **Leads** — actionable information: things Meghan could investigate or act on. Includes location references (which create map pins) and time windows where applicable. Sourced from conversations, observations, job side-effects, and NPC contact
   - **People** — what Meghan knows about specific NPCs: personality notes, reliability assessments, observed connections to other people, intel gathered through investigation
   - **World** — background information: history, geography, factions, technology, corporate structure. Encyclopaedia-style reference material sourced from lore objects, terminals, documents, and conversations
   - **Field Notes** — Meghan's personal observations and reflections. Diary-style entries: her read on a situation, emotional reactions, gut feelings, pattern recognition
   - **App Guides** — built-in help files for each phone app; unlock progressively as Meghan gains access to apps and features; diegetic — Meghan reading her own phone's documentation. See [CB-tutorials](CB-tutorials.md) for the full entry list and unlock triggers

2. **Player selects a category and browses entries.** Notes are searchable by keyword across all categories.

**Notes are generated automatically on trigger** — conversation, observation, discovery, world event, job side-effect, NPC contact, or time-based reflection. The player never manually creates notes. They are read-only and serve as an in-world substitute for an external wiki.

**The Notes section is the primary story-discovery mechanism.** There is no quest log for the main story — the story reveals itself through notes that Meghan writes automatically based on what she experiences, observes, or is told. The player reads notes, connects the dots, and decides what to pursue. See [CB-notes-and-discovery](CB-notes-and-discovery.md) for the full design.

---

### UC5 — Rewatching a Cutscene (Memories)

**Actor:** The player
**Goal:** Replay a previously-seen cutscene from the Memories catalogue
**Trigger:** Player opens the Memories option from either the Lost Phone Menu or the System button

**Step by step:**

1. **Memories catalogue displays entries in story progression order,** grouped by chapter. Each entry shows: cutscene name, chapter label, first-seen timestamp (in-game date/time).
2. **Player selects an entry.** The cutscene replays using the recorded actor state snapshot — characters wear the clothing and equipment they had when the player first saw the scene.
3. **Same skip controls as live cutscenes.** No gameplay consequences fire during replay — items are not re-granted, flags are not re-set.

**Recording rules:** Cutscenes are added to the catalogue on first completion — whether the player watched in full or skipped. Skipping counts as completion; the player can return to it later. Exception: Ambient World Scenes (scripted NPC exchanges in the environment) record conditionally — only when the scene reaches a key narrative point before interruption. See [CB-cutscenes](CB-cutscenes.md) UC6 for recording rules per cutscene tier.

**Content-tiered cutscenes** replay at the tier that was active at the time of recording.

**Save data:** The Memories catalogue is stored per-playthrough in the save file using the extension-safe persistence architecture. If a cutscene asset is missing (removed or replaced in a future build), the entry shows a "content unavailable" placeholder.

See [Cutscenes](../gdd/cutscenes.md) for the technical architecture and [CB-cutscenes](CB-cutscenes.md) for the player experience of the three cutscene tiers.

---

## Lost Phone Scenarios

If Meghan's phone is taken or lost:
- All in-fiction sections become unavailable until the phone is recovered
- Eye implant widgets continue to function (loaded into implants, not streamed from phone)
- Widgets cannot be reconfigured without the phone
- The out-of-character options (Save, Load, Settings, Tutorials, Memories, Exit) remain available via the Lost Phone Menu

Lost phone scenarios may be scripted mission events or may occur organically through the pipeline capture system. See [UI/UX open items](../gdd/ui-ux.md).

---

## Open Items

- Time behaviour when phone is open — full speed / slowed / paused
- Phone menu navigation layout — tab-based, radial, or list; visual design TBD
- ~~Whether the out-of-character options in the phone menu appear as a dedicated section or are accessible via a separate hardware shortcut even while the phone is open~~ — RESOLVED: **System button** on the phone interface opens OOC options; always visible, no separate hardware shortcut needed
- ~~Notes section — how entries are triggered~~ → **Resolved:** automatic on trigger (conversation, observation, discovery, event, job side-effect, NPC contact, time-based reflection). See [CB-notes-and-discovery](CB-notes-and-discovery.md) UC1
- HUD Modification detail — covered in CB-hud-modification
