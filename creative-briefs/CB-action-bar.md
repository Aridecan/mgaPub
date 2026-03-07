# Creative Brief — Action Bar

---

## Overview

The action bar is a **10-slot hotbar** fixed to the bottom of the screen. It holds every executable ability in the game — combat strikes, spells, search, lock picking, hacking, door interactions, grabbing items — and fires them in real time with a single keypress. The bar is always live; the clock never pauses for action selection.

The bar is fully configurable through Meghan's phone. Players assign actions to slots, organise them into nested folders, and save layouts as named profiles. The system rewards mastery of your own configuration — knowing where your actions are and reaching them under pressure is a genuine player skill.

The bar also supports **multiple modes** that swap the displayed layout automatically based on game state. The regular bar shows the player's configured actions; a grapple bar replaces it during grapple contests with state-specific combat options. Each mode is independently configurable.

**Related documents:**

- [Action Bar System](../../mgaPriv/mechanics/skill-bar.md) — full mechanics, input scheme, slot/folder specification
- [Combat System](CB-combat.md) — attack initiation (UC1), grapple mode swap (UC6)
- [HUD Modification](CB-hud-modification.md) — bar as fixed HUD infrastructure, widget profile pattern
- [Controls & Input](CB-controls.md) — full binding reference, modifier logic
- [Combat Mechanics](../../mgaPriv/mechanics/combat.md) — two-phase resolution, resource costs
- [Capture and Restraint](../../mgaPriv/mechanics/capture-restraint.md) — grapple chain, restraint types
- [Skills Overview](../../mgaPriv/mechanics/skills.md) — skill progression, action unlock gates

---

## Display Layout

The bar's visual layout changes based on the **last detected input method** — keyboard or gamepad. The switch is automatic and immediate; if the player touches a keyboard key the bar shows the keyboard layout, if they touch a gamepad input it shows the gamepad layout. Players who use both inputs will see the bar flip between layouts as they switch.

### Keyboard Layout

A straight horizontal row of 10 boxes, each labelled with its key (1–9, 0). Simple, flat, functional.

```
[1] [2] [3] [4] [5] [6] [7] [8] [9] [0]
```

Each box displays the assigned action's icon. No grouping, no elevation — the keyboard has no modifier structure to reflect.

### Gamepad Layout

The 10 slots are displayed in **groups that mirror the face button and modifier structure** of the controller.

```
         [Y]          [Y]          [Y]
      [X]   [B]    [X]   [B]    [X]   [B]    [X]
      ─────────    ─────────    ─────────    ─────
      No mod        +LT          +LB        +LT+LB
      Slots 1-3    Slots 4-6    Slots 7-9    Slot 10
```

The middle button in each group (Y position) is **elevated ~25%** above the left (X) and right (B) buttons, matching the physical face button layout where Y sits higher than X and B. This gives the player an immediate visual map from screen to controller.

The fourth group (LT+LB) displays two buttons: slot 10 in the X position and camera toggle in the Y position (elevated). The B position is not displayed — it is reserved and has no current function. The camera toggle button is shown but is not player-configurable; it always shows the camera toggle action.

### Active Group Highlighting

On the gamepad layout, the **currently active group is visually emphasised** to tell the player which actions are ready to fire with a bare face button press. Exact visual treatment (scale, brightness, outline, colour shift, or combination) is deferred to the feature brief.

When the player holds a modifier:

| Modifier held | Highlighted group | Slots ready |
|--------------|-------------------|-------------|
| None | Group 1 | Slots 1–3 |
| LT | Group 2 | Slots 4–6 |
| LB | Group 3 | Slots 7–9 |
| LT+LB | Group 4 | Slot 10 |

The highlight shift is instantaneous — the moment the modifier is depressed, the active group changes. Releasing the modifier returns the highlight to Group 1. This gives real-time feedback about which modifier layer the player is in before they commit to a face button press.

---

## Use Cases

### UC1 — Firing an Action

**Actor:** The player
**Goal:** Execute an action from the bar in real time

**Step by step:**

1. **Player presses a slot key** (keyboard 1–9, 0; controller X/Y/B with modifier layers — see [Controls](CB-controls.md))
2. **Contextual check** — if the action is contextual (e.g., "Hack Terminal" requires a terminal in reach, "Grab" requires a target in melee range), the system checks whether conditions are met
   - Conditions met: action proceeds
   - Conditions not met: slot shows inactive feedback (greyed icon, brief audio cue). No resource cost. The player can immediately press another slot
3. **Resource cost check** — SP, MP, or both are checked against the action's cost
   - Sufficient resources: cost is deducted at the start of the animation
   - Insufficient resources: action fails to initiate. Brief feedback (stagger animation, failed swing). No cost deducted
4. **Animation plays** — Meghan is committed for the animation's duration. She cannot initiate another action until the animation completes or is cancelled
5. **Action resolves** — collision detection, contest resolution, and damage cascade proceed per [Combat System](CB-combat.md)

**Action cancellation:** The player can cancel mid-animation. SP cost is proportional to time invested (~80% completion = ~80% cost). Cancellation enters recovery frames — there is no free exit from a committed action.

**The bar is always live.** There is no combat mode, no transition, no pause. Pressing a slot key in the middle of a conversation, while walking, or while falling all attempt to fire the action. Actions that require specific conditions (melee range, terminal access) simply fail the contextual check if conditions are not met.

---

### UC2 — Navigating Folders

**Actor:** The player
**Goal:** Reach a nested action using the folder system

**The tradeoff:** Folders let the player access more actions than 10 top-level slots allow, at the cost of additional keypresses. A direct action fires in one keypress; a folder requires at least two.

**Step by step:**

1. **Player presses a folder slot key** — two things can happen depending on the folder's tap behaviour setting:
   - **Slot 1 mode:** tapping fires the action in slot 1 of the folder immediately (one keypress — fast, but only reaches one action)
   - **Last Used mode:** tapping fires the most recently selected action from that folder (one keypress — fast, adapts to play pattern)
2. **Hold to open (~0.5s):** holding the folder key opens the folder for browsing. The bar visually swaps to show the folder's contents as selectable slots
3. **Player presses a sub-key** — selects an action or enters a deeper folder
   - Direct action: fires immediately
   - Sub-folder: bar swaps again to show sub-folder contents. Another keypress required
4. **Charging:** if the selected action supports charging, hold duration beyond the 0.5s open threshold charges the action. Release fires at the charged level

**Folders can nest without limit.** The player decides the depth. A deeply nested action requires many keypresses (1 → 1 → 1 → fire) but frees up top-level slots for frequently used direct actions. A flat bar gives instant access to 10 actions but no more.

**Folder display is consistent across input methods.** On both keyboard and gamepad, entering a folder takes over the full action bar — the folder's contents replace the top-level layout. On gamepad, this means the group layout is temporarily replaced by the folder contents until the player exits the folder or fires an action.

**Player skill expression:** Combat is intentionally paced slightly slower than a pure action game to make multi-keypress folder navigation viable under pressure. Players who invest in learning their layout are rewarded with faster, more diverse action access. Players who haven't memorised their setup will fumble under pressure — this is by design.

---

### UC3 — Configuring the Action Bar

**Actor:** The player
**Goal:** Assign actions to slots and create folder structures

**Interface:** The action bar is configured through Meghan's in-world **smartphone**. This is the same phone used for HUD widget configuration, hacking, communication, and purifier management.

**What the player can do:**

- **Assign an action to a slot** — drag any unlocked action to any of the 10 slots. Actions include everything: combat abilities, Search, Pick Lock, Hack Terminal, Open Door, Grab, and every other executable ability in the game
- **Create a folder** — place a folder in a slot, then assign actions (or sub-folders) to the folder's internal slots
- **Nest folders** — no system-imposed depth limit. The player controls how deep the tree goes
- **Set folder tap behaviour** — per folder: Slot 1 mode (always fires slot 1) or Last Used mode (fires most recent selection)
- **Rearrange freely** — drag actions between slots, between folders, or between top-level and folder positions

**How actions unlock:**

Actions unlock automatically when the parent skill reaches the required level — no purchase needed. A player who reaches Martial Arts level 5 gains "High Kick" immediately. This contrasts with HUD widgets, which require a licence purchase even after reaching the qualifying rank (see [HUD Modification — Actions vs Widgets](CB-hud-modification.md)).

Some actions have additional prerequisites beyond skill level: widget purchases, cybernetics capacity, licences, or passing specific tests. The configuration interface shows these gates clearly — unavailable actions display their requirements.

---

### UC4 — Grapple Mode

**Actor:** The player (as grappler or grappled)
**Goal:** Use grapple-specific actions during an active grapple contest

**Mode swap:** When a Grab connects (RT hold / E hold — see [Controls](CB-controls.md)), the action bar automatically replaces its regular layout with a **grapple-specific layout**. No player input triggers the swap — entering a grapple state does it. The grapple contest is played through action bar choices, not a separate minigame.

**The grapple bar shows different actions depending on role and state:**

**As the grappler (attacker):**

| Grapple state | Available actions |
|--------------|------------------|
| **Grabbed** | Advance to Grappled, Release, Strike (reduced), Rend (clothing) |
| **Grappled** | Advance to Pin, Maintain Hold, Strike (reduced), Rend (clothing), Release |
| **Pinned** | Apply Restraint (requires item + Binding skill), Maintain Pin, Strike (reduced), Rend (clothing), Release |

**As the grappled (defender):**

| Grapple state | Available actions |
|--------------|------------------|
| **Grabbed** | Escape Attempt, Counter-Grapple (if skill sufficient), Strike (reduced), Call for Help |
| **Grappled** | Escape Attempt, Strike (reduced), Call for Help, Submit |
| **Pinned** | Escape Attempt (penalised), Call for Help, Submit |

**Dynamic updates:** Actions update in real time as the grapple state advances or changes. If the grappler advances from Grabbed to Grappled, the bar immediately reflects the new action set. If the defender escapes, the bar restores to the regular layout.

**Configurability:** The grapple bar is configurable — the player can rearrange which grapple actions occupy which slots via the phone. But the *available* actions are fixed by the grapple system. The player cannot add non-grapple actions to the grapple bar or remove grapple actions from it.

**Exit:** The grapple bar restores to the regular layout when the grapple ends — whether by escape, release, or binding completion.

**Contest mechanics:** Each grapple action fires a contested roll (STR + Martial Arts vs STR + Escape Artist for advancement/escape, AGI + Binding vs STR + Escape Artist for restraint application). SP is drained continuously during the grapple for both participants. Full contest details are in [Combat System UC6](CB-combat.md) and [Capture and Restraint](../../mgaPriv/mechanics/capture-restraint.md).

---

### UC5 — Form-Specific Bars

**Actor:** The player
**Goal:** Maintain separate action bar layouts for civilian Meghan and magical girl Ava

**Why separate bars are necessary:**

Civilian Meghan and magical girl Ava have **different available actions**. Meghan's civilian actions — Search, Hacking, social skills, environmental interaction, CQC strikes — are not the same as Ava's transformed actions — elemental abilities, weapon techniques, magical girl combat skills. The two forms are mechanically distinct enough that a single shared bar would force the player to waste slots on actions that are unavailable or irrelevant in their current form.

**How it works:**

The regular mode bar maintains **two independent configurations** — one for civilian form, one for transformed form.

- **Transform** (T key / D-pad Left+LT) → bar swaps to the Ava configuration
- **Untransform** → bar swaps back to the Meghan configuration
- The swap is automatic — no player input needed beyond the transformation itself

**Design intent:** The civilian bar is built around the actions Meghan actually uses as a civilian — Search, Hack Terminal, Pick Lock, social abilities, unarmed combat. The transformed bar is built around what Ava brings to a fight — ice-element attacks, weapon abilities (when the sword is available), magical girl combat skills. The player configures each bar independently to match the action set and playstyle of that form.

**Profiles within forms:** Each form has its own set of saved profiles (see UC6). A player might have a "stealth" Meghan profile and an "exploration" Meghan profile, plus a "solo combat" Ava profile and a "party combat" Ava profile — all independent.

---

### UC6 — Managing Profiles

**Actor:** The player
**Goal:** Save and switch between named action bar configurations

**What profiles are:**

A profile is a named snapshot of the current action bar layout — which actions are in which slots, folder structure, folder tap behaviour settings. Profiles follow the same pattern as [HUD widget profiles](CB-hud-modification.md) — same phone interface, same mental model.

**What the player can do:**

- **Save** the current bar layout as a named profile from the phone menu
- **Load** a saved profile — applies the layout immediately
- **Overwrite** an existing profile with the current layout
- **Rename** or **delete** saved profiles

**Profile sets are separated by mode and form:**

| Profile set | What it stores |
|-------------|---------------|
| **Regular — civilian** | Action bar layout for civilian form |
| **Regular — transformed** | Action bar layout for magical girl form |
| **Grapple** | Grapple action slot arrangement (shared across forms — grapple actions are physical, not form-dependent) |

**Why profiles exist:**

- **Experimentation without risk** — try a radically different layout, revert in seconds if it doesn't work
- **Situational loadouts** — a stealth-focused bar vs a direct combat bar vs an exploration bar, switchable on demand
- **Form specialisation** — different transformed profiles for solo vs party play, or for different enemy types

**Phone access constraints:**

Two conditions prevent profile switching, consistent with widget profiles:

- **Lost phone** — if Meghan's phone is taken or lost, profiles are inaccessible. The currently loaded bar continues to function, but cannot be reconfigured or switched until the phone is recovered
- **Magical girl transformation** — the phone goes into a pocket dimension by default. No phone access means no profile switching during transformation. Whatever profile was loaded before transforming is what Meghan has for the fight. Players who want phone access during transformation can use the toss or uniform pocket options (see [HUD Modification — Widget Profiles](CB-hud-modification.md))

---

## Open Items

- Whether there is a "quick-slot" system for recently or frequently used sub-actions (e.g., a most-used action floats to a quick-access position)
- Folder navigation visual feedback — entry animation, depth indicator, cancel/back input and visual treatment
- Whether folder tap behaviour (Slot 1 / Last Used) interacts with controller modifier layers (LT/LB)
- Grapple mode visual transition — instant swap, slide animation, colour shift, or other treatment when the bar changes modes
- Whether third-party intervention actions appear on the bar when observing an ally being grappled (e.g., "Break Grapple" action)
- Whether the grapple bar shows opponent state (SP, escape progress) as a bar element or if that information belongs on the HUD as a widget
- Profile switching cooldown — whether switching is instantaneous from the phone or has a brief delay
- Maximum number of saved profiles per mode — unlimited, or a cap?
- Whether the bar supports a fourth mode for restraint escape (the 6-stage escape flow from [Escape Artist](../../mgaPriv/mechanics/skills/escape-artist.md) might warrant its own bar state)
- Whether profiles can be shared between playthroughs or are per-save
- LB+LT + B reserved controller slot — potential future assignment
- ~~Gamepad active group highlight — exact visual treatment~~ — DEFERRED to feature brief
- ~~Gamepad Group 4 (LT+LB) display~~ — RESOLVED: display X (slot 10) and Y (camera toggle, non-configurable); omit B (reserved, no function)
- ~~Folder navigation on gamepad~~ — RESOLVED: entering a folder takes over the full bar (same behaviour as keyboard); group layout is temporarily replaced by folder contents
