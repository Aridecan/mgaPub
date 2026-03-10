# Creative Brief — Main Menu

---

## Overview

MGA has **three menu contexts** that the player encounters depending on game state. All share the same hardware input (Esc/Tab / Menu button) but present different options based on context.

| Menu | When | Fiction | Contents |
|------|------|---------|----------|
| **Title Menu** | No active game session (pre-game, between sessions) | Out of fiction | Continue, New Game, Load, Mods, Settings, Exit |
| **Phone Menu** | Active session, Meghan has her phone | In fiction | In-world apps + System button (leads to OOC options) |
| **Lost Phone Menu** | Active session, Meghan's phone is missing | Out of fiction | Save, Load, Settings, Tutorials, Memories, Exit |

The Phone Menu and Lost Phone Menu are documented in detail in [In-Game Menus](CB-ingame-menus.md). This brief covers the **Title Menu** — the first screen the player sees.

**Related documents:**

- [In-Game Menus](CB-ingame-menus.md) — Phone Menu and Lost Phone Menu (active session)
- [Settings Menu](CB-settings-menu.md) — settings accessible from all three menus
- [Mod Manager](CB-mod-manager.md) — mod acquisition, load order, per-playthrough activation
- [Boot Manager](../gdd/boot-manager.md) — ABM/MBM lifecycle, first-launch sequence

---

## Use Cases

### UC1 — Continuing a Game

**Actor:** The player
**Goal:** Resume the most recent session with a single click
**Trigger:** Player selects Continue from the title menu

**Step by step:**

1. **Continue appears above New Game** once at least one save exists. It is hidden until then.
2. **Context is shown directly below the button** in small muted text:
   ```
   Continue
     Playthrough 1 · Chapter 2 · Saved 14 Feb 2331, 21:43
   ```
3. **Selecting Continue loads without any additional confirmation** — one click, straight into the game. No save browser required.

**Players managing multiple playthroughs** can see at a glance whether Continue is pointing at the right one; if not, they use Load Game to select a different playthrough instead.

---

### UC2 — Starting a New Game

**Actor:** The player
**Goal:** Create a new playthrough with a name, content tier, and mod selection
**Trigger:** Player selects New Game from the title menu

**Step by step:**

1. **Name the playthrough.** Default names auto-increment ("Playthrough 1", "Playthrough 2"); player can rename.
2. **Content tier.** Select from configured content categories (see [CB-settings-menu](CB-settings-menu.md) — Content section); inherits current global content settings as default.
3. **Mods.** Select which installed mods are active for this playthrough; mods can be changed later from within the playthrough.
4. **Confirm.** Creates the playthrough folder and starts the game from the prologue.

**First launch shortcut:** If this is the very first launch and the first-launch wizard has just completed, step 2 is pre-filled from the wizard's content selections.

---

### UC3 — Loading a Saved Game

**Actor:** The player
**Goal:** Browse playthroughs and saves to load a specific session
**Trigger:** Player selects Load Game from the title menu

**Step by step:**

1. **Load Game opens the playthrough and save browser.** All existing playthroughs are listed with their saves.
2. **Player selects a playthrough, then a save within it.**
3. **The selected save loads.**

**Greyed-out condition:** Load Game is disabled when no playthrough folders exist on disk — first launch before any New Game has been started, or a fresh install with no transferred saves. Once any playthrough folder exists — even one with only an autosave — Load Game becomes active. The greyed-out state uses the same visual treatment as other disabled UI elements: reduced opacity, no hover highlight, no click response.

---

## Title Menu Reference

### Menu Items

Listed top to bottom in display order:

| Item | Behaviour | State |
|------|-----------|-------|
| **Continue** | Loads the most recent save from the most recent playthrough — no browser required | **Hidden** until at least one save exists; appears above New Game once a save is present |
| **New Game** | Opens the new playthrough creation flow — name the playthrough, set content tier, enable mods | Always enabled |
| **Load Game** | Opens the playthrough and save browser | **Greyed out** if no playthrough folders exist; enabled as soon as any save exists |
| **Mods** | Opens global mod management — install, remove, browse installed mods | Always enabled |
| **Settings** | Opens the settings menu (see [CB-settings-menu](CB-settings-menu.md)) | Always enabled |
| **Exit to Desktop** | Closes the game | Always enabled |

### Visual Design

Visual language matches the rest of the game: **clean and minimal**. Menu items are a vertical list. Background is a static or gently animated scene — specific art TBD.

### SubscribeStar Icon

A small SubscribeStar icon is placed in one corner of the main menu screen — suggested placement: **bottom right**. It does not compete with the menu items.

**Behaviour:** clicking or selecting the icon opens the SubscribeStar page in the **system browser**. The game does not open an in-game browser. The URL is stored in a configuration file, not hardcoded, so it can be updated without a code change.

The icon should be visible but unobtrusive — present for players who want to support the project, not prominent enough to feel like a nag. A tooltip on hover: *"Support MGA on SubscribeStar"*.

On controller, the icon is reachable via the menu navigation flow — it appears as a focusable element after the last menu item, or can be accessed via a separate shoulder button shortcut.

### Build / Version Display

The build version and date are shown in a corner of the main menu — suggested placement: **bottom left**, opposite the SubscribeStar icon. Format:

```
v0.x.x — Build YYYY-MM-DD
```

This is especially important for weekly builds so players can confirm which version they are running before reporting issues. Text is small and muted — readable, not prominent.

---

## Open Items

- Background art direction — static image, looping animation, or live scene from the game world
- ~~Whether a **Continue** shortcut appears~~ — confirmed, added above New Game; loads most recent save with context subtitle
- Credits — not in the current list; decide whether it lives in the main menu, the settings menu, or is omitted from early builds
- Controller navigation order — confirm shoulder button shortcut for SubscribeStar icon or rely on sequential navigation
- Whether the SubscribeStar icon animates or pulses on new supporter milestones (probably out of scope for early builds)
