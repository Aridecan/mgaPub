# Creative Brief — Controls & Input

---

## Overview

MGA supports two input methods from launch: **keyboard and mouse** (primary) and **gamepad** (secondary). Both are always active simultaneously — the game detects the last-used device and swaps UI prompts automatically. There is no manual input device selection.

Three core actions live **off the action bar** as dedicated inputs: **Jump**, **Dodge**, and **Contextual Action** (interact / grab). These are always available regardless of action bar state, folder depth, or modifier layer. Every other executable ability routes through the 10-slot action bar.

The controller scheme uses a **3×3 + 1 modifier layer system** to map 10 action bar slots onto three face buttons: base layer, LT layer, LB layer, and a double-modifier (LB+LT) layer for slot 10 plus utility functions. Keyboard uses number keys 1–9 and 0 for direct access.

All bindings are **fully rebindable** through the settings menu (see [Settings Menu](CB-settings-menu.md)).

**Related documents:**

- [Action Bar System](../../mgaPriv/mechanics/skill-bar.md) — 10-slot bar, folders, slot types, combat tempo
- [Traversal Creative Brief](CB-traversal.md) — analog movement, posture, sprint, parkour
- [Combat Creative Brief](CB-combat.md) — action bar in combat, attack initiation, defence
- [Knockout & Recovery Creative Brief](CB-knockout-recovery.md) — brainwave sync minigame reuses action bar slots
- [Camera Creative Brief](CB-camera.md) — CAMERA drone, 1st/3rd person toggle
- [Settings Menu](CB-settings-menu.md) — rebinding UI, sensitivity, dead zones, aim assist

---

## Controller Map (Gamepad)


| Input                  | Action                                              |
| ---------------------- | --------------------------------------------------- |
| Left Stick             | Move — view-relative (1st person), screen-relative (3rd person) |
| Left Stick full + L3   | Sprint toggle                                       |
| Right Stick            | Look (1st person) / Orbit camera (3rd person)       |
| R3 (right stick click) | Dodge                                               |
| A (bottom face)        | Jump                                                |
| RT press/release       | Contextual action (interact, pick up, activate)     |
| RT hold                | Grab (grapple initiate, ledge grab, object carry)   |
| X / Y / B              | Action slots 1 / 2 / 3                              |
| LT + X / Y / B         | Action slots 4 / 5 / 6                              |
| LB + X / Y / B         | Action slots 7 / 8 / 9                              |
| LB+LT + X              | Action slot 10                                      |
| LB+LT + Y              | Camera toggle (1st/3rd person)                      |
| LB+LT + B              | Reserved                                            |
| RB                     | Zoom (scope, binoculars)                            |
| D-pad Up               | Posture up (belly crawl → crawl → crouch → stand)   |
| D-pad Down             | Posture down (stand → crouch → crawl → belly crawl) |
| D-pad Left/Right       | Weapon switch                                       |
| D-pad Left/Right + LT  | Transform / Untransform                             |
| LB + D-pad Up/Down     | Camera zoom in / out                                |
| D-pad Left/Right + LB  | Cycle characters                                    |
| Menu button            | OOC / Phone menu                                    |
| L4/R4 (Steam grip)     | Supported; mappings open / player configurable      |
| Trackpad click (DualSense / Steam) | Supported; mapping open / player configurable |


### Modifier Layer Summary

The controller maps 10 action bar slots onto 3 face buttons (X/Y/B) using modifier layers:


| Layer     | Modifier held | Face buttons | Slots         |
| --------- | ------------- | ------------ | ------------- |
| **Base**  | None          | X / Y / B    | 1 / 2 / 3     |
| **LT**    | LT            | X / Y / B    | 4 / 5 / 6     |
| **LB**    | LB            | X / Y / B    | 7 / 8 / 9     |
| **LB+LT** | LB + LT       | X            | 10            |
| **LB+LT** | LB + LT       | Y            | Camera toggle |
| **LB+LT** | LB + LT       | B            | Reserved      |


A (bottom face) is never modified — it is always Jump. RT is never modified — it is always contextual action. This keeps the two most time-critical inputs (jump and interact/grab) accessible without any modifier.

### D-pad Modifier Combos


| D-pad input | No modifier   | + LT                    | + LB             |
| ----------- | ------------- | ----------------------- | ---------------- |
| Left/Right  | Weapon switch | Transform / Untransform | Cycle characters |
| Up          | Posture up    | —                       | Camera zoom in   |
| Down        | Posture down  | —                       | Camera zoom out  |


---

## Keyboard & Mouse Map


| Action                                      | Key                |
| ------------------------------------------- | ------------------ |
| Look (1st person) / Orbit camera (3rd person + RMB) | Mouse      |
| Move (view-relative in 1st person, screen-relative in 3rd person) | W / S / A / D |
| Walk                                        | Left Ctrl          |
| Sprint                                      | Shift              |
| Jump                                        | Space              |
| Dodge                                       | V                  |
| Contextual Action (interact, pick up, grab) | E                  |
| Action slots 1–10                           | 1–9, 0             |
| Zoom (scope, binoculars) — 1st person       | Right Mouse Button |
| Zoom (scope, binoculars) — 3rd person       | Middle Mouse Button (RMB is camera orbit) |
| Camera zoom in / out                        | Scroll Wheel       |
| Posture down                                | C                  |
| Posture up                                  | Z                  |
| Weapon switch                               | Q                  |
| Transform / Untransform                     | T                  |
| Cycle characters                            | F1–F4              |
| Camera toggle (1st/3rd)                     | F5                 |
| Phone / Menu                                | Esc or Tab         |


Keyboard users have direct access to all 10 action bar slots without modifier layers. The number row provides 1:1 mapping — what you see on the bar is what you press. This is the primary advantage of KB+M over controller for combat.

---

## Camera Mode Controls

Movement and camera inputs behave differently depending on the active camera mode (1st person / 3rd person). The toggle is F5 (KB+M) or LB+LT+Y (controller). See [Camera Creative Brief](CB-camera.md) for the CAMERA drone system.

### First Person (Eye Implants)

Standard FPS controls — mouse directly controls where Meghan looks.

**KB+M:**

| Input | Action |
| ----- | ------ |
| Mouse | Pan and tilt — directly controls Meghan's view direction |
| W / S | Move forward / backward relative to view direction |
| A / D | Strafe left / right relative to view direction |

**Controller:**

| Input | Action |
| ----- | ------ |
| Right Stick | Pan and tilt — directly controls Meghan's view direction |
| Left Stick | Move relative to view direction (forward/strafe) |

Movement is **view-relative** — W always moves toward where the camera is looking. Turning is immediate and continuous. This is the standard FPS model.

### Third Person (CAMERA Drone)

The CAMERA drone orbits Meghan. Camera movement requires explicit input; the camera tracks Meghan but does not turn with her movement.

**KB+M:**

| Input | Action |
| ----- | ------ |
| RMB + Mouse left/right | Orbit the CAMERA drone horizontally around Meghan |
| RMB + Mouse up/down | Orbit the CAMERA drone vertically (up/down) around Meghan |
| Scroll Wheel | Zoom the drone closer to / further from Meghan |
| W | Move Meghan toward the top of the screen |
| S | Move Meghan toward the bottom of the screen |
| A | Move Meghan toward the left of the screen |
| D | Move Meghan toward the right of the screen |

**Controller:**

| Input | Action |
| ----- | ------ |
| Right Stick | Orbit the CAMERA drone around Meghan (horizontal and vertical) |
| Left Stick | Move Meghan relative to screen direction |
| LB + D-pad Up/Down | Camera zoom in / out |

Movement is **screen-relative** — W (or left stick up) moves Meghan toward the top of the screen, not toward where the camera is looking. The camera continues to track Meghan but does not rotate with her movement. The player explicitly orbits the camera with RMB+mouse (KB+M) or right stick (controller).

**Why screen-relative in 3rd person:** In third-person, the camera sits behind and above Meghan. View-relative movement (where W = toward camera look direction) creates a disconnect when the camera is angled — the player pushes "forward" but Meghan moves at an angle to the screen. Screen-relative movement keeps the visual feedback intuitive: push up, character moves up on screen.

**Camera tracking:** The CAMERA drone automatically follows Meghan's position. It does not auto-rotate to face the direction of movement — the player controls the orbit independently. This gives full control over framing without the camera fighting the player's intent.

---

## Posture System

Four posture states for movement and stealth:


| Posture         | Detection profile | Movement speed | Access                                   |
| --------------- | ----------------- | -------------- | ---------------------------------------- |
| **Standing**    | Full visibility   | Full speed     | Default; all actions available           |
| **Crouching**   | Reduced           | Moderate       | Cover use; improved ranged accuracy      |
| **Crawling**    | Low               | Slow           | Tight spaces; vents                      |
| **Belly Crawl** | Minimal           | Very slow      | Under obstacles; lowest possible profile |


Transitions are sequential — D-pad Down steps one posture level lower, D-pad Up steps one level higher. The player cannot skip directly from standing to belly crawl; they must step through crouch and crawl. Each step has a brief transition animation.

Posture affects [Stealth](../../mgaPriv/mechanics/skills/stealth.md) effectiveness, [Evasion](../../mgaPriv/mechanics/skills/evasion.md) options, and ranged accuracy (see [Traversal Creative Brief](CB-traversal.md)).

---

## Contextual Action — RT / E

The contextual action input is context-sensitive based on what is near the player:


| Context                       | Press/release                                 | Hold                           |
| ----------------------------- | --------------------------------------------- | ------------------------------ |
| Near an interactable object   | Interact (open door, activate terminal, talk) | —                              |
| Near a pickup item            | Pick up                                       | —                              |
| Near an NPC in combat         | —                                             | Grab (initiates grapple chain) |
| Near a ledge during traversal | —                                             | Ledge grab (hang, climb)       |
| Carrying an object            | Set down                                      | —                              |
| Near a movable object         | —                                             | Object carry (push/pull)       |


Press/release fires on button up — a quick tap. Hold fires after the **hold threshold** (default 0.25s, tunable 0.15s–0.50s in [Settings Menu — Controls](CB-settings-menu.md)). This prevents accidental grabs when the player intends to interact.

---

## Rebinding

All keys and controller bindings are fully remappable through the settings menu. See [Settings Menu — Controls](CB-settings-menu.md) for the rebinding UI design, including:

- Standard rebind table for keyboard (click binding, press new key, conflict detection)
- Modifier-layer checkboxes for controller (select None / LT / LB / LT+LB layer, then assign face buttons and D-pad within that layer)
- Reset to defaults for both KB+M and controller
- Per-profile rebinds as an open question (may follow HUD widget profile pattern)

---

## Use Cases

### UC1 — Basic Exploration (Learning Controls)

**Actor:** New player
**Goal:** Walk through Avernus City for the first time, learning fundamental controls
**Context:** Chapter 1, post-immigration. Meghan is exploring on foot. No combat, no pressure.

**Controller experience:**

1. Player pushes the left stick slightly — Meghan walks slowly, looking around. The analog speed curve gives fine control over pace
2. Player pushes the stick fully — Meghan runs. No sprint yet; full deflection is her default jog
3. Player approaches a door — a contextual prompt appears ("RT: Open"). Player presses and releases RT — door opens
4. Player sees an item on the ground — prompt changes ("RT: Pick up"). Press/release RT picks it up
5. Player pushes the right stick — the CAMERA drone orbits around Meghan. Meghan does not turn with the camera — the player controls framing independently of movement
6. Player presses D-pad Down — Meghan crouches. Prompt: "D-pad Up to stand." Player presses D-pad Up — back to standing
7. Player finds a low gap under a fence — prompt suggests crouching. D-pad Down twice (crouch → crawl) lets Meghan through

**KB+M experience:**

1. W moves Meghan toward the top of the screen. RMB + mouse orbits the CAMERA drone — screen-relative movement in third person
2. E interacts with doors and items — a single key for all contextual actions
3. C to crouch, Z to stand back up. C again from crouch enters crawl
4. Shift to sprint when the player wants to move faster

**What the player learns:** Move, look, interact, posture. Four inputs cover basic exploration.

---

### UC2 — Combat Action Bar

**Actor:** The player in first combat encounter
**Goal:** Use the action bar to fight while keeping jump, dodge, and interact available at all times
**Context:** Gang attack in Chapter 1. Meghan has a small number of actions — a few punches, a kick, maybe one defensive move.

**Step by step:**

1. Combat begins — enemies approach. The action bar is visible at the bottom of the screen with the player's configured actions
2. **KB+M:** Player presses 1 — fires the action in slot 1 (e.g., right hook). Presses 2 — fires slot 2 (e.g., low kick). Direct, immediate
3. **Controller:** Player presses X — fires slot 1. Presses Y — fires slot 2. Presses B — fires slot 3. Three actions available without any modifier
4. Enemy swings — player presses V (KB+M) or R3 (controller) to dodge. Dodge is **not on the action bar** — it is always available, even mid-folder navigation
5. Enemy stumbles — player presses E / RT to grab. Contextual action is **not on the action bar** — always available
6. Player needs to jump over an obstacle during the fight — Space / A. Jump is **not on the action bar** — always available

**Key insight:** The three dedicated inputs (jump, dodge, contextual action) are never consumed by the action bar. A player navigating deep into a folder for a specific ability can still dodge an incoming attack without backing out of the folder first.

---

### UC3 — Controller Modifier Layers

**Actor:** Controller player
**Goal:** Access all 10 action bar slots using modifier layers
**Context:** Mid-game combat with a fully loaded action bar.

**Layer walkthrough:**

1. **Base layer (no modifier):** Player presses X → slot 1, Y → slot 2, B → slot 3. These are the most-used combat actions — placed here for zero-modifier access
2. **LT layer:** Player holds LT, presses X → slot 4, Y → slot 5, B → slot 6. The left trigger is the first modifier. Player releases LT to return to base layer
3. **LB layer:** Player holds LB, presses X → slot 7, Y → slot 8, B → slot 9. The left bumper is the second modifier
4. **Double modifier (LB+LT):** Player holds both LB and LT, presses X → slot 10. This is the deepest slot — reserved for the least frequently used action
5. LB+LT + Y toggles camera mode (1st/3rd person). LB+LT + B is reserved for future assignment

**Muscle memory development:**

- Slots 1–3 (base) are immediately intuitive — same as any action game
- Slots 4–6 (LT) feel natural within an hour — LT-as-modifier is familiar from aim-down-sights in shooter muscle memory
- Slots 7–9 (LB) require deliberate practice — the player is building a new motor pattern
- Slot 10 (LB+LT+X) is for rarely used but important actions (e.g., a heal, a transform ability). Two-modifier access is slow but acceptable for non-time-critical actions

**UI feedback:** The action bar visually highlights which layer is active. Holding LT shifts the bar display to show slots 4–6 in the primary position. Holding LB shows 7–9. Holding both shows slot 10 and utility bindings.

---

### UC4 — Traversal Under Pressure

**Actor:** The player during a delivery job or chase sequence
**Goal:** Chain movement actions fluidly — sprint, jump, grab ledges, change posture — while under time pressure
**Context:** Courier delivery with a timer, or fleeing from pursuers.

**Controller flow:**

1. Player pushes left stick fully and clicks L3 — sprint toggle activates. Meghan runs at full speed
2. Approaching a gap — player presses A mid-sprint — Meghan jumps. Sprint maintains through the jump landing if the stick is still forward
3. Jump falls short of a ledge — player holds RT — Meghan grabs the ledge. Still holding RT, she hangs. Player pushes left stick up — she climbs over
4. Narrow vent ahead — player taps D-pad Down twice (standing → crouch → crawl). Meghan enters the vent
5. Exiting the vent — player taps D-pad Up twice (crawl → crouch → standing). Immediately back to sprint with L3
6. Enemy appears — player can dodge (R3) or jump (A) without interrupting the traversal flow. The dedicated inputs are always available

**KB+M flow:**

1. Shift held for sprint, Space for jump, E to grab ledge
2. C twice for crawl, Z twice to return to standing
3. V to dodge if needed

**What this demonstrates:** Traversal inputs (sprint, jump, grab, posture) and the three dedicated buttons work together without ever touching the action bar. The action bar is for abilities; movement is handled by dedicated controls.

---

### UC5 — Stealth Posture Cycling

**Actor:** The player during an infiltration
**Goal:** Cycle through posture states to manage detection while moving through guarded areas
**Context:** Hacking contract or exploration of a restricted area.

**Step by step:**

1. **Standing** — Meghan approaches a guarded area at normal height. Full speed, full detection profile
2. Player presses D-pad Down (or C on KB+M) — **Crouch**. Speed drops to moderate. Detection profile shrinks. Meghan can use waist-height cover
3. A guard patrol passes. Player presses D-pad Down again — **Crawl**. Speed drops further. Detection profile is low. Meghan fits through vents and under obstacles
4. An open area with no cover — player presses D-pad Down again — **Belly Crawl**. Minimal speed, minimal detection. Meghan is flat on the ground. Useful for crossing open ground when detection would mean failure
5. Guard passes. Player presses D-pad Up — **Crawl**. D-pad Up again — **Crouch**. D-pad Up again — **Standing**. Each step up is one button press
6. **Spotted!** Player needs to flee. D-pad Up rapidly (if not already standing) → L3 sprint toggle → run. The transition from belly crawl to sprint requires three D-pad Up presses plus L3 — deliberate slowness. Being caught in belly crawl is punishing because recovery to full speed takes real time

**Detection profile per posture:**


| Posture     | Visual detection | Movement sound | Speed     |
| ----------- | ---------------- | -------------- | --------- |
| Standing    | 100%             | Full           | Full      |
| Crouching   | ~60%             | Reduced        | Moderate  |
| Crawling    | ~30%             | Low            | Slow      |
| Belly Crawl | ~15%             | Minimal        | Very slow |


Exact values scale with [Stealth](../../mgaPriv/mechanics/skills/stealth.md) skill level.

---

### UC6 — Scope and Ranged Combat

**Actor:** The player engaging a target at range
**Goal:** Use zoom to aim precisely and fire from the action bar
**Context:** Ranged combat or scouting with binoculars.

**Controller:**

1. Player holds RB — zoom activates. If a scope or binoculars are equipped, the appropriate zoom level applies. Without equipment, this is a basic camera zoom
2. Right stick provides aiming — sensitivity may differ in zoomed mode (configurable in [Settings Menu](CB-settings-menu.md))
3. Player presses X/Y/B (or LT/LB + face button) to fire the assigned ranged action from the action bar. The action bar operates normally while zoomed
4. Player releases RB — zoom deactivates, returning to normal camera

**KB+M:**

1. Player holds Right Mouse Button — zoom activates
2. Mouse provides aiming — raw mouse input, no acceleration (unless configured)
3. Player presses 1–9 or 0 to fire from the action bar
4. Release Right Mouse Button to exit zoom

**Two zoom systems:** RB / Right Mouse Button is **scope zoom** — activates a scope or binoculars overlay and enters aiming mode. **Camera zoom** (Scroll Wheel on KB+M, LB + D-pad Up/Down on controller) adjusts the CAMERA drone's orbit distance — closer in or further out — and is available at all times, not just during combat. Scope zoom overrides camera zoom while held.

**Camera interaction:** Scope zoom overrides the normal third-person camera distance. In first-person mode, scope zoom provides additional magnification. The transition between normal and zoomed view is smooth, not a hard cut.

**Posture bonus:** Crouching while zoomed provides an accuracy bonus to ranged actions. Standing is default accuracy. Moving while zoomed applies an accuracy penalty.

---

### UC7 — Minigame Input Reuse

**Actor:** The player in the brainwave sync minigame (knockout recovery)
**Goal:** Use the same action bar inputs in a different context — controlling brain wave synchronisation
**Context:** Meghan is knocked out. The brainwave sync minigame activates (see [Knockout & Recovery Creative Brief](CB-knockout-recovery.md)).

**How action bar inputs map to waves:**

The brainwave sync minigame has 5 wave bands. Each band has two controls (up/down), requiring 10 inputs — exactly the 10 action bar slots.


| Action bar slot | Wave control         |
| --------------- | -------------------- |
| 1 / 2           | Delta wave up / down |
| 3 / 4           | Theta wave up / down |
| 5 / 6           | Alpha wave up / down |
| 7 / 8           | Beta wave up / down  |
| 9 / 10          | Gamma wave up / down |


**Controller modifier mapping:**

- Delta (slots 1/2): X / Y — base layer, no modifier. The easiest wave to control
- Theta (slots 3/4): B (base) / LT+X — crosses layers. Slightly slower access
- Alpha (slots 5/6): LT+Y / LT+B — full LT layer
- Beta (slots 7/8): LB+X / LB+Y — full LB layer
- Gamma (slots 9/10): LB+B / LB+LT+X — hardest to reach, matching the narrative difficulty of the highest-frequency wave

**KB+M:** Slots 1–9 (keys 1–9) plus slot 10 (key 0). Direct access to all 10 inputs without modifiers — KB+M players have a slight mechanical advantage in this minigame.

**Design implication:** The minigame difficulty on controller is partly driven by the modifier layer system. Shallow knockouts (1–2 waves, base layer only) are mechanically easy. Deep knockouts (4–5 waves, all layers active) require the same modifier fluency as full combat. This is intentional — the brainwave sync is harder when the player is already under pressure.

---

### UC8 — Input Device Switching

**Actor:** The player switching between controller and KB+M mid-session
**Goal:** Seamless transition between input methods with automatic prompt updates
**Context:** Player sets down controller to type in chat, or picks up controller from desk.

**Behaviour:**

1. Both input devices are always active. No manual selection required
2. The game detects the **last input received** and swaps UI prompts accordingly:
  - Controller input detected → prompts show gamepad icons ("A", "RT", "LB+X")
  - KB+M input detected → prompts show key names ("Space", "E", "LT+5")
3. The swap is instantaneous — no transition animation, no confirmation dialog
4. Action bar slot labels update to reflect the current input method (number keys for KB+M, face button + modifier icons for controller)
5. The swap does not interrupt gameplay — it can happen mid-combat, mid-traversal, mid-menu

**Edge cases:**

- If both devices send input simultaneously (player bumps controller while using KB+M), the most recent input wins. No conflict resolution needed
- The game remembers the last-used device across sessions for initial prompt display on boot
- Settings menu sensitivity and dead zone values are per-device — switching devices does not change the other device's configuration

---

### UC9 — Transform and Character Switching

**Actor:** The player managing transformation and party members
**Goal:** Transform Meghan into Ava (magical girl form) and cycle between party members using D-pad modifier combos
**Context:** Pre-combat preparation or mid-combat tactical switching.

**Transform (controller):**

1. Player holds LT and presses D-pad Left or Right — Meghan transforms into Ava (or Ava untransforms into Meghan)
2. The transformation animation plays. The action bar may swap to a different configuration if the player has set up separate civilian/Ava bar layouts
3. The D-pad direction (left or right) does not matter — both trigger the same transform toggle. This is intentional: the player does not need to remember a direction, just the modifier

**Transform (KB+M):**

1. Player presses T — transform/untransform toggle. Single key, no modifier needed

**Character cycle (controller):**

1. Player holds LB and presses D-pad Left or Right — cycles through available party members (Meghan/Ava, Nagi, Arri, etc.)
2. D-pad Left cycles backward, D-pad Right cycles forward through the party order
3. The camera and control transfer to the selected character. The previously controlled character reverts to AI behaviour

**Character cycle (KB+M):**

1. F1–F4 select party members directly by slot. F1 is always Meghan/Ava

**Why D-pad + modifier:** Unmodified D-pad Left/Right is weapon switch — the most common D-pad action. Transform and character cycle are less frequent and tolerate the modifier cost. The modifier also prevents accidental transforms during weapon switching.

---

### UC10 — Extended Controller Input (Grip Buttons, Trackpads)

**Actor:** Player using a controller with extra inputs (Steam Controller, Steam Deck, DualSense)
**Goal:** Use grip buttons and trackpad click as additional configurable shortcuts
**Context:** Any gameplay.

**Supported extended inputs:**

| Input | Available on |
|-------|-------------|
| L4 / R4 (grip buttons) | Steam Controller, Steam Deck |
| Trackpad click | DualSense (PS5), Steam Controller |

All extended inputs are **supported but unmapped by default**. The game recognises them as valid inputs but does not assign default actions. Players configure them through the standard rebinding interface in the settings menu.

**Trackpad implementation:** The trackpad is exposed as a single click input only — not as a 2D surface. The game does not use trackpad position or touch/swipe data. Players who want trackpad-as-mouse or trackpad-as-joystick behaviour can configure that through Steam Input or system-level remapping, which the game does not interfere with.

**Suggested uses (not enforced):**

- L4: Dodge (mirroring R3 for players who dislike stick clicks)
- R4: Contextual action (mirroring RT for quicker access)
- Trackpad click: Map, phone toggle, or any single-press action
- L4: Quick posture toggle (standing ↔ crouching)
- R4: Zoom (mirroring RB)

**Why open:** These inputs are not available on all controllers. Mapping critical functions to them would create a hardware advantage. Keeping them as player-configurable shortcuts provides value without penalising standard gamepad users.

**Steam Input compatibility:** The game's binding system should not conflict with Steam Input's remapping layer. If a player has configured extended inputs through Steam Input, the game should respect those bindings without double-mapping.

---

## Open Items

- ~~Exact hold threshold for RT contextual action~~ — RESOLVED: default 0.25s, tunable 0.15s–0.50s in settings menu
- Whether folder tap behaviour (slot 1 / last used) interacts with modifier layers on controller
- L4/R4 default mapping — currently open; may assign defaults after playtesting identifies common player preferences
- LB+LT + B reserved slot — potential future assignment (party commands, emote wheel, map)
- Whether the action bar visual layer indicator is sufficient or whether a persistent modifier state icon is needed on-screen
- Analog sprint curve tuning — how does stick deflection map to speed between walk and run?
- Whether posture transitions have i-frames or vulnerability windows during animation
- Per-profile control bindings — whether control profiles follow the HUD widget profile pattern (see [CB-hud-modification](CB-hud-modification.md))

