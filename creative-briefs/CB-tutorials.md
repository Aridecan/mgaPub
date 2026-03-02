# CB — Offline Encyclopedia / Tutorial System

## Overview

The prologue teaches 11 systems in rapid succession through guided story beats. Once Chapter 1 begins, the rails come off entirely. Players who didn't fully absorb a system during the prologue have no way to revisit the explanation — they must either figure it out or look up an external wiki.

This system gives players a persistent reference they can consult at their own pace, without adding more beats to the already-dense prologue.

---

## Two-Tier Structure

The system is split into two tiers by what they cover and where they live:

| Tier | Name | Location | Available when |
|------|------|----------|----------------|
| **1** | OOC Tutorials | Out-of-character menu (5th entry) | Always — including when the phone is lost |
| **2** | Phone Tutorials (App Guides) | Notes section of the phone menu (4th category) | Only when Meghan has her phone |

No duplication between tiers — each explains its own domain.

---

## Tier 1 — OOC Tutorials (Mechanics)

A 5th entry in the out-of-character menu, alongside Save / Load / Settings / Exit. Available at all times, including when the phone is lost.

### What it covers

Game mechanics that exist independent of the phone — movement, camera modes, combat, stealth, traversal, economy concepts, clothing damage, personal shield, time system, dialogue controls, bed/time skip, DLC content tiers.

### Why OOC

These are "how to play the game" entries. They break diegesis the same way Save/Load does — they exist because the player needs them, not because Meghan needs them. Putting them in the phone would mean losing access to "how do I fight" when the phone is taken, which is exactly when the player might need it most.

### Visual style

Matches the existing OOC menu — clean, minimal, not themed to the game world.

### Progressive unlock

Entries unlock after the prologue beat or Chapter 1 moment that first introduces the system. The player cannot read ahead to systems they haven't encountered yet.

### Entry list

| Entry | Unlocks after | Covers |
|-------|---------------|--------|
| Movement & Camera | Beat 1 (shuttle) | WASD, camera controls, 1st/3rd person switching, movement differences per camera mode |
| Interaction (Action Key) | Beat 1 (shuttle) | Action Key usage, context-sensitive interactions |
| Inventory Basics | Beat 2 (immigration) | Carry weight, hand slots, clothing storage, Give action |
| Dialogue & Choices | Beat 2–3 (immigration / lounge) | Dialogue interaction, branching choices, skipping, fast-forward |
| Stealth & Cameras | Beat 4 (port hallways) | Camera facing, coverage zones, sneaking movement, detection rules |
| Traversal & Jumping | Beat 5 (luggage terminal) | Jump controls, mantling, balancing, reading environment paths |
| Clothing Damage & Shields | Beat 6 (air vents, Spicy only) | Personal shield, clothing durability, damage types, economic consequences |
| Combat | Beat 8 (gang fight) | Animation reading, counter/dodge/parry, CP as primary resource, real-time vs tutorial mode distinction |
| Time & Rest | Beat 9 (apartment) | Time skip via bed, wake-up time selection, 24-hour clock |
| Action Bar | Beat 11 (phone pocket) | Action bar slots, customisation, quick-retrieve actions, pocket designation |
| Economy & Jobs | Ch.1 (first job taken) | Job system overview, currency, pay, economic loop |
| Failure Pipeline | Ch.1 (first pipeline encounter) | Pipeline capture system, consequences, recovery |

This list is not exhaustive — additional entries may be added as systems are finalised.

### Beat 6 unlock — Spicy-only content

Clothing Damage & Shields unlocks after Beat 6 only if the player chose the Spicy route. If they took the base jumping puzzle, the entry unlocks the first time clothing damage occurs in Chapter 1 instead — the system does not require the Spicy DLC to be documented, only to be encountered.

---

## Tier 2 — Phone Tutorials (App Guides)

A 4th category within the Notes section of the phone menu, alongside field notes, lore entries, and quest notes. Only available when Meghan has her phone.

### What it covers

How to use each phone app — widget configuration, map navigation, STALKER profile reading, inventory management via phone, mission tracking, contacts, delivery app dispatch.

### Why phone-only

These are diegetic — Meghan reading the built-in help files for her own phone apps. They don't make sense outside the phone context. If the phone is lost, these guides are irrelevant (the apps are also lost).

### Visual style

Matches the phone UI — feels like built-in app documentation, not a game manual. May adopt the slightly institutional STALKER aesthetic where appropriate (e.g. the Skills/STALKER guide).

### Progressive unlock

Entries unlock when Meghan gains access to the corresponding app or feature.

### Entry list

| Entry | Unlocks after | Covers |
|-------|---------------|--------|
| Phone Basics | Beat 10 (phone shop) | Phone menu navigation, sections overview, real-time behaviour |
| Widget Configuration | Beat 10 (phone shop) | Eye implant widgets, point budget, drag-and-position, licence tiers |
| Map & Navigation | Beat 10 (phone shop) | City map, points of interest, fast travel (when unlocked) |
| Skills & STALKER Profile | Beat 10 (phone shop) | Skill and attribute view, progression tracking, profile reading |
| Inventory (Phone View) | Beat 10 (phone shop) | Per-storage view, combined grid view, drag-and-drop, reservations |
| Mission Tracking | Ch.1 (first mission) | Active/completed missions, quest tracking, objective markers |
| Contacts | Ch.1 (first contact added) | NPC relationships, communication log |
| Delivery App | Ch.1 (courier job unlocked) | Courier job dispatch, delivery tracking |
| HUD Modification (Advanced) | Ch.3 (Xtalics upgrade) | Drone widget set, dual cap system, porting widgets, advanced profiles |

This list is not exhaustive — additional entries may be added as phone features are finalised.

---

## How the Two Tiers Relate

The OOC tutorials cover *what the system is and how it works mechanically*. The phone tutorials cover *how to operate the phone app that interfaces with the system*.

**Example — Inventory:**
- OOC "Inventory Basics" explains carry weight, hand slots, and clothing storage
- Phone "Inventory (Phone View)" explains the per-storage view, combined grid, drag-and-drop, and reservations

**Example — HUD/Widgets:**
- OOC tutorials do not cover widgets (they are phone-dependent)
- Phone "Widget Configuration" and "HUD Modification (Advanced)" explain the full widget system from the phone's perspective

The dividing line: if the player could need the information when the phone is lost, it belongs in OOC. If the information is useless without the phone, it belongs in the phone.

---

## Design Principles

- **No duplication** — each entry exists in exactly one tier; cross-references link between them where useful
- **Progressive, not premature** — entries unlock only after the system is introduced; no reading ahead
- **Reference, not replacement** — the prologue beats remain the primary teaching tool; encyclopedia entries exist for review, not as a substitute for playing through the tutorial
- **No spoilers** — entries describe mechanics, not story; they do not reveal what happens after the point the player has reached
- **Searchable** — both tiers support keyword search within their respective interfaces
- **Concise** — entries explain enough to use the system; they are not exhaustive design documents

---

## Related

- [Prologue / Tutorial](prologue.md) — the 11-beat tutorial sequence that drives unlock triggers
- [CB — In-Game Menus](CB-ingame-menus.md) — menu states, phone sections, Notes category, OOC menu
- [UI/UX](../gdd/ui-ux.md) — OOC menu, phone as menu hub, widget system
- [CB — HUD Modification](CB-hud-modification.md) — widget configuration detail

---

## Open Items

- Entry content — individual entries need to be written once the mechanics they describe are finalised
- Whether entries update in place when a system gains new features (e.g. Inventory Basics expanding when a new storage type is introduced) or whether new entries are added
- Visual design for OOC tutorial browser — layout, scrolling, category tabs, search bar
- Phone App Guide visual treatment — how closely it mimics real app help screens vs the existing Notes aesthetic
- Whether completed entries show a "read" indicator to help players track what they've reviewed
