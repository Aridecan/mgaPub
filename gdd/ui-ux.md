# MGA — UI/UX

---

## Philosophy

MGA's UI is diegetic. Everything the player sees during gameplay is something Meghan actually sees — her cybernetic eye implants displaying AR overlays driven by her smartphone. The information available to the player is the information available to Meghan, which means UI capability is a function of her hardware and her skills. A better phone and higher skill ranks produce a more capable HUD. A player who has invested in Perception and bought the right licenses sees more than one who hasn't.

The only elements that break diegesis are the out-of-character menu options — save, load, settings, exit — which exist because Meghan's phone cannot be the interface for operations outside the game world.

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

### Phone Tier and Slot Count

The number of widgets Meghan can run simultaneously in first-person mode is determined by her smartphone tier. The basic phone — financed by Terry at the start of the game — supports a limited number of concurrent widgets. Higher-tier phones support more.

| Phone tier | Widget slots | Notes |
|------------|-------------|-------|
| Basic (Terry-financed) | 3 | Available from Chapter 1 |
| *(Higher tiers)* | More | Purchased through the economy; exact tiers TBD |

Purchasing a better phone is an economic decision that directly expands HUD capability.

### Widget Configuration

The player arranges their active widget set through the phone UI. Available licensed widgets are dragged onto the screen and positioned freely. Widgets outside the active slot count are queued but not displayed. Configuration can be changed at any time outside of combat.

The arrangement is spatial — the player decides where on their screen each widget appears. There is no forced layout.

---

## CAMERA Drone Widgets — Third Person HUD

The CAMERA drone has its own widget system, independent of the eye implant set. Widgets must be specifically adapted to run on the drone before they can be loaded into it — a widget that works in Meghan's eye implants does not automatically work on the drone.

### Drone Capacity — Pre and Post Chapter 3

The drone's widget capacity is limited in the early game. This is a technology constraint, not a phone constraint: the drone's systems are not yet fully understood or expanded.

| Game stage | Drone widget capacity | Reason |
|------------|----------------------|--------|
| Chapters 1–2 | Limited | Drone systems not yet understood; basic functionality only |
| Chapter 3+ | Expanding | Meghan joins MD and begins working with CryTek technology; she understands the drone's architecture and can extend it |
| Late game | Full | Full widget set available as Meghan and Imara Kesari develop the CAMERA system |

The Chapter 3 internship at Machina Dynamics is the gate — not just narratively but mechanically. Access to CryTek and to Imara's expertise is what allows the drone HUD to become the tactical tool it eventually is.

### Drone Widget Configuration

Configured separately from the eye implant layout. Same drag-and-position interface, accessed through the CAMERA section of the phone app.

---

## The Smartphone as Menu Hub

Outside of widget configuration, the smartphone is the primary interface for all in-world menus. If Meghan has her phone, she has full access to:

| Phone section | Contents |
|--------------|----------|
| **Widgets** | Configure active eye implant and drone widget sets |
| **Map** | City map, fast travel (if unlocked), points of interest |
| **Skills** | Full skill and attribute view; progression tracking |
| **Inventory** | Equipment, items, and consumables *(detail forthcoming)* |
| **Missions** | Active and completed mission list; quest tracking |
| **Contacts** | NPCs Meghan has relationships with; communication log |
| **Delivery app** | Courier job dispatch (unlocked in Chapter 1) |

The phone interface is navigated in-world — Meghan takes out her phone; time does not pause. Players who want to manage menus in a safe environment should find one. Accessing the phone in a dangerous situation is a player choice with real consequences.

### Losing the Phone

If Meghan is separated from her phone — stolen, lost during a mission, or taken during a pipeline capture — the in-world menus become unavailable. She cannot access her skill view, her map, her inventory, or her mission list until she recovers it. The eye implant widgets continue to function (they are loaded into the implants, not streamed from the phone in real time) but cannot be reconfigured.

---

## Out-of-Character Menu

Four options are always available regardless of phone status, accessed via a hardware input that does not require the phone:

| Option | Function |
|--------|----------|
| **Save** | Manual save to current playthrough folder |
| **Load** | Load a save from any playthrough folder |
| **Game Settings** | Graphics, audio, controls, autosave slider, streamer settings |
| **Exit Game** | Return to desktop |

These are the only elements that break diegesis. They are intentionally minimal.

---

## Open Items

- Full widget list and skill/license gate table — feature brief TBD
- Phone tier progression — exact number of tiers and slot counts
- Whether time pauses when the phone is open, or only slows, or continues normally
- Inventory system design — not yet documented
- Drone widget adaptation process — is it a crafting step, a skill check, a time cost, or some combination?
- Whether lost phone scenarios are scripted (specific mission events) or can occur organically through pipeline capture
- Contact/relationship tracker design — what information is shown and how
- Whether the delivery app and other job interfaces live in the phone or are accessed via separate in-world terminals
