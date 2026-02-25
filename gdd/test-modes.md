# MGA — Test Game Modes

---

## Overview

MGA includes standalone test game modes that exist outside the main campaign. These modes serve two purposes: they are development sandboxes for building and validating core systems in isolation, and they ship with the game as playable content.

Each mode exercises a specific subset of the game's systems without the weight of the full campaign — no story dependencies, no chapter gating, no save-state complexity. Systems proven in test modes are promoted into the main game with confidence.

---

## Job Test Modes

Four dedicated test modes exist for the non-combat side jobs. Each mode isolates a single job and its associated systems for focused testing and iteration.

| Mode | Job | Primary Systems Tested |
|------|-----|----------------------|
| **Waitress Test** | Waitress at Burton's Bar | NPC interaction, order management, tray navigation, SP drain, Small Talk, Conflict Resolution |
| **Courier Test** | Courier / Delivery | Traversal, vehicle progression, city navigation, package integrity, time pressure, delivery app UI |
| **Mechanic Test** | Mechanic | Engineering puzzles, Repair skill, diagnostic Perception, tool requirements, client faction choices |
| **Hacker Test** | Hacker | Logic puzzle minigame, terminal traversal, Stealth, detection consequences, digital environment |

Each job test mode provides a stripped-down environment with just enough world geometry, NPCs, and economy to exercise the job's full loop — accept work, perform work, get paid, unlock progression.

Bounty hunting is excluded from the job test modes. Its combat-heavy nature means it is better tested through the main campaign's combat encounters and the standalone adventure mode below.

---

## Standalone Adventure Mode

A standalone mini-game mode built as a lightweight quest-driven adventure. The gameplay reference is the *Naked Order* / *Naked Adventure* genre — simple quest-loop games where the player picks up tasks, travels to locations, acquires items, and completes objectives. MGA's version uses that same structure as a systems testbed. Name TBD.

### Concept

The player controls a character in a simplified open environment with a basic quest system. The loop is: receive quests, travel to locations, find or purchase items, complete objectives, return. The structure deliberately mirrors the delivery side job's pickup-and-deliver pattern but adds combat encounters and quest variety.

### Systems Tested

| System | What Is Validated |
|--------|-------------------|
| **Clothing destruction** | Full degradation pipeline — damage thresholds, visual states, repair/replacement economy |
| **Quest system** | Quest dispatch, tracking, objectives, completion, reward delivery |
| **Enemy logic** | AI behaviour, patrol, detection, engagement, combat loop |
| **Content tier gating** | Different content visible/available depending on which DLC paks are loaded (Base, Spicy, Super Spicy) |

### Content Tier Gating

This mode is the primary testbed for the DLC content tier system. The same mode behaves differently based on which content packages are installed:

| Loaded Paks | Behaviour |
|-------------|-----------|
| **Base only** | Standard clothing, no destruction beyond cosmetic wear, no explicit content |
| **Base + Spicy** | Clothing destruction system active — combat-driven degradation, nudity states |
| **Base + Spicy + Super Spicy** | Full content — explicit scenes, additional quest content, expanded NPC interactions |

This allows validation that content tier boundaries work correctly: Spicy content does not leak into Base-only builds, Super Spicy content does not leak into Spicy-only builds, and the game functions cleanly at every tier.

### Design Constraints

- **No story dependencies** — the mode does not reference the main campaign's characters, plot, or world state
- **Self-contained economy** — currency and item progression exist only within the mode
- **Minimal world** — enough environment to test traversal, combat spaces, and quest destinations; not a full open world
- **Replayable** — the quest system can generate or cycle content for repeated testing without a fixed endpoint

---

## Development Strategy

Test modes are built **before** the corresponding systems are integrated into the main campaign. The workflow:

1. Build the system in isolation inside a test mode
2. Validate behaviour, performance, and edge cases
3. Promote the proven system into the main game
4. The test mode ships as bonus content

This approach aligns with the manifesto's [Systems Before Content](manifesto.md#2-systems-before-content) principle — systems must work before story is layered on top.

---

## Related

- [Core Loop](core-loop.md) — the five jobs and their progression
- [Content Architecture & DLC](content-and-dlc.md) — content tier definitions
- [Boot Manager](boot-manager.md) — how content paks are loaded and tiered
- [Scope](scope.md) — what ships with Game 1

---

## Open Items

- Name for the standalone adventure mode
- Whether test modes share the main game's save system or use independent saves
- Whether job test modes are available from the main menu or accessed through a separate launcher/menu
- Character used in the adventure mode — unique character, generic avatar, or Meghan in a non-canon context
- Whether test modes are included in the demo build, the full game only, or both
- Scope of the quest system — fully procedural, hand-authored pool, or hybrid
