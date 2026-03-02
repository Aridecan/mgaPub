# Creative Brief — Prologue / Tutorial

## Overview

The prologue is the game's tutorial. It teaches every major system through story beats while on rails — event-driven time progression, paused explanations, guided interactions. By the end of the prologue the player has a phone, a place to sleep, a reason to go to school, two people they've met organically, and first-hand experience with every control and system they will need in Chapter 1.

The prologue ends **after the phone shop visit** the following morning — not after Meghan goes to sleep. Chapter 1 opens with Meghan equipped and the world open.

---

## Tutorial Time Model

Time during the prologue advances on **event triggers**, not real-time. The game can pause gameplay, pop up an explanation, and resume. This is distinct from the Chapter 1+ time model where the 24-hour clock runs continuously (with an opt-in pause toggle in the phone menu).

---

## Tutorial Sequence — 11 Beats

### Beat 1 — Getting Off the Shuttle

| Field | Value |
|-------|-------|
| **Systems taught** | Action Key (E), movement controls (WASD), camera controls, 1st/3rd person view switching, movement differences per camera mode |
| **Story context** | Meghan arrives at the empty port at 1:30 AM on a Sunday |

Meghan's shuttle was delayed; she is the only passenger. The port is quiet. The player uses the Action Key (E) to interact with the shuttle door, then walks to the immigration desk. During this walk, on-screen prompts introduce:

- **WASD movement** — basic directional control
- **Camera controls** — mouse look / right stick
- **1st/3rd person switching** — how to toggle between eye implant view and CAMERA drone view, and how movement differs between them (first person is direct; third person is relative to camera)

This is the lowest-stakes moment in the game — an empty hallway with nothing to fail at.

---

### Beat 2 — Immigration Desk

| Field | Value |
|-------|-------|
| **Systems taught** | Inventory management (retrieve item), Give action, dialogue interaction |
| **Story context** | Meghan presents her passport to the immigration agent |

The player opens inventory for the first time and retrieves Meghan's passport. The Give action places it on the desk. The full dialogue sequence plays out (see `mgaPriv/chapters/prologue.md` for the dialogue draft) — this is the first time the player interacts with the dialogue system.

The immigration agent discovers the diplomatic tags, leaves to wake his supervisor. Meghan is shown to the VIP lounge to wait.

---

### Beat 3 — VIP Lounge

| Field | Value |
|-------|-------|
| **Systems taught** | Dialogue choices (branching conversation), dialogue skipping |
| **Story context** | Meghan and Milo discuss what to do — leave before the situation formalises |

The player participates in a branching conversation with Milo. The decision is made to leave before anyone can formalise whatever the Calibran government has decided Meghan is. Meghan leaves her luggage and passport behind.

**Tutorial moment:** During this conversation, the game teaches how to skip dialogue lines and how to fast-forward through dialogue. This is a low-stakes conversation — nothing said here is critical to progression — making it a safe place to demonstrate the skip mechanic without the player missing anything they cannot revisit.

---

### Beat 4 — Port Hallways (Stealth)

| Field | Value |
|-------|-------|
| **Systems taught** | Stealth mechanics, camera awareness (facing, coverage zones), sneaking movement |
| **Story context** | Milo points out the port's camera system as they move through hallways |

As Meghan and Milo navigate the port's corridors, Milo draws attention to the camera system. The game pauses to explain:

- How to read camera facing and coverage zones
- How to move through gaps in coverage
- The sneaking movement mode and how it affects detection

The hallway ends at a corner with a waiting sofa. Around the corner is the luggage claim area — heavily surveilled. This is the transition point into the traversal/DLC branch.

---

### Beat 5 — Luggage Terminal: Jumping Puzzle (Base)

| Field | Value |
|-------|-------|
| **Systems taught** | Jumping, traversal, platforming mechanics |
| **Story context** | The route over the beams above the luggage claim area avoids the cameras |

The luggage claim area is full of cameras. The route over the structural beams above avoids them entirely. The player learns:

- Jump controls
- Traversal mechanics (mantling, balancing)
- Basic platforming — reading the environment for paths

This is the base-game path. It is always available regardless of DLC installation.

---

### Beat 6 — Air Vents (Spicy DLC — Player's Choice)

| Field | Value |
|-------|-------|
| **Systems taught** | Vent traversal, trap identification, trap disarming, clothing damage system, personal shield mechanic |
| **Story context** | A maintenance air vent offers an alternate route through the terminal |

Halfway through the jumping puzzle, a maintenance air vent is accessible **if the Spicy DLC is installed**. The player **chooses** whether to enter the vent or continue with the jumping puzzle — even with Spicy or Super Spicy installed, the base route is always available.

Vent traversal uses standard movement controls — no new control scheme. Milo points out the first badly-maintained duct joints and teaches the player:

- **Trap identification** — how to spot dangerous joints before reaching them
- **Trap disarming** — how to neutralise them
- **Personal shield** — the nanotech shield (everyone on Terridyn has one) protects Meghan from physical injury, but the joints cut clothing
- **Clothing damage** — failing to disarm a joint destroys clothing; this is an economic consequence from the very start of the game ("hard start" if the player loses garments here)

The vent route is harder than the jumping puzzle and carries real stakes. Players who succeed learn the clothing system early; players who take damage learn the cost of carelessness.

---

### Beat 7 — Impound Room (Super Spicy DLC — Player's Choice)

| Field | Value |
|-------|-------|
| **Systems taught** | Super Spicy content introduction, additional exploration |
| **Story context** | A branch in the air vents leads toward the impound room |

Halfway through the air vents, a second branch leads toward the impound room with content appropriate to the Super Spicy patch. The player chooses whether to take the branch or continue through the vents.

All three routes — base jumping puzzle, Spicy air vents, Super Spicy impound branch — converge at the same destination: the tram station. The DLC tier shapes the experience without breaking the structure.

---

### Beat 8 — Tram Ride + Combat

| Field | Value |
|-------|-------|
| **Systems taught** | Combat system (tutorial-only paused mode), animation reading, counter/dodge/parry, CP as primary combat resource |
| **Story context** | Tram to downtown; Meghan hears a call for help — Celeste being attacked by gang members |

Meghan rides the air tram into downtown Terridyn City. At ground level (~2:30 AM), she hears Celeste Burton being assaulted by gang members behind Burton's Tavern — they are looking for a score for the black market job fairs.

**Tutorial combat** uses a paused mode unique to this encounter:

- Time freezes between actions
- The game highlights enemy animations and explains what each telegraph means
- Prompts guide the player through counter, dodge, and parry responses
- CP (Consciousness Pool) is introduced as the primary combat resource — reducing an opponent's CP to zero ends the fight

This paused mode exists only for this fight. Chapter 1 and beyond use the real-time unified action system described in the combat mechanics documentation. The tutorial combat teaches the vocabulary (animation reading, reaction selection) that real-time combat requires.

**Super Spicy variant:** If installed, the encounter may introduce randomness and intimacy-based combat mechanics with their potential outcomes.

---

### Beat 9 — Celeste Introduction + Apartment

| Field | Value |
|-------|-------|
| **Systems taught** | Time skip mechanics (bed interaction) |
| **Story context** | Post-fight introduction; Celeste offers Meghan a room; they go to the apartment; Meghan meets Terry |

After the fight, Celeste and Meghan talk. Meghan has no place to stay, no luggage, and no clear plan beyond "get to Terridyn and figure it out." Celeste offers a room in her six-bedroom dorm apartment.

Back at the apartment, Meghan meets Terry. Terry and Celeste are enrolled at the local tertiary school. Meghan mentions she was hoping to apply.

The player uses the bed to trigger a **time skip** to the next morning. The game explains the time skip mechanic — interacting with a bed advances the clock to the selected wake-up time.

---

### Beat 10 — Phone Shop

| Field | Value |
|-------|-------|
| **Systems taught** | Phone menu navigation, phone as diegetic UI hub |
| **Story context** | The next morning, Terry takes Meghan to a phone shop |

Terry takes Meghan to buy a phone. The player acquires the basic phone (Terry-financed, 3 widget points) and learns the phone menu system:

- **Widgets** — configure eye implant and drone widget sets
- **Map** — city map, points of interest
- **Skills** — attribute and skill view
- **Inventory** — equipment and items (connects to the inventory system introduced in Beat 2)
- **Missions** — active and completed mission list
- **Contacts** — NPC relationship tracker

The phone is the diegetic hub for all in-world menus. The game walks the player through each section during the shop tutorial. Time does not pause when the phone is open (except in the prologue's event-driven time model).

---

### Beat 11 — Phone Pocket + Action Bar

| Field | Value |
|-------|-------|
| **Systems taught** | Pocket designation (marking a pocket as the phone pocket), action bar (adding retrieve/store phone), action bar customisation |
| **Story context** | Final guided tutorial moment after the phone purchase |

After the phone tutorial, the game teaches two connected systems:

- **Pocket designation** — the player marks a specific pocket on their clothing as the phone pocket (using the reservation system from the inventory). This is where the phone lives when not in hand
- **Action bar** — the player adds a quick-retrieve action for the phone to the action bar, learning how action bar slots work and how to customise them

This is the last guided tutorial moment. The prologue ends here. Chapter 1 opens with Meghan equipped — phone in pocket, action bar configured — and the world open. No more paused explanations, no more guided prompts.

---

## Design Principles

- **Fiction-first teaching** — every system is taught through story, not UI overlays disconnected from the narrative. The player learns stealth because Milo points out the cameras; learns combat because Celeste needs help; learns the phone because Meghan needs one
- **Milo as tutorial voice** — Milo serves as the in-world guide, pointing things out and explaining mechanics in character. The game's paused explanations are Milo talking to Meghan
- **DLC shapes, never gates** — the Spicy and Super Spicy content tiers add optional paths and additional teaching moments without breaking the structure. All routes converge. The base path is always complete
- **One-time training wheels** — the paused combat mode exists only in Beat 8. The event-driven time model exists only in the prologue. Chapter 1 removes both and never brings them back
- **Consequences from the start** — clothing destruction in the Spicy route creates a real economic cost. The game does not protect the player from the consequences of failure even in the tutorial
- **Replay-friendly** — dialogue skipping is taught early (Beat 3); low-stakes content is skippable; the DLC branches offer different experiences on different playthroughs
- **Encyclopedia unlock** — each beat that introduces a system also unlocks the corresponding entry in the offline encyclopedia (OOC Tutorials for mechanics, Phone App Guides for phone features). The prologue teaches; the encyclopedia lets the player revisit. See [CB-tutorials](CB-tutorials.md) for the full entry list and unlock triggers

---

## Story Summary

| Time | Location | What happens |
|------|----------|-------------|
| 1:30 AM | Shuttle / port hallway | Meghan arrives alone; Action Key, movement, camera |
| ~1:35 AM | Immigration desk | Passport from inventory; dialogue system |
| ~1:40 AM | VIP lounge | Dialogue choices; skip tutorial; decision to leave |
| ~1:50 AM | Port hallways | Stealth; camera awareness |
| ~2:00 AM | Luggage terminal | Jumping puzzle (base) / air vents (Spicy) / impound room (Super Spicy) |
| ~2:20 AM | Tram | Ride to downtown |
| ~2:30 AM | Alley behind Burton's Tavern | Combat tutorial — rescue Celeste |
| ~3:00 AM | Celeste's apartment | Meet Terry; time skip via bed |
| Next morning | Phone shop | Phone UI tutorial |
| After shop | Outside / apartment | Pocket designation; action bar; **prologue ends** |

---

## Related

- [Combat mechanics](../../mgaPriv/mechanics/combat.md) — unified action system, CP/SP/MP pools, animation reading
- [Inventory system](../gdd/inventory.md) — physically modelled storage, reservations, quick actions
- [UI/UX](../gdd/ui-ux.md) — phone as menu hub, widget system, camera modes
- [CB-clothing](CB-clothing.md) — clothing damage, degradation, personal shield
- [Content & DLC](../gdd/content-and-dlc.md) — Spicy/Super Spicy tier structure
- [Prologue narrative](../../mgaPriv/chapters/prologue.md) — dialogue draft, immigration scene, story detail
