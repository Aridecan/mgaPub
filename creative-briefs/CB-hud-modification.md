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
| **Retagging** | Requires re-acquiring line of sight |

The tag duration creates a window — not a permanent tracker. If the player tags a target and then loses them, they have a few minutes to relocate before having to re-establish line of sight and tag again. This keeps bounty hunting active and requiring genuine tracking rather than set-and-forget.

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
- Whether tags can be refreshed before expiry (extend duration by retagging while still in LOS) or must expire first
- World-anchored widget range — configurable per widget, or a single global setting?
- Alert monitor design — what triggers it, what information it surfaces
- Whether the configuration canvas shows a mock game view as background or a neutral layout grid
- Drone widget adaptation process — how widgets are made compatible with the drone (crafting, skill check, time cost, or automatic on licence purchase)
