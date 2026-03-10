# Creative Brief — HUD Modification

---

## Overview

The HUD Modification screen is where the player configures what appears in each of Meghan's two active views — first person (eye implants) and third person (CAMERA drone). Each view has an independent widget set. Configuration changes take effect immediately and can be adjusted any time the phone is accessible.

UI/UX foundation: [UI/UX](../gdd/ui-ux.md) — diegetic HUD philosophy, widget availability gates, phone tier slot limits, drone capacity progression.

**Related documents:**

- [CB-skills-menu](CB-skills-menu.md) — licence purchase and widget eligibility
- [CB-ingame-menus](CB-ingame-menus.md) — phone menu structure
- [CB-action-bar](CB-action-bar.md) — action bar slots, folders, modes, profiles, grapple mode
- [CB-settings-menu](CB-settings-menu.md) — camera settings, gameplay HUD opacity

---

## Use Cases

### UC1 — Placing a Screen-Anchored Widget

**Actor:** The player
**Goal:** Add and position a screen-anchored widget on the active HUD
**Trigger:** Player opens the HUD Modification screen and selects a screen-anchored widget from the dropdown

**Step by step:**

1. **The configuration screen displays a live preview** of the current HUD — the actual game world behind the configuration overlay. This gives real context for widget placement.
2. **Player selects a widget from the dropdown** at the top of the screen. Only widgets within the available point budget are selectable; widgets that would exceed the budget are shown as unavailable with the point cost displayed.
3. **The widget appears on the canvas as a draggable block.** The player clicks and drags to position it anywhere on the screen.
4. **Position is stored as a percentage of screen dimensions** (e.g., 25% from left, 80% from top), not raw pixel coordinates. This ensures placement is consistent across all supported resolutions and aspect ratios.
5. **The point budget display updates in real time** as widgets are added or removed (e.g., **2 / 3 pts**).

---

### UC2 — Enabling World-Anchored Widgets

**Actor:** The player
**Goal:** Toggle which world-anchored widgets are active and at what range they appear
**Trigger:** Player opens the HUD Modification screen and selects a world-anchored widget from the dropdown

**Step by step:**

1. **Player selects a world-anchored widget from the dropdown.** It is added to a separate list alongside the canvas — no screen position to set.
2. **The list shows which dynamic widgets are active.** They can be enabled or disabled from there. No dragging, no placement.
3. **The player configures which world-anchored widgets are active** (name tags on, pool bars off, etc.) and at what range they appear.

**World-anchored widgets** attach to objects or characters in the game world and move with them. They appear when the relevant target is in frame and disappear when it is not. They are toggled on or off as a class — the player cannot individually reposition them per target.

**World-anchored widget examples:** Enemy or NPC name tag, pool bars above a character's head, faction indicator, vision cone overlay, tag marker on a player-tagged target.

---

### UC3 — Managing the Widget Point Budget

**Actor:** The player
**Goal:** Allocate a limited point budget across widgets, creating meaningful trade-off decisions
**Trigger:** Player adds or removes widgets and observes point budget constraints

**Step by step:**

1. **Each widget has a point cost** determined by how high up the skill tree the licence that unlocked it sits — a basic early-game widget costs 1 point; a high-tier late-game widget may cost 2 or 3.
2. **The player allocates their budget** across whichever widgets they want active: three cheap widgets, one expensive and one cheap, or a single costly widget with points to spare.
3. **The action bar does not consume points** — it is fixed infrastructure outside the budget.

**Eye implant AR (first person) budget:**

| Stage | Points | How to increase |
|-------|--------|----------------|
| Game start | 3 | — |
| New phone tier | More | Purchasing a higher-tier phone adds to the point budget |

**CAMERA drone (third person) has two independent point caps.** Both must be satisfied for a widget to run on the drone. The effective budget is whichever cap is lower.

| Cap | What it represents | How to increase |
|-----|--------------------|----------------|
| **Phone cap** | The phone's transmission budget for pushing widgets to the drone | Purchase a higher-tier phone |
| **CAMERA cap** | The drone's own hardware budget for running widgets | Xtalics research at Machina Dynamics (Chapter 3+) |

| Stage | Phone cap | CAMERA cap | Effective budget |
|-------|-----------|------------|-----------------|
| Game start | 3 | 3 | 3 |
| New phone only | Higher | 3 | 3 (CAMERA is bottleneck) |
| Chapter 3+ Xtalics only | 3 | Higher | 3 (phone is bottleneck) |
| Both upgraded | Higher | Higher | Whichever is lower |
| Late game | Full | Full | Full |

**To get more widgets on CAMERA, the player must upgrade both.** Upgrading one without the other hits the ceiling on the unupgraded side.

---

### UC4 — Porting a Widget to CAMERA

**Actor:** The player
**Goal:** Adapt an eye AR widget to run on the CAMERA drone
**Trigger:** Player wants a widget available in third-person view that they already own for first-person

**Step by step:**

1. **Own the widget for eye AR first.** A widget cannot be ported to CAMERA unless Meghan already holds the licence and has it available for first-person use.
2. **Spend time porting it.** Porting takes time proportional to how high up the skill tree the licence that unlocked the widget sits — a basic early-game widget ports quickly; a high-tier late-game widget takes significantly longer. Meghan's **Hacking** skill reduces porting time across the board.
3. **CAMERA budget must cover the point cost.** A ported widget costs the same number of points on CAMERA as it does on the eye AR system.

**The CAMERA widget set is always a subset of or equal to the eye AR set, never larger.** Porting is available from the start of the game — no chapter gate, no research prerequisite. The Chapter 3 Xtalics gate applies only to expanding the CAMERA hardware cap, not to porting itself.

---

### UC5 — Switching Widget Profiles

**Actor:** The player
**Goal:** Swap between pre-configured widget layouts for different gameplay situations
**Trigger:** Player opens the profile manager in the HUD Modification screen or switches profiles from the phone interface

**Step by step:**

1. **Player saves the current configuration as a named profile.** A profile is a complete snapshot — which widgets are active, where screen-anchored widgets are positioned, which world-anchored widgets are enabled.
2. **Player creates profiles for different situations** — a combat loadout, a social loadout, a stealth loadout.
3. **Switching profiles is immediate.** Profiles are stored per HUD — an eye AR profile does not affect the CAMERA configuration and vice versa.

**Profiles are part of gameplay, not just convenience.** Two conditions prevent switching:

**Lost phone** — if Meghan's phone is taken or lost, widget profiles are inaccessible. The widgets currently loaded into her eye implants continue to function (they run from the implants, not streamed live from the phone), but she cannot reconfigure or switch profiles until she recovers it.

**Magical girl transformation** — when Meghan transforms into Ava, everything she is wearing is stored in a pocket dimension for the duration. Her phone goes with it by default. She has no access to her phone in transformed state — no menus, no profile switching, no reconfiguration. Whatever profile was loaded before transformation is what she has for the fight. Choosing the right profile before transforming is a deliberate pre-combat decision.

Players who want phone access during transformation have options — the toss, or a uniform pocket modification — each with their own trade-offs. Full detail: [UI/UX — Phone and Transformation](../gdd/ui-ux.md#phone-and-transformation).

---

### UC6 — Navigating with GPS

**Actor:** The player
**Goal:** Set a destination and follow a navigation widget to it
**Trigger:** Player opens the GPS app and enters or selects a destination address

**Step by step:**

1. **Delivery missions provide a destination address.** The player opens the GPS app on the phone and enters or selects the address.
2. **The GPS feeds a navigation widget to the active HUD** — a directional indicator and route overlay on the mini-map.
3. **The navigation widget is only active when a destination is set.** It clears when the destination is reached or the player cancels it.
4. **The GPS widget must be loaded and active in the HUD** for navigation to display. A player who has not equipped it will need to refer to the map in the phone menu instead.

**No persistent quest arrows.** MGA does not use waypoint markers for unknown targets. If Meghan does not know where something is, the HUD does not know either. GPS is for known destinations.

---

### UC7 — Tagging a Target

**Actor:** The player
**Goal:** Mark a specific target with a world-anchored tag widget visible through the HUD
**Trigger:** Player tags a character while they have line of sight

**Step by step:**

1. **Player tags a target while in line of sight.** A world-anchored tag marker appears on the target.

   | Property | Detail |
   |----------|--------|
   | **Requirement** | Line of sight at time of tagging |
   | **Duration** | A few minutes — tag expires regardless of whether the target is in view |
   | **Widget** | World-anchored marker; visible through walls until expiry if the world-anchored tag widget is loaded |
   | **Retagging** | Requires line of sight; resets the timer to full duration |

2. **The tag duration creates a window, not a permanent tracker.** If the player tags a target and then loses them, they have a few minutes to relocate.
3. **Retagging while still in line of sight resets the timer,** so sustained tracking of a visible target is possible.
4. **Once line of sight is lost and the tag expires,** it must be re-established from scratch.

**Tagging is available for any character,** not just bounty targets. Tagged allies, tagged threats, tagged persons of interest — the tag widget does not distinguish. The player decides what is worth tagging.

---

### UC8 — Operating Under Blindfold

**Actor:** The system / the player
**Goal:** Understand how HUD information degrades when Meghan is blindfolded
**Trigger:** Meghan is blindfolded during a capture or restraint scenario

**Step by step:**

1. **First person — complete blackout.** The laser channel between the drone and her eye implants fails. The first-person view produces no feed. All eye implant widgets go dark. The player has no HUD information from this view.
2. **Third person — degraded mode.** The CAMERA drone falls back to its radio channel. The view narrows to a small cylinder around Meghan's body — the player can see her, her outfit, and her animations, but cannot see the surrounding environment.
3. **World-anchored widgets beyond her immediate position are unavailable.** Screen-anchored widgets that require world input (mini-map, alert monitor) may show degraded or empty states depending on implementation.

**The degraded view is still a third-person perspective on Meghan.** The player is not completely blind. They can observe her physical state and read her body language. This is intentional — capture and blindfolded sequences are gameplay, not cutscenes.

| HUD | Camera source | Blindfold effect |
|-----|--------------|-----------------|
| **First person** | Meghan's eyes | Completely blocked — laser channel fails; no feed, no widgets |
| **Third person** | CAMERA drone | Partially blocked — radio fallback; narrow cylinder view centred on Meghan; her animations and appearance remain visible in the middle of the screen |

---

## Reference

### Two Independent HUDs

Switching camera modes switches the active HUD. The configuration screen reflects whichever HUD is currently active, with a tab to switch to the other.

### Widget Types

**Screen-anchored widgets** are fixed to a position on the screen regardless of where Meghan is looking. Examples: mini-map, alert monitor, pool bars, compass, clock.

**World-anchored widgets** attach to objects or characters in the game world and move with them. Examples: enemy/NPC name tag, pool bars above a character's head, faction indicator, vision cone overlay, tag marker.

### The Action Bar

Both HUDs include a fixed action bar along the bottom of the screen. It is not configurable — it cannot be moved, disabled, or repositioned. The action bar is the one deliberate break from full diegesis in the HUD — it exists as a player reference for available actions and their inputs. Full design: [CB-action-bar](CB-action-bar.md).

### Actions vs Widgets — How They Unlock

**Actions** unlock automatically when a skill reaches the required rank. No purchase needed.

**Widgets** do not unlock automatically. A skill rank may make a widget available for purchase, but the player must actively buy the licence before the widget can be loaded. Reaching the qualifying rank only opens the door; the purchase is a separate decision and a separate cost.

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
