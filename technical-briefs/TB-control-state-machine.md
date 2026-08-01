# TB — Control State Machine & Input-Mode Architecture

> **Status: design spec (2026-07-22).** Ratified direction from Peter: **abstract the controls and drive
> them from a state machine that slots in the right control set for the current situation** (1st/3rd person,
> vehicle, menu…), with a self-healing maintenance pass and the mode-state reusable by other systems. This
> TB defines that architecture, the mode/category IMC model, and how the rebind screen
> ([TB — Controls Rebind](TB-controls-rebind.md)) is a window into it. Engine facts = UE 5.8 (`d:/uegit`).

## Core principle

Controls are **abstract actions** (device-agnostic Input Actions), grouped into **categories**, and a
**Control State Machine (CSM)** decides which categories are *active* at any moment by adding/removing their
**Input Mapping Contexts (IMCs)**. The player rebinds the abstract actions; the CSM decides *when* each set
is live. The rebind screen's categories are the CSM's control sets, 1:1.

Three properties Peter wants from this:
1. **Situation-driven:** the active control set always matches the current mode (on-foot 1st/3rd person,
   vehicle, menu).
2. **Self-healing:** a lightweight **maintenance task** verifies the active IMC set matches the current
   state each tick/interval and re-asserts it if something drifted (a missed transition, a bug) → controls
   can never get "stuck" in the wrong mode.
3. **Shared signal:** the CSM's current mode is a **published state** other systems key off — HUD widget
   profile, camera rig, animation set, save/streamer logic, etc.

## Modes (states)

> **REVISED 2026-07-30.** The earlier model had **two** on-foot modes (1st / 3rd person). There are
> **three**, and the axis that matters is not first-versus-third person — it is **DIRECT vs POINTER**,
> i.e. **whether the mouse cursor is captured or free**. First-versus-third person is a *camera*
> variant inside Direct, and the two share their control set entirely.

### The three on-foot modes

| Mode | Cursor | Look input | Move | Camera anchored to |
|---|---|---|---|---|
| **First Person** | **captured** | rotates the character | WASD, character-relative | the character's eyes |
| **Third Person Direct** | **captured** | rotates the character; **camera-adjust modifier** repositions the drone | WASD, character-relative | **the character** — park it over her shoulder and it stays there through every turn |
| **Third Person Pointer** | **free** | **RMB + drag** orbits | **click a destination** *or* **WASD, camera-relative** (`W` = up-screen); click to interact; click the action bar | **the world** — point it north and it holds north while she turns beneath it |

**First Person and Third Person Direct share controls completely.** Look rotates the character in
both, move is character-relative in both. The only differences are where the camera sits and whether
the camera-adjust modifier is available. Third Person Direct is therefore **First-Person controls plus
a momentary camera-adjust layer**, not a third control set.

**Third Person Pointer is the genuinely different one.** It is a point-and-click paradigm — the whole
game is *playable* with the mouse alone: click to move, click to interact when in range, click the
action bar. `RMB` is its camera-adjust modifier, because the bare mouse is a world cursor and cannot
also be a look axis. **WASD remains fully live** alongside the cursor, camera-relative — mouse-only is
an option, not a requirement.

> **Cursor capture is the hard boundary**, and it is a hard boundary in the engine too: captured vs
> free is `GameOnly` vs `GameAndUI`, cursor visibility, world hit-testing under the pointer, and
> different CommonUI routing. Everything else — HUD profile, action bar, animation set — layers on top.

### Active control categories

| State | Active control categories |
|---|---|
| **OnFoot · FirstPerson** | Modifiers + General + OnFoot + **Move-FP + Look-FP** |
| **OnFoot · ThirdPersonDirect** | *(as FirstPerson)* + **`IMC_CameraAdjust`** (momentary) |
| **OnFoot · ThirdPersonPointer** | Modifiers + General + OnFoot + **Move-TPP + Look-TPP** (cursor/click + RMB orbit; **WASD also live**, camera-relative) |
| **Vehicle** (TBD) | Modifiers + General + **Vehicle-Move/Look** (replaces OnFoot move/look) |
| **Menu / Paused** | *(all gameplay categories removed)* — CommonUI input only |

- **Shared** across all gameplay states: **General** (action-bar slots), **OnFoot** (jump/dodge/interact/
  posture/…), and the **Modifier** sub-layers.
- **Swapped** by state: **Move / Look**, and eventually **Vehicle**.
### Transitions — TWO separate toggles, deliberately (decided 2026-07-30)

| Action | Binding | Flips |
|---|---|---|
| **`CameraToggle`** | `F5` · pad **LB+LT+Y** | First Person ↔ Third Person Direct |
| **`PointerModeToggle`** | `PgUp`/`PgDn` · pad **LB+LT+RS-press** | Pointer ↔ non-Pointer |

**Both remain available in combat.** The earlier worry — that changing cursor capture mid-fight is
jarring — was answered: jarring only harms when it is *unexpected*, and these are player-initiated, so
the player has already committed and made the mental adjustment. **Automatic** switching would be the
problem; there isn't any.

**But the two transitions are deliberately not equally easy.** First↔Third is a moment-to-moment
flick. Pointer↔non-Pointer changes the whole interaction paradigm, so its binding is chosen to be
*awkward on purpose*:

- **Pad:** every other `LB+LT` combo lives on the **face buttons** (`LB+LT+X` = slot 10, `LB+LT+Y` =
  CameraToggle), so the thumb rests on the face for all of them. Requiring **RS-press** forces the
  thumb *off* the face — a deliberate reposition that will not happen by accident mid-combat.
- **KB:** `PgUp`/`PgDn`, chosen for distance from the movement cluster.

Contextual mount/dismount → Vehicle; opening a menu → Menu (see gating below).

**Naming note:** "Indirect" was considered and rejected — *indirect control* already means influencing
autonomous agents (the Sims lineage), and Pointer mode is still fully direct commanding, just through
a cursor. **Pointer** names the mechanism the state machine actually branches on. A friendlier
player-facing label (e.g. "Command") on the settings screen is fine; the enum wants the mechanism.

## Abstract actions → category IMC model (the re-cut)

Our current IMCs are cut by **device + modifier layer** (`IMC_KBM`, `IMC_Pad_Base/LT/LB/LBLT`). The CSM
needs them cut by **category**, so each state is "a list of categories to activate." Target set:

| Category IMC | Holds (both KB & pad mappings unless noted) |
|---|---|
| **`IMC_Modifiers`** | `ModLT`, `ModLB` (pad). *(Player-added KB modifiers = post-M1.)* |
| **`IMC_General`** | Action slots: **KB `1`–`0` (all 10, direct)** + **pad base `X/Y/B` = slots 1-3** |
| **`IMC_General_ModA`** | pad **LT+X/Y/B = slots 4-6** *(swapped in by the modifier sub-machine)* |
| **`IMC_General_ModB`** | pad **LB+X/Y/B = slots 7-9** |
| **`IMC_General_ModAB`** | pad **LB+LT+X = slot 10** (+ CameraToggle on Y) |
| **`IMC_OnFoot`** | Jump, Dodge, Interact, Sprint, Walk, PostureUp/Down, Zoom, WeaponSwitch, Transform, Party (select/cycle), CameraToggle, PhoneMenu, CameraZoomIn/Out |
| **`IMC_Move_FP`** | `MoveForward/Back/StrafeL/StrafeR` (character-relative) — KB WASD; pad Left Stick. **Used by BOTH First Person and Third Person Direct** |
| **`IMC_Look_FP`** | `LookUp/Left/Right/Down` (direct) — KB Mouse XY (+ optional IJKL alt); pad Right Stick. **Used by BOTH First Person and Third Person Direct** |
| **`IMC_CameraAdjust`** | **NEW (2026-07-30)** — momentary layer that *shadows Look* to reposition the drone instead of turning the character. KB **`Alt` + Mouse**; pad **LB + Right Stick**. Only active in **Third Person Direct** |
| **`IMC_Move_TPP`** | same four directions, **camera-relative** — `W` moves the character *up-screen*, `S` down-screen. **POINTER mode only.** WASD is a full first-class input here, not a fallback: click-to-move and WASD both work |
| **`IMC_Look_TPP`** | orbit — KB **RMB + Mouse** (+ IJKL alt); pad Right Stick. **POINTER mode only** — RMB is required because the bare cursor is a world pointer |

> **There is deliberately no `IMC_Move_TPD` / `IMC_Look_TPD`.** Third Person Direct uses
> `IMC_Move_FP` / `IMC_Look_FP` unchanged. The `_FP` suffix therefore covers **two** modes — kept
> as-is rather than renamed to `_Direct`, so do not "fix" it later thinking it is a mistake.
>
> **`Alt` is free** — `Left Ctrl` is Walk and `Shift` is Sprint, and nothing else claims Alt.
> **Caveat: `PhoneMenu` has an alternate binding on `Tab`** (see TB-controls-rebind), so holding Alt to
> adjust the camera and tapping Tab for the phone is **Alt+Tab** — window-switch on Windows in
> windowed/borderless, which the video settings offer. Either move `PhoneMenu`'s alternate off Tab or
> accept it as an unlikely pairing. `MMB` was the earlier candidate and was dropped: middle-mouse drag
> fights stiff wheel-clicks and accidental scroll.
| **`IMC_Vehicle`** | TBD |

> **`LB + Right Stick` composes cleanly with the existing pad layer.** `LB` is already a modifier
> (`LB + X/Y/B` = action slots 7–9), so `LB + RS` adds a camera meaning to the same layer without
> collision — different physical control, same modifier. LB reads consistently as "the secondary
> layer" rather than a special case.
>
> **`MMB` is the weaker half of the KBM pair.** Holding middle-mouse while dragging is awkward on many
> mice (stiff wheel-clicks, accidental scroll mid-drag) and is often already spoken for. `Alt` is the
> established free-look modifier in this role (WoW, FFXIV) and is comfortable to hold while moving the
> mouse. Rebindable either way — but the default sets the first impression.

**Move/Look are Axis2D actions with per-direction *named* mappings** (so each direction is independently
rebindable, per the QuickMoveLook sketch): e.g. `IA_Move_FP` (Axis2D) whose W/A/S/D mappings carry override
`PlayerMappableKeySettings` `MoveForward_FP` / `StrafeLeft_FP` / … . Same for Look (Up/Left/Right/Down). The
**gamepad stick is one 2D input, not four directions** — no per-direction pad binding; the only pad control
for Move/Look is the global **Swap Sticks** toggle. Look's directional keys **grey out while Mouse XY is the
active look input**.

*(This supersedes the earlier device-cut IMCs; those 5 become the seed to re-cut from — see Asset re-cut.)*

## The state machine

**Top-level CSM** (one active gameplay state at a time; Menu is an overlay that suspends gameplay input):
- On `BeginPlay` → default state (e.g. OnFoot·ThirdPerson), which adds `IMC_Modifiers + IMC_General + IMC_OnFoot + IMC_Move_TP + IMC_Look_TP`.
- `CameraToggle` → swap `Move/Look_TP` ↔ `Move/Look_FP` (remove one pair, add the other); publish the new mode.
- Mount/dismount → swap OnFoot move/look for `IMC_Vehicle` (and back).

**Nested modifier sub-machine** (within any gameplay state, per [TB — Controls Rebind] runtime section):
`IMC_General` (base) stays active; on `ModLT`/`ModLB` `Started`/`Completed`, add/remove
`IMC_General_ModA/ModB/ModAB` (higher priority; consume-input shadows the base face buttons). Momentary
(active only while held).

**Menu / pause gating:** opening a menu pushes the **Menu** state → remove all gameplay IMCs (or use EI
native input-mode filtering: tag gameplay IMCs to a "Game" input-mode). CommonUI routes input to the active
`CommonActivatableWidget`; closing restores the prior gameplay state. (No separate menu gameplay IMC.)

## Maintenance / self-heal task

A cheap periodic check (timer or subsystem tick): compute the **expected** IMC set from the current mode +
modifier state, diff against the **actually-added** contexts on the `EnhancedInputLocalPlayerSubsystem`, and
**re-assert** (add missing / remove stray) on mismatch. Guarantees controls can't get wedged in the wrong
set after a missed transition or a bug. Log a warning when it corrects drift (surfaces the underlying bug).

## Mode state as a shared signal

The CSM publishes its current mode (an enum + a change delegate / gameplay-message). Consumers:
- **HUD** — widget profile per mode (1st vs 3rd person layouts already differ; menu hides HUD).
- **Camera** — which rig/behaviour (eye-implant view vs CAMERA drone orbit vs vehicle chase).
- **Animation / traversal** — posture & movement model.
- Later: streamer-mode / save context, AI awareness, etc.

## Rebind screen ↔ CSM (collapsible categories)

The Controls tab renders **one collapsible category per CSM control set**, in order:
**Modifiers · 1st-Person Move/Look · 3rd-Person Move/Look · General Actions · On-Foot · Vehicle.** Each
category is a collapsible header; **Move/Look inside the person categories are themselves collapsible
axis-groups** (parent summary row `WASD | – | Left Stick` / `Mouse XY | IJKL | Right Stick`, expanding to the
four directional child rows). So the screen is a direct view of the state machine's sets — editing a binding
edits that abstract action wherever the CSM slots it.

## Asset re-cut (from what we have)

Today: `IMC_KBM` + `IMC_Pad_Base/LT/LB/LBLT` (device+layer cut), 34 `IA_`. Re-cut to the category IMCs above:
- **Split `IMC_Pad_Base`** into `IMC_General` (slots 1-3 + KB 1-0) · `IMC_OnFoot` (the dedicated actions) ·
  `IMC_Move_*`/`IMC_Look_*` (the sticks) · `IMC_Modifiers` (ModLT/LB).
- **Rename** `IMC_Pad_LT/LB/LBLT` → `IMC_General_ModA/ModB/ModAB` (they're the slot layers).
- **New per-mode Move/Look IAs** (`IA_Move_FP/TP`, `IA_Look_FP/TP`) with per-direction named mappings;
  retire the single `IA_Move`/`IA_Look` (or keep FP as the rename).
- **Vehicle** IMC/IAs: stub (TBD).

## M1 boundary

- **M1 (done):** the **category re-cut of the assets** + the **categorized/collapsible rebind screen** so the
  screen is architecturally correct and every abstract action is rebindable. Rebinds persist + validate.
- **Post-M1 (needs the pawn/gameplay):** the **live CSM** (state transitions, the modifier sub-machine, menu
  gating, maintenance task) and the **mode-state consumers**. The rebind screen is the edit surface; the CSM
  is the consumer, built with the gameplay milestone.

## M2 boundary (decided 2026-07-30)

**CoreX-M2 is the gameplay milestone this TB deferred to** — the pawn arrives, so the live CSM does too,
in a limited cut.

**IN for M2 — DIRECT modes only:**
- **First Person** and **Third Person Direct**, plus the `CameraToggle` between them. These are cheap
  together because they **share a control set**; the delta is camera placement and the adjust layer.
- **Menu gating.** Not optional — M2 Pillar D is main menu → Game Modes → Delivery Girl → possess,
  which is a real state transition.
- The **published mode signal**, even with only the camera consuming it initially.

**OUT for M2:**
- **Third Person Pointer — and the reason is navigation, not camera code.** Click-to-move needs
  pathfinding, which needs a **navmesh**. M2 ships a hand-baked megablock of static meshes with no
  navigation work in scope, and nav-mesh-vs-nav-graph is still open research. **Pointer mode arrives
  with navigation**, in a later milestone.
- **Vehicle** state.
- The **modifier sub-machine** — nothing consumes action slots until an action bar exists, which is the
  same reasoning that put mgaPub #27 in M3.

**This also settles M2 Pillar C's camera.** "Third-person camera at its simplest useful setting" is
concretely **Third Person Direct**: character-steered, camera at a fixed body-relative offset. The
camera-adjust layer is optional for M2 — a fixed over-the-shoulder offset is walkable without it.

## Open items
- ~~**Does `CameraToggle` reach Pointer mode?**~~ **RESOLVED 2026-07-30** — no. Two separate actions
  (`CameraToggle`, `PointerModeToggle`), both combat-available, with the Pointer one deliberately
  awkward to reach. See Transitions.
- **`PgUp`/`PgDn` — toggle pair or cycle?** Two keys for a two-state flip is one more than needed;
  is `PgUp` "enter Pointer" and `PgDn` "leave", or is it a cycle through all three modes?
- **`CB-camera` UC1 contradicts this TB and needs updating.** It says *"Mouse left/right orbits the
  drone horizontally"* with no modifier, which now matches no mode: Direct uses the mouse to turn the
  character (MMB to adjust the drone), and Pointer needs RMB because the bare cursor is a world
  pointer. UC1 predates the three-mode split.
- ~~**Camera-adjust default on KBM** — `MMB` vs `Alt`~~ **RESOLVED 2026-07-30: `Alt`.** Verified free
  (`Left Ctrl` = Walk, `Shift` = Sprint, nothing on Alt). **Residual: the Alt+Tab collision** — move
  `PhoneMenu`'s alternate off `Tab`, or accept it.
- **Does the camera offset persist across mode switches?** Park the drone over her shoulder in Direct,
  switch to Pointer and back — same offset, or reset?
- **Vehicle controls** — unspecced (CB-traversal mentions vehicles, no bindings). Define before Vehicle state is real.
- **Full-split redundancy** — largely resolved: Direct modes now *share* one Move/Look pair, so the
  redundancy is only Direct-vs-Pointer, which genuinely differ.
- **Look directional keys vs mouse** — exact grey-out rule + whether IJKL alt ships in M1 or later.
- **KB modifier layers** (Ctrl/Shift chords) — post-M1 (see TB-controls-rebind).

## Related
- [TB — Controls Rebind](TB-controls-rebind.md) — the rebind screen + the per-command/per-cell modifier model + the deferred-runtime notes this generalizes.
- [CB — Controls](../creative-briefs/CB-controls.md) · [CB — Camera](../creative-briefs/CB-camera.md) — source scheme + 1st/3rd-person modes.
- [CB — HUD Modification](../creative-briefs/CB-hud-modification.md) — per-mode HUD profiles (a CSM consumer).
</content>
