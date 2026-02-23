# CB — Main Menu

## Overview

The main menu is the first screen the player reaches after the first-launch wizard (or directly on subsequent launches). Visual language matches the rest of the game: **clean and minimal**. Menu items are a vertical list. Background is a static or gently animated scene — specific art TBD.

---

## Menu Items

Listed top to bottom in display order:

| Item | Behaviour | State |
|------|-----------|-------|
| **New Game** | Opens the new playthrough creation flow — name the playthrough, set content tier, enable mods | Always enabled |
| **Load Game** | Opens the playthrough and save browser | **Greyed out** if no playthrough folders exist; enabled as soon as any save exists |
| **Mods** | Opens global mod management — install, remove, browse installed mods | Always enabled |
| **Settings** | Opens the settings menu (see [CB-settings-menu](CB-settings-menu.md)) | Always enabled |
| **Exit to Desktop** | Closes the game | Always enabled |

---

## Load Game — Greyed Out Condition

Load Game is disabled when no playthrough folders exist on disk. This covers:
- First launch before any New Game has been started
- A fresh install with no transferred saves

Once any playthrough folder exists — even one with only an autosave — Load Game becomes active. The greyed-out state uses the same visual treatment as other disabled UI elements in the game: reduced opacity, no hover highlight, no click response.

---

## SubscribeStar Icon

A small SubscribeStar icon is placed in one corner of the main menu screen — suggested placement: **bottom right**. It does not compete with the menu items.

**Behaviour:** clicking or selecting the icon opens the SubscribeStar page in the **system browser**. The game does not open an in-game browser. The URL is stored in a configuration file, not hardcoded, so it can be updated without a code change.

The icon should be visible but unobtrusive — present for players who want to support the project, not prominent enough to feel like a nag. A tooltip on hover: *"Support MGA on SubscribeStar"*.

On controller, the icon is reachable via the menu navigation flow — it appears as a focusable element after the last menu item, or can be accessed via a separate shoulder button shortcut.

---

## Build / Version Display

The build version and date are shown in a corner of the main menu — suggested placement: **bottom left**, opposite the SubscribeStar icon. Format:

```
v0.x.x — Build YYYY-MM-DD
```

This is especially important for weekly builds so players can confirm which version they are running before reporting issues. Text is small and muted — readable, not prominent.

---

## New Game Flow

Selecting **New Game** opens a short playthrough creation sequence:

1. **Name the playthrough** — default names auto-increment ("Playthrough 1", "Playthrough 2"); player can rename
2. **Content tier** — select from configured content categories (see [CB-settings-menu](CB-settings-menu.md) — Content section); inherits current global content settings as default
3. **Mods** — select which installed mods are active for this playthrough; mods can be changed later from within the playthrough
4. **Confirm** — creates the playthrough folder and starts the game from the prologue

If this is the very first launch and the first-launch wizard has just completed, step 2 is pre-filled from the wizard's content selections.

---

## Open Items

- Background art direction — static image, looping animation, or live scene from the game world
- Whether a **Continue** shortcut appears at the top of the menu once saves exist (loads most recent save directly, without opening the browser) — common QoL feature worth considering
- Credits — not in the current list; decide whether it lives in the main menu, the settings menu, or is omitted from early builds
- Controller navigation order — confirm shoulder button shortcut for SubscribeStar icon or rely on sequential navigation
- Whether the SubscribeStar icon animates or pulses on new supporter milestones (probably out of scope for early builds)
