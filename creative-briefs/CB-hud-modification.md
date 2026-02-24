# CB — HUD Modification

## Overview

The HUD Modification screen is where the player configures what appears in each of Meghan's two active views — first person (eye implants) and third person (CAMERA drone). Each view has an independent widget set. Configuration changes take effect immediately and can be adjusted any time the phone is accessible.

UI/UX foundation: [UI/UX](../gdd/ui-ux.md) — diegetic HUD philosophy, widget availability gates, phone tier slot limits, drone capacity progression.

---

## Two Independent HUDs

| HUD | Camera source | Blindfold effect |
|-----|--------------|-----------------|
| **First person** | Meghan's eyes | Completely blocked — laser channel fails; no feed, no widgets |
| **Third person** | CAMERA drone | Partially blocked — radio fallback; narrow cylinder view centred on Meghan; her animations and appearance remain visible in the middle of the screen |

Switching camera modes switches the active HUD. The configuration screen reflects whichever HUD is currently active, with a tab to switch to the other.

---

## The Action Bar

Both HUDs include a fixed action bar along the bottom of the screen. It is not configurable — it cannot be moved, disabled, or repositioned.

The action bar is the one deliberate break from full diegesis in the HUD. It exists as a player reference for available actions and their inputs. Meghan's eye implants technically display it, but it is not something she consciously reads — it is there for the player's benefit. This is a pragmatic concession: action availability changes frequently enough that requiring the player to memorise it from context would be friction without payoff.

---

## Widget Types

There are two categories of widget, with different behaviour in the configuration screen.

### Screen-anchored widgets

These are fixed to a position on the screen. The player places them and they stay there regardless of where Meghan is looking or what is in the world.

Examples:
- Mini-map
- Alert monitor
- Pool bars (Meghan's own HP / CP / SP / Mana)
- Compass
- Clock

These widgets are placed by dragging to a position on the configuration canvas. They can be enabled or disabled individually.

### World-anchored widgets

These attach to objects or characters in the game world and move with them in the view. They appear when the relevant target is in frame and disappear when it is not.

Examples:
- Enemy or NPC name tag
- Pool bars above a character's head
- Faction indicator attached to a character
- Vision cone overlay on a character
- Tag marker on a player-tagged target

World-anchored widgets are toggled on or off as a class — the player cannot individually reposition them per target. What can be configured is which world-anchored widgets are active at all (name tags on, pool bars off, etc.) and at what range they appear.

---

## Actions vs Widgets — How They Unlock

These two things are gated differently and it is worth stating explicitly.

**Actions** unlock automatically when a skill reaches the required rank. No purchase needed — the capability is there the moment the threshold is crossed. A player who hits the right rank in Melee Combat gains the new action immediately.

**Widgets** do not unlock automatically. A skill rank may make a widget available for purchase, but the player must actively buy the licence before the widget can be loaded. Reaching the qualifying rank only opens the door; the purchase is a separate decision and a separate cost.

This distinction matters for the configuration screen: a widget shown as "eligible" in the skills menu still does nothing until it has been purchased.

---

## Slot Counts and Capacity

### Eye implant AR — First Person

| Stage | Slots | How to increase |
|-------|-------|----------------|
| Game start | 3 custom widgets | — |
| New phone tier | More slots | Purchasing a higher-tier phone adds capacity |

Slot count scales with phone hardware. The basic phone (Terry-financed, Chapter 1) supports 3 concurrent custom widgets. Higher-tier phones add more. The action bar does not occupy a custom widget slot — it is fixed infrastructure.

### CAMERA drone — Third Person

| Stage | Slots | How to increase |
|-------|-------|----------------|
| Game start | 3 custom widgets | — |
| Chapter 1–2 | 3 (no change) | Phone upgrades do not affect CAMERA capacity |
| Chapter 3+ | Expanding | Slot cap increases via Xtalics research at Machina Dynamics |
| Late game | Full | Capacity grows as Meghan and Imara Kesari develop the CAMERA system |

Phone tier does not affect CAMERA capacity. The drone is independent hardware with its own architecture. Expanding beyond 3 slots requires Meghan to understand and extend that architecture — which only becomes possible when she joins MD in Chapter 3 and begins working with Xtalics technology.

Porting widgets to CAMERA is available from the start of the game. The Chapter 3 gate applies only to expanding slot capacity, not to porting itself.

---

## Porting Widgets to CAMERA

A widget does not automatically become available on the CAMERA drone when purchased. The drone runs different systems from the eye implants, and widgets must be adapted to run on it. Porting is available immediately — no chapter gate, no research prerequisite.

The process:

1. **Own the widget for eye AR first** — a widget cannot be ported to CAMERA unless Meghan already holds the licence and has it available for first-person use
2. **Spend time porting it** — porting takes time proportional to how high up the skill tree the licence that unlocked the widget sits; a basic early-game widget ports quickly; a high-tier late-game widget takes significantly longer. Meghan's **Hacking** skill reduces porting time across the board.
3. **CAMERA slot must be available** — a ported widget still occupies a CAMERA slot; porting without capacity does nothing until a slot opens

This means the CAMERA widget set is always a subset of or equal to the eye AR set, never larger. A player who wants a widget on the drone must first invest in it for first-person use. If they can afford it, they can port it — but higher-tier widgets demand more of Meghan's time to adapt.

---

## Widget Profiles

The phone stores named widget profiles for each HUD. A profile is a complete snapshot of the current widget configuration — which widgets are active, where screen-anchored widgets are positioned, which world-anchored widgets are enabled.

The player can:

- Save the current configuration as a named profile
- Switch between saved profiles from the phone interface
- Maintain separate profiles for different situations (e.g., a combat loadout, a social loadout, a stealth loadout)

Switching profiles is immediate. Profiles are stored per HUD — an eye AR profile does not affect the CAMERA configuration and vice versa.

---

## Configuration Interface

The configuration screen shows a canvas representing the full screen area. Screen-anchored widgets appear as draggable blocks on the canvas. World-anchored widgets are listed separately as toggles — they cannot be repositioned, only enabled or disabled.

| Element | Screen-anchored | World-anchored |
|---------|----------------|----------------|
| Enable / disable | Yes | Yes |
| Reposition | Drag freely on canvas | No — follows target in world |
| Resize | TBD | No |

The canvas shows a live preview of widget placement. Widgets outside the active slot count for the current phone tier are shown greyed out — they are configured but not loaded until slot capacity increases.

---

## Navigation and Guidance

MGA does not use persistent quest arrows or waypoint markers for unknown targets. If Meghan does not know where something is, the HUD does not know either.

### Delivery missions — GPS navigation

Delivery missions provide a destination address. The player opens the **GPS app** on the phone and enters or selects the address. This feeds a navigation widget to the active HUD — a directional indicator and route overlay on the mini-map. The navigation widget is only active when a destination is set; it clears when the destination is reached or the player cancels it.

The GPS widget must be loaded and active in the HUD for navigation to display. A player who has not equipped it will need to refer to the map in the phone menu instead.

### Bounty hunting — player-driven investigation

Bounty targets do not have guideposts. The player receives a contract with whatever information the client provides — name, last known location, description, associates — and must find the target using available tools:

- Social skills — asking NPCs, gathering information through conversation
- Deductive reasoning — following leads, observing patterns
- Hacking — accessing records if available

Once the player has identified and located the target, they can **tag** them.

---

## Tagging

Tagging marks a specific target with a world-anchored tag widget visible through the HUD.

| Property | Detail |
|----------|--------|
| **Requirement** | Line of sight at time of tagging |
| **Duration** | A few minutes — tag expires regardless of whether the target is in view |
| **Widget** | World-anchored marker; visible through walls until expiry if the world-anchored tag widget is loaded |
| **Retagging** | Requires line of sight; resets the timer to full duration |

The tag duration creates a window — not a permanent tracker. If the player tags a target and then loses them, they have a few minutes to relocate. Retagging while still in line of sight resets the timer, so sustained tracking of a visible target is possible. Once line of sight is lost and the tag expires, it must be re-established from scratch.

Tagging is available for any character, not just bounty targets. Tagged allies, tagged threats, tagged persons of interest — the tag widget does not distinguish. The player decides what is worth tagging.

---

## Blindfold States

### First person — complete blackout

When Meghan is blindfolded, the laser channel between the drone and her eye implants fails. The first-person view produces no feed. All eye implant widgets go dark. The player has no HUD information from this view.

### Third person — degraded mode

The CAMERA drone falls back to its radio channel. The view narrows to a small cylinder around Meghan's body — the player can see her, her outfit, and her animations, but cannot see the surrounding environment. World-anchored widgets beyond her immediate position are unavailable. Screen-anchored widgets that require world input (mini-map, alert monitor) may show degraded or empty states depending on implementation.

The degraded view is still a third-person perspective on Meghan. The player is not completely blind. They can observe her physical state and read her body language. This is intentional — capture and blindfolded sequences are gameplay, not cutscenes.

---

## Open Items

- Full widget list and skill/licence gate table — feature brief TBD
- Whether screen-anchored widgets can be resized, or only repositioned
- GPS widget — is it available from the basic phone, or gated behind a licence? Delivery is available from Chapter 1, so the GPS widget likely needs to be accessible early
- Tag duration — exact value TBD; "a few minutes" is the design intent
- ~~Whether tags can be refreshed before expiry~~ — confirmed: retagging in LOS resets the timer to full duration
- World-anchored widget range — configurable per widget, or a single global setting?
- Alert monitor design — what triggers it, what information it surfaces
- Whether the configuration canvas shows a mock game view as background or a neutral layout grid
- ~~Drone widget adaptation process~~ — confirmed: own the eye AR widget first, then spend time porting it; porting is available immediately from game start; Chapter 3 Xtalics research only expands slot capacity
- ~~Porting time cost~~ — confirmed: proportional to the licence tier that unlocked the widget; reduced by Hacking skill
- Number of profiles the phone can store — unlimited, or a cap?
- Whether profiles can be shared between playthroughs or are per-save
