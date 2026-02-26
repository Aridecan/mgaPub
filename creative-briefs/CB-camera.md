# Creative Brief — CAMERA System

---

## Overview

CAMERA (Cognitive Augmented Monitoring, Engagement & Reconnaissance Assistant) is the in-fiction justification for the third-person camera. Meghan brought a personal CAMERA drone with her from Calibran — it has been providing the third-person view since the moment she steps off the ship in the prologue. She also has the drone's source code, carried over from her years as a magical girl on Calibran.

The drone is battery-powered Calibran technology. The Chapter 3–4 work with Imara is about upgrading the existing drone using Xtalics technology and building new drones for the other magical girls. Without a CAMERA drone, a magical girl cannot be controlled by the player.

This brief covers the player experience of the CAMERA system: how the third-person camera works, how the drone is managed, how it is upgraded, how team drones are built, and how party switching works once allies have their own drones.

**Related documents:**

- [CB-hud-modification](CB-hud-modification.md) — widget point budget, dual-cap system, widget porting, widget profiles
- [UI/UX](../gdd/ui-ux.md) — diegetic HUD philosophy, camera modes, drone physical operation, degraded mode
- [CAMERA System (lore)](../../mgaPriv/world/camera-system.md) — lore document, development background, technical architecture
- [Leadership](../../mgaPriv/mechanics/skills/leadership.md) — ally command synergy with CAMERA

---

## Use Cases

### UC1 — Using the Third-Person Camera

**Actor:** The player
**Goal:** Navigate the game world using the standard third-person camera view provided by Meghan's CAMERA drone
**Available from:** Game start (prologue)

The CAMERA drone is Calibran technology that Meghan brought with her. It has been operational since before the game begins. The player does not need to build, find, or unlock it.

**Controls:**

| Input | Action |
|-------|--------|
| Mouse left/right | Orbits the drone horizontally around Meghan |
| Mouse up/down | Orbits the drone vertically around Meghan |
| Scroll wheel | Zooms the drone closer to or further from Meghan |

These controls position the drone in a sphere around Meghan, functioning identically to a conventional third-person follow camera from the player's perspective. The drone is a physical object in the game world, controlled through Meghan's cybernetics-linked transmitter at a thought.

**HUD in third person:**

The third-person view has its own independent widget set, configured separately from the first-person eye implant widgets. Widgets must be ported to the CAMERA drone before they can be used in this view (see [CB-hud-modification — Porting Widgets to CAMERA](CB-hud-modification.md)).

**Battery indicator:**

A fixed element on the third-person HUD overlay shows the drone's current battery level. This indicator is always visible when in third-person mode and does not consume widget points — it is fixed infrastructure, like the action bar.

---

### UC2 — Switching Camera Modes

**Actor:** The player
**Goal:** Toggle between first-person (eye implants) and third-person (CAMERA drone) views
**Available from:** Game start

**The toggle:**

A single input switches between first person and third person. The transition is seamless — no loading screen, no fade, no animation delay. The active HUD switches with the camera mode. Each view has its own independent widget configuration and point budget.

| Mode | Camera source | HUD source | Widget budget |
|------|--------------|------------|---------------|
| First person | Meghan's eye implants | Eye implant widgets | Phone tier |
| Third person | CAMERA drone | Drone widget set | Dual-cap (phone + CAMERA hardware) |

The player can switch at any time — in combat, in dialogue, while moving. The camera mode is a view preference, not a gameplay state.

**Widget independence:**

Widget configurations are fully independent between the two views. A widget active in first person is not automatically active in third person (and vice versa). Profiles are stored per HUD. See [CB-hud-modification](CB-hud-modification.md) for full widget management.

---

### UC3 — Managing CAMERA Battery

**Actor:** The player
**Goal:** Keep the CAMERA drone charged and operational
**Available from:** Game start

Meghan's Calibran drone runs on battery power. Under normal use the battery lasts approximately one week of in-game time on a full charge.

**Battery indicator:**

- Visible on the third-person HUD overlay at all times
- Shows current charge level as a percentage or visual fill

**Charging:**

- The drone charges at any standard power outlet — Meghan's apartment, Machina Dynamics workshop, a bar, a shop, any location with accessible power
- Charging takes in-game time. The player initiates charging and the clock advances until the charge is complete (or the player interrupts early for a partial charge)
- The drone does not need to be in third-person mode to charge — it can be docked and charging while the player plays in first person

**Low battery:**

- A low battery warning appears on the third-person overlay before the drone shuts down, giving the player time to find a power source
- When battery reaches zero: the drone powers down and lands. The player is forced into first-person mode until the drone is charged
- The drone retains its position near Meghan when powered down — the player does not need to retrieve it from a remote location

**Design intent:** Battery is a soft management consideration, not a constant pressure. A week of game time is generous enough that most players will charge incidentally (while sleeping, while at MD). The player who completely ignores it will eventually be forced to first-person, which is an inconvenience, not a hard failure.

---

### UC4 — Upgrading CAMERA Hardware (Ch.3+)

**Actor:** The player
**Goal:** Expand the CAMERA drone's widget point budget through Xtalics upgrades
**Available from:** Chapter 3 (after joining Machina Dynamics)

After joining MD, Meghan works with Imara to upgrade the Calibran drone using Xtalics crystal-substrate technology. This does not replace the drone — it enhances the existing hardware with Terridyn's crystal engineering capabilities.

**The upgrade process:**

1. **Location:** MD workshop — the Xtalics integration requires Imara's Lapidronics expertise and MD's fabrication equipment
2. **Activity type:** Puzzle-based crafting. The player works through a circuit design activity at the workshop bench
3. **Result:** Each successful upgrade expands the CAMERA hardware widget point cap (the second cap in the dual-cap system documented in [CB-hud-modification](CB-hud-modification.md))
4. **Escalating costs:** Later upgrades require exotic materials obtained through quest chains. Early upgrades use materials available through normal play; advanced upgrades gate behind exploration and mission completion

**Progression:**

| Stage | CAMERA hardware cap | How reached |
|-------|--------------------|-------------|
| Game start (Calibran drone) | 3 | — |
| First Xtalics upgrade | Higher | Basic MD workshop materials |
| Subsequent upgrades | Higher still | Escalating material requirements |
| Late game | Full | Exotic materials from quest chains |

Upgrading the CAMERA hardware cap only expands one side of the dual-cap system. The effective widget budget is whichever cap (phone or CAMERA) is lower. The player must also upgrade their phone to realise the full benefit.

**Continues through Ch.3 and Ch.4+:** The upgrade path is not a single event. New upgrade tiers become available as the story progresses and new materials are discovered. The workshop remains accessible for the duration of the game.

---

### UC5 — Building Team CAMERA Drones (Ch.3)

**Actor:** The player
**Goal:** Construct CAMERA drones for Nisa and Nagi so they can be player-controlled
**Available from:** Chapter 3 (must be complete before end of Ch.3)

Meghan and Imara construct new drones for the other magical girls using Xtalics technology. These are new-build drones, not copies of Meghan's Calibran hardware — they are built from scratch at the MD workshop using Imara's Lapidronics expertise.

**The build process:**

1. **Material gathering:** Each drone requires specific materials obtained through quest chains and activities. The player must acquire the components before construction can begin
2. **Construction:** Puzzle-based crafting activity at the MD workshop, similar to the upgrade process but longer and more involved
3. **Calibration:** Each completed drone must be individually calibrated to the specific magical girl's cybernetics. Nisa's drone is calibrated to Nisa; Nagi's drone is calibrated to Nagi. A drone calibrated for one girl does not work with another without reconfiguration
4. **Result:** Once a drone is calibrated, that magical girl becomes available for player control (see UC6)

**Sequence:**

- The builds are presented as a quest/activity chain — not a single crafting session
- Both drones must be complete before the end of Chapter 3 — they are required for the team management gameplay introduced in Chapter 4
- The player can build Nisa's and Nagi's drones in either order

**New-build vs Meghan's drone:** The team drones are Xtalics-native from the start. They are built with Terridyn crystal-substrate technology, not retrofitted Calibran hardware. This means they start with the Xtalics integration that Meghan's drone needs to be upgraded to receive.

---

### UC6 — Party Control via CAMERA

**Actor:** The player
**Goal:** Switch between controlling different magical girls during gameplay
**Available from:** When an ally's CAMERA drone is calibrated (Ch.3+)

Once a magical girl has her own calibrated CAMERA drone, the player can switch to controlling her. The player sees the world from the switched-to girl's CAMERA drone.

**Switching:**

- Free switching at any time, including mid-combat
- No cooldown, no resource cost, no animation delay
- The switch is instantaneous — the camera cuts to the selected girl's drone view

**When controlling another girl:**

- Camera controls are identical to Meghan's drone (mouse orbit, scroll zoom)
- The player has full control of the switched-to girl's actions via the action bar
- Each girl's drone has its own widget configuration and point budget
- The player sees the world through that girl's CAMERA drone, with her widget set active

**When switched away from Ava:**

- Ava becomes AI-controlled
- She continues to act based on party AI behaviour (combat stance, threat response, positioning)
- The player can switch back to Ava at any time to resume direct control

**Party management context:** CAMERA drones are the in-world justification for the party management system. Before drones, allies appear as NPC allies in specific encounters but cannot be directly controlled. After drones, full party management is unlocked — the player coordinates Ava, Nisa, and Nagi as a team.

---

### UC7 — Pre-CAMERA Ally Commands (Open Item)

**Actor:** The player
**Goal:** Issue basic commands to allied magical girls before they have CAMERA drones
**Available from:** When allies are present in combat (Ch.2+)
**Status:** Open item — scope TBD

Before allies have their own CAMERA drones, the player cannot switch to controlling them directly. However, the Leadership skill may enable basic command functionality — directing allies to focus targets, hold position, fall back, or use specific abilities.

The Leadership skill already documents synergy with the CAMERA system (see [Leadership — Skill Synergies](../../mgaPriv/mechanics/skills/leadership.md)). The question is whether and how Leadership provides value *before* CAMERA drones exist.

**Possible scope (not committed):**

- Simple command wheel (focus target, hold, fall back, use ability)
- Effectiveness gated by Leadership skill level
- Ally compliance influenced by relationship + Leadership + situational factors
- Commands become more effective once CAMERA drones are online (better information = better execution)

This is left as an open item pending further design work on pre-Ch.4 ally encounters and Leadership's early-game role.

---

## Open Items

- **Leadership-based command scope** — what commands are available before CAMERA drones? How effective are they? (UC7)
- **Drone physical design** — size, appearance, flight characteristics. Currently TBD in the lore document
- **Battery drain rate under load** — does heavy widget usage or sustained high-altitude flight drain battery faster than idle? Or is drain constant regardless of usage?
- **Xtalics power conversion milestone** — does upgrading the Calibran drone to Xtalics power eventually remove the battery concern? Or do all drones (including Xtalics-built team drones) have battery considerations?
- **Team drone battery** — do Nisa's and Nagi's Xtalics-built drones have battery limits, or does Xtalics-native construction mean they are self-charging?
- **Party AI behaviour quality** — how smart is Ava (and other girls) when AI-controlled? What combat behaviours do they exhibit? Can the player configure AI stances?
- **Charging time** — exact in-game duration for a full charge from empty. Design intent is "overnight" but not specified
- **Picture-in-picture** — can the player see another girl's CAMERA drone view as a PiP window while controlling a different girl?
- **Whether drones have any direct combat capability** — currently ISR-only; lore document leaves this open
- **Drone and transformation interaction** — does the drone integrate with the transformed form, the civilian form, or both?
- **Post-Ch.5 CAMERA maintenance** — when MD falls to QNS, does the CAMERA system remain operational or require Imara's support?
- **Tal's role** — can Tal (Nisa's familiar) contribute to CAMERA maintenance or expansion?
