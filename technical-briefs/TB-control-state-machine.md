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

The key axis (from the consolidated control scheme): **1st- vs 3rd-person changes ONLY movement + look;
everything else is mode-agnostic.** So most categories are shared and only Move/Look swap.

| State | Active control categories |
|---|---|
| **OnFoot · FirstPerson** | Modifiers + General + OnFoot + **Move-FP + Look-FP** |
| **OnFoot · ThirdPerson** | Modifiers + General + OnFoot + **Move-TP + Look-TP** (orbit) |
| **Vehicle** (FP/TP TBD) | Modifiers + General + **Vehicle-Move/Look** (replaces OnFoot move/look) |
| **Menu / Paused** | *(all gameplay categories removed)* — CommonUI input only |

- **Shared** across all gameplay states: **General** (action-bar slots), **OnFoot** (jump/dodge/interact/
  posture/…), and the **Modifier** sub-layers.
- **Swapped** by state: **Move / Look** (per the *full-split* decision — each mode owns its own bindings),
  and eventually **Vehicle**.
- **Transitions:** `CameraToggle` (F5 / LB+LT+Y) flips FirstPerson↔ThirdPerson; contextual mount/dismount →
  Vehicle; opening a menu → Menu (see gating below).

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
| **`IMC_Move_FP`** | `MoveForward/Back/StrafeL/StrafeR` (view-relative) — KB WASD; pad Left Stick |
| **`IMC_Look_FP`** | `LookUp/Left/Right/Down` (direct) — KB Mouse XY (+ optional IJKL alt); pad Right Stick |
| **`IMC_Move_TP`** | same four directions, **screen-relative** — KB WASD; pad Left Stick |
| **`IMC_Look_TP`** | orbit — KB **RMB+Mouse** (+ IJKL alt); pad Right Stick |
| **`IMC_Vehicle`** | TBD |

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

- **M1 (now):** the **category re-cut of the assets** + the **categorized/collapsible rebind screen** so the
  screen is architecturally correct and every abstract action is rebindable. Rebinds persist + validate.
- **Post-M1 (needs the pawn/gameplay):** the **live CSM** (state transitions, the modifier sub-machine, menu
  gating, maintenance task) and the **mode-state consumers**. The rebind screen is the edit surface; the CSM
  is the consumer, built with the gameplay milestone.

## Open items
- **Vehicle controls** — unspecced (CB-traversal mentions vehicles, no bindings). Define before Vehicle state is real.
- **Full-split redundancy** — Move-FP/TP default to the same WASD; confirm players want independent per-mode rebinds vs a "same as 1st person" convenience.
- **Look directional keys vs mouse** — exact grey-out rule + whether IJKL alt ships in M1 or later.
- **KB modifier layers** (Ctrl/Shift chords) — post-M1 (see TB-controls-rebind).

## Related
- [TB — Controls Rebind](TB-controls-rebind.md) — the rebind screen + the per-command/per-cell modifier model + the deferred-runtime notes this generalizes.
- [CB — Controls](../creative-briefs/CB-controls.md) · [CB — Camera](../creative-briefs/CB-camera.md) — source scheme + 1st/3rd-person modes.
- [CB — HUD Modification](../creative-briefs/CB-hud-modification.md) — per-mode HUD profiles (a CSM consumer).
</content>
