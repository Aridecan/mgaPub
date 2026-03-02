# MGA — UI/UX

---

## Philosophy

MGA's UI is diegetic. Everything the player sees during gameplay is something Meghan actually sees — her cybernetic eye implants displaying AR overlays driven by her smartphone. The information available to the player is the information available to Meghan, which means UI capability is a function of her hardware and her skills. A better phone and higher skill ranks produce a more capable HUD. A player who has invested in Perception and bought the right licenses sees more than one who hasn't.

The only elements that break diegesis are the out-of-character menu options — save, load, settings, tutorials, exit — which exist because Meghan's phone cannot be the interface for operations outside the game world.

---

## Camera Modes and HUD States

The game has two camera modes, each with its own HUD state:

| Mode | Camera source | HUD source |
|------|--------------|------------|
| **First person** | Meghan's eyes | Eye implant widgets (phone-loaded) |
| **Third person** | CAMERA drone | Drone widget set |

Switching camera modes switches the active HUD. The two widget sets are configured and managed independently.

---

## The Widget System

Widgets are modular HUD elements — individual readouts, overlays, and indicators that the player selects and arranges. They are loaded into Meghan's eye implants (first-person mode) or the CAMERA drone (third-person mode) and displayed as AR overlays in the respective view.

### Widget Availability

Widgets are not all available from the start. Access is gated by two requirements:

1. **Skill rank** — some widgets require a minimum rank in a relevant skill before they function correctly or become purchasable
2. **License** — widgets are purchased; owning the license is required to load the widget

Examples of the availability progression:

| Widget | Skill gate | Notes |
|--------|------------|-------|
| Character name tag | None | Available from basic phone |
| CP / SP bars | None | Available from basic phone |
| Faction indicator | Basic Situational Awareness | Identifies a target's affiliation |
| Vision cone overlay | High Perception | Shows the cone of what a character can currently see |
| Patrol route prediction | High Situational Awareness | Projects an NPC's likely movement path |
| Mana readout | Magical ability | Displays a target's mana state |
| Weakness indicator | Target Analysis | Flags elemental or physical vulnerabilities |

*Full widget list to be documented in a separate feature brief.*

---

## Eye Implant Widgets — First Person HUD

### Phone Tier and Widget Budget

The number of widgets Meghan can run simultaneously in first-person mode is determined by her smartphone tier. Each HUD has a **point budget** rather than a fixed slot count — widgets have individual point costs based on their licence tier, so the budget creates trade-off decisions rather than a simple "pick N widgets" cap.

| Phone tier | Widget points | Notes |
|------------|--------------|-------|
| Basic (Terry-financed) | 3 | Available from Chapter 1 |
| *(Higher tiers)* | More | Purchased through the economy; exact tiers TBD |

Purchasing a better phone is an economic decision that directly expands HUD capability. Full widget point system: [CB-hud-modification](../creative-briefs/CB-hud-modification.md).

### Widget Configuration

The player arranges their active widget set through the phone UI. Available licensed widgets are dragged onto the screen and positioned freely. Widgets outside the active slot count are queued but not displayed. Configuration can be changed at any time outside of combat.

The arrangement is spatial — the player decides where on their screen each widget appears. There is no forced layout.

---

## CAMERA Drone Widgets — Third Person HUD

The CAMERA drone has its own widget system, independent of the eye implant set. Widgets must be specifically adapted to run on the drone before they can be loaded into it — a widget that works in Meghan's eye implants does not automatically work on the drone.

### Drone Capacity — Dual Cap

The CAMERA drone has two independent point caps. Both must be satisfied for a widget to run. The phone transmits widget data to the drone — so the phone's budget limits what it can push, and the drone's own hardware limits what it can receive and run. Upgrading one without the other hits the ceiling on the unupgraded side.

- **Phone cap** — increases with phone tier, same upgrades that expand eye AR budget
- **CAMERA hardware cap** — starts at 3; expands via Xtalics research at Machina Dynamics from Chapter 3 onward

Both caps start at 3. Full CAMERA capability requires upgrading both. Detail: [CB-hud-modification](../creative-briefs/CB-hud-modification.md).

### Drone Widget Configuration

Configured separately from the eye implant layout. Same drag-and-position interface, accessed through the CAMERA section of the phone app.

---

## CAMERA Drone — Physical Operation

### Control

Meghan controls the drone through a cybernetics-linked transmitter — effectively at a thought. She can adjust two parameters in real time:

- **Position around her body** — the drone orbits her at any angle, functioning like a standard third-person camera the player rotates
- **Height off the ground** — how high the drone hovers, affecting the camera's vertical position and field of view

This makes the drone behave like a conventional third-person follow camera from the player's perspective, while remaining a physical object in the game world.

### Video Return — Laser Channel

The drone transmits its camera feed back to Meghan via laser, received by the optical sensors in her eye cybernetics. The laser requires line of sight — specifically, it needs to reach a surface within Meghan's field of vision to reflect into her eye receiver.

Under normal conditions this is always satisfied: the drone is positioned nearby, the laser bounces off the ground, a wall, or any surface Meghan can see, and the full video feed reaches her eye implants. The result is a complete third-person view with full widget overlay capability.

### Degraded Mode — Blindfolded

If Meghan is blindfolded, the laser channel fails: there is nothing in her line of sight for the laser to reflect from, so the video feed cannot reach the eye receiver.

In this state the drone falls back to its **command radio channel**, which carries significantly less data. The radio fallback provides:

- A narrow view covering a small cylinder around Meghan's body
- Enough for the player to see Meghan's profile and her outfit
- No tactical view of the surrounding area; no widget data beyond her immediate position

The player's information state mirrors Meghan's. When she cannot see, the camera cannot see. This is intentional — capture sequences and blindfolded states use the degraded drone view as a gameplay mechanic, not just a visual effect.

The degraded view is still a third-person perspective on Meghan. The player is not completely blind; they can see what Meghan is wearing, her physical state, and her immediate body position. They cannot see what is around her.

---

## The Smartphone as Menu Hub

Outside of widget configuration, the smartphone is the primary interface for all in-world menus. If Meghan has her phone, she has full access to:

| Phone section | Contents |
|--------------|----------|
| **Widgets** | Configure active eye implant and drone widget sets |
| **Map** | City map, fast travel (if unlocked), points of interest |
| **Skills** | Full skill and attribute view; progression tracking |
| **Inventory** | Equipment, items, and consumables — see [Inventory](inventory.md) |
| **Notes** | Field notes, lore entries, quest notes, and app guides — see [CB-ingame-menus](../creative-briefs/CB-ingame-menus.md) |
| **Missions** | Active and completed mission list; quest tracking |
| **Contacts** | NPCs Meghan has relationships with; communication log |
| **Delivery app** | Courier job dispatch (unlocked in Chapter 1) |

The phone interface is navigated in-world — Meghan takes out her phone; time does not pause. Players who want to manage menus in a safe environment should find one. Accessing the phone in a dangerous situation is a player choice with real consequences.

### Losing the Phone

If Meghan is separated from her phone — stolen, lost during a mission, or taken during a pipeline capture — the in-world menus become unavailable. She cannot access her skill view, her map, her inventory, or her mission list until she recovers it. The eye implant widgets continue to function (they are loaded into the implants, not streamed from the phone in real time) but cannot be reconfigured and widget profiles cannot be switched.

The same applies during magical girl transformation. When Meghan transforms into Ava, everything she is wearing is stored in a pocket dimension for the duration. Her phone, if it is on her person, goes with it. The widget profile loaded at the moment of transformation is fixed for the entire fight. This makes profile selection a deliberate pre-combat decision.

---

## Phone and Transformation

The default behaviour — phone into the pocket dimension, inaccessible during transformation — can be worked around. Players have several options, each with meaningful trade-offs.

### The toss

If Meghan is holding her phone when she transforms, she can toss it into the air just before the transformation completes, catch it on the way down as Ava, and keep it in hand. The phone remains in the physical world; it never enters the pocket dimension.

Trade-off: the toss requires deliberate timing and leaves the phone airborne and uncontrolled for a moment. If she misses the catch — in a chaotic situation, under pressure, or simply mistimed — the phone lands on the ground. It is now somewhere in a combat zone.

### The uniform pocket

The magical girl uniform can be modified through the same clothing customisation system as Meghan's civilian wardrobe. Adding a pocket to the uniform is one option. A phone in the uniform's pocket goes into the pocket dimension with the uniform on transformation and comes back out with it on untransformation — remaining accessible in transformed state throughout.

Trade-off: the uniform is not indestructible. If the magical girl uniform degrades in combat, the phone drops. A player relying on the pocket is betting on the uniform holding.

### Pocket dimension storage as a mechanic

If Meghan stores her phone in the magical girl uniform's pocket and then untransforms, she is back in her civilian clothes — and her phone is in the pocket dimension with the uniform. It stays there until she transforms again. She is effectively without a phone until her mana recovers enough to transform.

This creates an emergent tactic: Meghan can deliberately exploit the pocket dimension as secure storage. She stores her real phone in the magical girl uniform, then allows herself to be captured carrying only a burner phone. Captors search and confiscate the burner. Once she is alone in a cell or left unguarded, she transforms to retrieve her real phone — with all menus, maps, and contacts intact — and proceeds from there.

The burner phone is a purchasable item. It functions as a basic phone with no widgets, no skill profile, and no data — just enough to look like a phone worth taking.

---

---

## Localization Settings

The settings menu provides two independent dropdowns for language selection:

| Setting | Controls | Populated from |
|---------|----------|----------------|
| **Text Language** | Subtitles, UI strings, in-game text | Installed `LocText-*` packages |
| **Voice Language** | Spoken dialogue, voice-over | Installed `LocVoc-*` packages |

The two selections are fully independent. A player can choose any combination of installed text and voice languages — for example, English text with Japanese vocals. The boot manager loads both selected packages without requiring them to match. See [Boot Manager — Localization Independence](boot-manager.md#localization-independence).

Each dropdown only lists languages for which the corresponding package is installed. If no voice package is installed for a language, it does not appear in the voice dropdown even if the text package is present, and vice versa.

---

## Out-of-Character Menu

Five options are always available regardless of phone status, accessed via a hardware input that does not require the phone:

| Option | Function |
|--------|----------|
| **Save** | Manual save to current playthrough folder |
| **Load** | Load a save from any playthrough folder |
| **Game Settings** | Graphics, audio, controls, autosave slider, streamer settings |
| **Tutorials** | Offline encyclopedia of game mechanics — entries unlock progressively as systems are introduced |
| **Exit Game** | Return to desktop |

These are the only elements that break diegesis. They are intentionally minimal. The Tutorials entry provides a persistent reference for every system taught during the prologue and Chapter 1, available even when Meghan's phone is lost. See [CB-tutorials](../creative-briefs/CB-tutorials.md) for the full design.

---

## Jumping Puzzle Timer

Jumping puzzles include a visible in-game timer for players who want to compete on completion times.

| Element | Detail |
|---------|--------|
| **Timer display** | On-screen with millisecond precision (e.g. `02:14.327`) |
| **Start trigger** | Entering the start volume or interacting with a start prompt |
| **End trigger** | Reaching the end volume |
| **Modded indicator** | Small, neutral "MODDED" label displayed on the timer when mods are active |
| **Local records** | Best-of-session and personal best times saved locally |

The timer provides transparent run information. Enforcement and ranking standards are left to the community — competitive players and speedrunners set their own rules around what constitutes a valid run. The "MODDED" indicator is informational, not punitive; mods are not treated as cheating.

No online leaderboard, anti-cheat, or run upload is provided. Most competitive jumping puzzle communities rely on video verification, and the game's role is to provide a consistent clock and unambiguous start/end points.

---

## Open Items

- Full widget list and skill/license gate table — feature brief TBD
- Phone tier progression — exact number of tiers and slot counts
- ~~Whether time pauses when the phone is open~~ — resolved: world time continues normally by default; opt-in **Pause in In-Game Menu** toggle in Gameplay Settings covers both phone and out-of-character menus
- Inventory system design — not yet documented
- ~~Drone widget adaptation process~~ — resolved: own eye AR widget first, then time investment to port; time proportional to licence tier, reduced by Hacking skill; see CB-hud-modification
- Whether lost phone scenarios are scripted (specific mission events) or can occur organically through pipeline capture
- Environmental laser degradation confirmed — smoke, magical interference, and certain materials can degrade the laser channel; technical implementation TBD
- Drone damage and destruction: the drone is a stealth unit and does not have a damage model. It exists primarily to provide the third-person camera perspective and will not be targeted or destroyed by enemies. This may be revisited in a later design pass.
- Contact/relationship tracker design — what information is shown and how
- Whether the delivery app and other job interfaces live in the phone or are accessed via separate in-world terminals
