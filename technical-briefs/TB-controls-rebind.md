# TB — Controls Rebinding (Implementation) · WS4 Build Spec

> **Status: draft for review (2026-07-21).** This is the concrete build spec for **CoreX-M1 WS4**
> (controls remap screen). It expands the single Controls row in
> [TB — Settings Menu](TB-settings-menu.md) (§Controls) and the layout direction captured in the
> `controls-screen-design` session note into an authorable asset list + C++/BP task breakdown.
> Design source of truth for the *scheme*: [CB — Controls](../creative-briefs/CB-controls.md);
> for the *screen*: [CB — Settings Menu](../creative-briefs/CB-settings-menu.md) §3.

## Scope

**In (M1):** rebind the default **keyboard/mouse** and **gamepad** bindings over Enhanced Input
user-settings; controller **modifier-layer** editing (LT / LB / LB+LT); **conflict detection**;
auto-switching controller **glyphs**; the Controls tab shell (`WBP_ControlsTab`) + sensitivity /
dead-zone / invert / hold-threshold scalar rows.

**Out (deferred):** the runtime *consumption* of these bindings by gameplay (no pawn/action-bar
exists in M1 — nothing reads the input yet); per-HUD-profile control profiles (structure only);
Steam-Input interop; camera-tab orbit scalars (own tab). We build the **edit + persist + validate**
surface; the **apply-to-gameplay** surface lands with the action bar in a later milestone.

## Decisions locked this session (2026-07-21)

1. **Persistence = native `UEnhancedInputUserSettings`** (reuse-UE). Not our own config. Enable via
   `bEnableUserSettings=true` in the Enhanced Input developer settings. Rebinds persist to the
   engine's own save slot, keyed by **mapping name**. (The one consequence to note: control rebinds
   live in that save slot, **not** `GameUserSettings.ini` where Video/Audio/UI-scale live. The scalar
   rows — sensitivity, dead zones, invert, hold-threshold — still go in `UOgmMgaGameUserSettings`.)
2. **This doc is written before any asset/C++ work** — Peter reviewed the asset list + the A-vs-B
   layer decision before authoring.

**Ratified (Peter, 2026-07-21) — the forks below are now closed:**
- **Layer model = A (layer-IMCs).** (§"Layer model — RESOLVED".)
- **Modifier checkboxes** enable/disable each layer; modifiers are **momentary** — a layer is active
  only while its modifier is physically held (**no sticky / latch / toggle**).
- **KB/M** default = direct keys `1`–`0`, **no** modifiers; but the player **may add** modifier keys
  (e.g. Ctrl/Shift + `1`-`4`, freeing the hand from WASD; `5`-`0` still need a hand move — player's
  choice). Native user-settings rebinds a **single** key, so *player-added* KB chords need the same
  optional-layer mechanism as the pad — treated as a **post-M1 extension**; the data model must not
  preclude it. M1 ships the direct KB layout.
- **Movement:** rebindable on **KB**; on **controller** only a **Swap Sticks** toggle (LS ↔ RS), not
  free-rebind. **Look:** on KB **locked to mouse**, but **additional look keys may be added**.
- **Party cycle:** **KB = F1–F4 direct-select** (four `IA_SelectPartyMember1..4`); controller =
  D-pad **prev/next** (`IA_CyclePartyPrev/Next`).
- **Per-HUD control profiles:** structure now (empty `ProfileIdString`), UI deferred (Peter undecided
  on the feature).
- **User-data location:** co-locate the EI save slot with `GameUserSettings.ini` / `Aridecan.ini`
  under the redirected user dir — folds into the existing (deferred) user-file relocation task.

## The reuse spine (what the engine gives us)

| Need | Engine system (reuse) |
|---|---|
| Persist rebinds per player | `UEnhancedInputUserSettings` (a `USaveGame`; `AsyncSaveSettings()` after edits) |
| Set / clear a binding | `MapPlayerKey(FMapPlayerKeyArgs, FGameplayTagContainer& FailureReason)` / `UnMapPlayerKey` |
| Reset a row / profile | `ResetAllPlayerKeysInRow(args, reason)` · profile `ResetToDefault()` |
| Enumerate rebindable rows | profile `GetPlayerMappingRows()` → `TMap<FName, FKeyMappingRow>` |
| "who else uses this key" | `GetMappingNamesForKey(FKey, OutNames)` — **global**, not layer-aware (see §Conflict) |
| KB vs gamepad on one action | `FMapPlayerKeyArgs.HardwareDeviceId` + `Slot` — one **mapping name** carries both a KB key and a pad key; the sub-tab filters by device |
| Controller glyphs by pad | **CommonInput**: `UCommonInputBaseControllerData` per pad + `CommonInputSubsystem::GetCurrentGamepadName` + `CommonActionWidget` draws the live glyph |

`FMapPlayerKeyArgs` fields we use: `MappingName`, `Slot` (`EPlayerMappableKeySlot::First..Fourth`
= primary/secondary bindings for the *same* action), `NewKey`, `HardwareDeviceId`,
`bCreateMatchingSlotIfNeeded` (add a binding to an empty secondary slot), `ProfileIdString` (empty
= active profile; the multi-profile hook for per-HUD control profiles later).

## Project enablement (editor-closed, do first)

1. **`Config/DefaultInput.ini`** under `[/Script/EnhancedInput.EnhancedInputDeveloperSettings]`
   (dev settings are `config = Input`): `bEnableUserSettings=True` and
   `UserSettingsClass=/Script/EnhancedInput.EnhancedInputUserSettings` (or a game subclass if we
   later add fields — not needed for M1).
2. **Clear the UE-template legacy mappings** from `DefaultInput.ini` — the `+ActionMappings`
   (`Jump`) and `+AxisMappings` (`Move*`, `Look*`, `Turn*`) blocks are dead weight for an Enhanced
   Input game and will confuse the rebind surface. Keep the `AxisConfig` dead-zone lines and the
   `DefaultPlayerInputClass=/Script/EnhancedInput.EnhancedPlayerInput` /
   `DefaultInputComponentClass=/Script/EnhancedInput.EnhancedInputComponent` lines.
3. **Build.cs** — add **`CommonInput`** (and `CommonUI` if we reference its types in C++) to
   `OGMMGA.Build.cs`. `EnhancedInput` is already a dep.

## Asset inventory (editor-open, Peter drives)

### Input Actions (`IA_`)  — `/Game/MGA/Input/Actions/`

One `IA_` per **logical action**; the mapping **name** = the `IA_`'s `PlayerMappableKeySettings.Name`.
Digital button actions are `bool`; movement/look are `Axis2D`; the two modifiers are `bool` triggers.

- **Action bar:** `IA_ActionSlot1` … `IA_ActionSlot10` (mapping names `ActionSlot1`…`ActionSlot10`).
- **Movement / look:** `IA_Move` (Axis2D — rebindable on KB; on pad = Left Stick, only a **Swap
  Sticks** toggle), `IA_Look` (Axis2D — KB **locked to mouse** but accepts **added** look keys; pad =
  Right Stick, subject to Swap Sticks). Swap Sticks = `config` bool `bControllerSwapSticks` (not an
  `IA_`).
- **Modifiers (NOT in the rebindable slot set):** `IA_ModLT`, `IA_ModLB` — drive the layer swap.
- **Dedicated (never-modified) actions:** `IA_Jump`, `IA_Dodge`, `IA_Interact`, `IA_Sprint`,
  `IA_Walk`, `IA_Zoom`, `IA_CameraZoomIn`, `IA_CameraZoomOut`, `IA_PostureUp`, `IA_PostureDown`,
  `IA_WeaponSwitch`, `IA_Transform`, `IA_CameraToggle`, `IA_PhoneMenu`.
- **Party:** `IA_SelectPartyMember1`…`4` (KB direct-select F1–F4) + `IA_CyclePartyPrev` /
  `IA_CyclePartyNext` (controller D-pad prev/next). Both drive the same party-switch system.

### Input Mapping Contexts (`IMC_`) — `/Game/MGA/Input/`

Modelled per **Approach A (layer-IMCs)** — ratified (see §"Layer model — RESOLVED").

- **`IMC_KBM`** — keyboard/mouse; **no modifier layers by default** (slots 1-10 = direct number keys).
  Player-added KB modifiers (Ctrl/Shift + digit) are a **post-M1 extension** built on optional KB
  modifier-layer IMCs mirroring the pad; the model reserves room for them but M1 ships direct keys.
- **`IMC_Pad_Base`** — gamepad, no modifier held.
- **`IMC_Pad_LT`** — gamepad, higher priority; pushed while `IA_ModLT` held.
- **`IMC_Pad_LB`** — gamepad, higher priority; pushed while `IA_ModLB` held.
- **`IMC_Pad_LBLT`** — gamepad, highest priority; pushed while both held.

Layer priority = the mechanism that makes "modifier shadows the base face button": while `IMC_Pad_LT`
is active and consuming X, the base `IMC_Pad_Base` X (Slot1) does not fire. (Enhanced Input context
priority + consume-input; free with Approach A.) In M1 nothing pushes/pops these at runtime yet —
that swap component lands with the pawn. For M1 the IMCs exist so the **rebind UI has rows to edit**.

> **AUTHORING GOTCHA (UE 5.8, learned 2026-07-22).** An IMC's mappings live in the newer struct
> **`DefaultKeyMappings.Mappings`** (`FInputMappingContextMappingData`), **not** the top-level `Mappings`
> array — the flat `Mappings` was **deprecated in 5.7** (`GetMappings()` returns `DefaultKeyMappings.Mappings`).
> The editor writes to `DefaultKeyMappings`; scripting/MCP must set `defaultKeyMappings.mappings` and leave the
> deprecated flat array empty, or the mappings are silently ignored at runtime. (MCP note: `set_properties`
> does incremental array *diffing* — it errors on a grow-with-changes on a non-empty array; clear via
> `reset_properties(["defaultKeyMappings"])` first, then set the full array. Instanced `modifiers` are created
> by passing a class path, e.g. `/Script/EnhancedInput.InputModifierSwizzleAxis` (default `Order = YXZ` — the
> WASD-forward swizzle needs no property edit). Built this way 2026-07-22: 34 `IA_` + 5 `IMC_`.)

### Default bindings

**Keyboard / Mouse (`IMC_KBM`)** — one row per mapping name, KB device:

| Mapping name | Default key | | Mapping name | Default key |
|---|---|---|---|---|
| `ActionSlot1`…`9` | `1`…`9` | | `Sprint` | Left Shift |
| `ActionSlot10` | `0` | | `Walk` | Left Ctrl |
| `Jump` | Space | | `Zoom` | RMB (3rd-person: MMB) |
| `Dodge` | V | | `CameraZoomIn`/`Out` | Scroll Up / Down |
| `Interact` | E | | `PostureUp` / `PostureDown` | Z / C |
| `WeaponSwitch` | Q | | `Transform` | T |
| `SelectPartyMember1`…`4` | F1…F4 | | `CameraToggle` | F5 |
| `PhoneMenu` | Esc (alt Tab) | | `Move` / `Look` | WASD / Mouse (Look adds keys) |

KB party switch = four direct-select actions (F1–F4). `Move` is fully rebindable; `Look` keeps the
mouse locked as its primary and only accepts **added** keys.

**Gamepad — by layer:**

| Layer / IMC | X (FaceButton_Left) | Y (FaceButton_Top) | B (FaceButton_Right) | Other |
|---|---|---|---|---|
| **Base** `IMC_Pad_Base` | `ActionSlot1` | `ActionSlot2` | `ActionSlot3` | A=`Jump`·RT=`Interact`·R3=`Dodge`·L3=`Sprint`·RB=`Zoom`·D-pad↑↓=`PostureUp/Down`·D-pad←→=`WeaponSwitch`·Menu=`PhoneMenu` |
| **LT** `IMC_Pad_LT` | `ActionSlot4` | `ActionSlot5` | `ActionSlot6` | D-pad←→=`Transform` |
| **LB** `IMC_Pad_LB` | `ActionSlot7` | `ActionSlot8` | `ActionSlot9` | D-pad←→=`CyclePartyPrev/Next`·D-pad↑↓=`CameraZoomIn/Out` |
| **LB+LT** `IMC_Pad_LBLT` | `ActionSlot10` | `CameraToggle` | *(reserved)* | — |

**A (Jump) and RT (Interact) are locked** — the rebind UI must present them read-only (CB-controls:
the two time-critical inputs are never modified). Enforce in the UI + the conflict layer.

## The three custom pieces (the actual WS4 work)

### 1. Modifier-first layout + the two modifier checkboxes

The Controls tab is **two sub-tabs**: **Keyboard & Mouse** and **Controller** (CB-settings-menu §3).

**Controller sub-tab layout (session direction, 2026-07-21):**
1. **Modifiers first** — surface LT and LB at the top (the two-modifier structure up front).
2. Then the **base bindings** (slots 1-3, dedicated actions, D-pad).
3. Then the **LT / LB / LB+LT layer groups** (slots 4-6 / 7-9 / 10) — one editable group per layer,
   matching the CB-settings-menu "edit one layer at a time" model.
4. **Per-command modifier checkboxes (REVISED 2026-07-22, Peter).** NOT two global bottom checkboxes.
   Instead **every boolean-command row carries two checkboxes — Modifier 1 / Modifier 2** (not on
   Move/Look). Checking one/both/neither sets the modifier combo that command fires under: this is the
   **per-command chord model**, player-defined per row. Defaults seed from CB-controls (slots 4-6 = Mod1,
   7-9 = Mod2, 10 = both). A **help line at the top of the page**: *"Selecting both modifiers means both
   must be pushed at the same time."* And the **modifier keys themselves are rebindable** — `IA_ModLT`/
   `IA_ModLB` (now player-mappable, display "Modifier 1"/"Modifier 2") appear as rebind rows **at the top**
   so the player can change which physical button each modifier is.
   - **Backend consequence:** the *base key* is a native EI rebind (`MapPlayerKey`); the *modifier flags*
     per command are **our own stored state** (not native rebind data) — persist per mapping name (custom
     config / profile extension). `ValidateRebind`'s "layer" = exactly these flags (supersedes the static
     pad-layer registry; the 5 pad IMCs become the default seed). Runtime consumption deferred (no pawn in
     M1). Modifiers remain **momentary** (held, not latched).

> **Reconciliation:** CB-settings-menu §3 describes a *layer selector* (None/LT/LB/LT+LB radio, pick
> the layer you're editing). The 2026-07-21 direction adds the **two enable-checkboxes at the bottom**.
> These are compatible: the layer *groups* are the "which layer am I editing" surface; the checkboxes
> are the enable toggles. Fold the radio into "grouped rows per layer, all visible."

### 2. Conflict / uniqueness detection — the load-bearing custom logic

**Hard requirement (CB-controls):** no two commands may share the same **button + modifier-set**.

Why the engine can't do it: `GetMappingNamesForKey(X)` is **global** — it returns `ActionSlot1`
(base) *and* `ActionSlot4` (LT) *and* `ActionSlot7` (LB) because all three legitimately map gamepad-X.
That is **not** a conflict — they're in different modifier layers. The engine has no notion of our
layers. So the check must be **layer-scoped**, and that scope is our data.

**Design — `UAridGInputRebindLibrary` (framework, `AridG` prefix; reusable) — BUILT (CL 116):**
- **`EAridGModifierLayer` = `{ Base, ModA, ModB, ModAB }`** (generic; ModA=LT, ModB=LB). No `KBM` value —
  keyboard is handled by the device branch below, not a layer.
- A **pad-layer registry**: `mapping name → EAridGModifierLayer`, passed as a `TMap` (a small `UDataAsset`
  later). Derived from the pad IMCs (2026-07-22): **ModA** `ActionSlot4/5/6`,`Transform`; **ModB**
  `ActionSlot7/8/9`,`CyclePartyPrev/Next`,`CameraZoomIn/Out`; **ModAB** `ActionSlot10`,`CameraToggle`;
  **Base** everything else with a pad key. KB-only names (`Walk`,`SelectPartyMember1-4`) never carry a pad key.
- `ValidateRebind(Settings, TargetMappingName, ProposedKey, LayerByMapping, LockedMappings)` — takes the
  active profile's `GetMappingNamesForKey(ProposedKey)`, then filters **device-dependently** (the key crux,
  learned while authoring the assets — layer is a function of *(name, device)*, not name alone):
  - **KB/mouse key** (`!ProposedKey.IsGamepadKey()`): no modifier layers → **any** other mapping on that
    key conflicts (binding `Jump`→`4` MUST clash with `ActionSlot4`→`4`, even though `ActionSlot4` is ModA
    on the *pad*). The registry is ignored here.
  - **Gamepad key**: keep only candidates whose pad layer == the target's pad layer (X = Slot1/Slot4/Slot7
    across layers is legitimate). Registry consulted here.
  - Locked target (`A`=Jump, `RT`=Interact) → `bTargetIsLocked`, reject outright.
- Returns `FAridGRebindConflict{ ConflictingMappings, bTargetIsLocked, bHasProblem }`. On a problem the UI
  does CB behaviour: **warn + offer swap or cancel** (swap = `MapPlayerKey` both rows). Resolve policy = UI.

Thin `BlueprintFunctionLibrary`, one file (`AridGInput` plugin). BP calls it from the rebind-capture flow
before committing `MapPlayerKey`.

> **SECOND EXCLUSION AXIS — control context (added 2026-07-25, fixing a real bug).** The modifier layer
> alone is not enough. As first written, the KB/mouse branch treated *every* other mapping on a key as a
> conflict — so **the 1st-person Move/Look bindings blocked the 3rd-person ones** (both default to WASD +
> mouse), and neither could be set while the other held the key. But per
> [TB — Control State Machine](TB-control-state-machine.md) §Modes, `IMC_Move_FP`/`IMC_Look_FP` and
> `IMC_Move_TP`/`IMC_Look_TP` are **never active at the same time** — the CSM slots in one pair or the
> other — so they cannot collide.
>
> `ValidateRebind` therefore takes a second registry, **`ContextByMapping : TMap<FName, FName>`** (mapping
> name → exclusive **context group** name), and a candidate is a real conflict only when **both** axes
> permit it: same modifier layer (gamepad only) **and** `ContextsCanCoexist(TargetContext, CandidateContext)`.
> Coexistence rule: `None` (= always active) overlaps everything; two *named* groups overlap only if
> identical. Mappings absent from the registry default to `None`, so Modifiers/General/OnFoot keep
> conflicting with everything — including with whichever person-mode set is live.
>
> The registry is **derived from the IMC assets**, same anti-drift reasoning as `BuildPadLayerRegistry`:
> `BuildContextRegistry(TArray<FAridGControlContextGroup>)` where each group is `{ContextName, Contexts[]}`.
> MGA's groups: `"FirstPerson"` = `IMC_Move_FP` + `IMC_Look_FP`; `"ThirdPerson"` = `IMC_Move_TP` +
> `IMC_Look_TP`; `IMC_Vehicle` joins as `"Vehicle"` when it is real. A mapping named by two groups is an
> IMC authoring error — it demotes to always-active (over-reports rather than under-reports) and logs a
> warning. The generic `FName` group (rather than an FP/TP enum) keeps the `AridG` plugin game-agnostic.
>
> **Wiring note:** the new pin is `AutoCreateRefTerm`, so an existing `ValidateRebind` node just *gains*
> a pin and keeps its wires — but an unwired pin means an empty map, which is exactly the old (broken)
> behaviour. `WBP_ControlsTab.InitTab` must build the registry next to the pad-layer one and push it to
> each row, and the row must feed it to `ValidateRebind`.

### 3. CommonInput auto-glyphs (reuse, don't build)

Author one **`UCommonInputBaseControllerData`** per pad family (Xbox / DualSense / Switch Pro) mapping
`FKey → glyph brush`; register in the CommonInput platform settings. Each binding row's glyph is a
**`CommonActionWidget`** (or `CommonButtonBase`) bound to the action — it draws the correct pad's glyph
and **swaps live** when `CommonInputSubsystem` detects a different gamepad. KB+M rows show key-name
text. No custom glyph code — this is configuration.

> **BUILT 2026-07-22 (Windows), P4 CL 118:** Xelu CC0 sets under `/Game/MGA/UI/Glyphs/` (XBox 22, PS5 25,
> SteamDeck 32, KBM 95) + `BP_CommonInputData_Generic` (18 Xbox gamepad brushes), registered in
> `DefaultGame.ini`. **Its `GamepadName=Windows` is a Windows-only expedient — the glyph lookup matches the
> name EXACTLY with no fallback, and each platform's default gamepad name is its own platform name.** Before
> Linux/Mac bring-up, read [TB — CommonInput Per-Platform Gamepad Glyphs](TB-commoninput-platform-gamepad.md)
> or gamepad glyphs silently vanish off-Windows.

## The UI — `WBP_ControlsTab`

Another **`WBP_SettingsTabBase`** child (same contract as `WBP_VideoTab` / `WBP_AudioTab`:
`Content` NamedSlot fill, `InitTab` / `ApplyTab` / `RevertTab` overrides, `OnBackRequested`). Hosted
in `WBP_Settings`' `TabContent` switcher at the existing **Controls placeholder (index 4)**.

- **Content structure (BUILT 2026-07-22 via MCP):** `Root_Controls` (VBox) = **`Txt_Help`** (the
  "both modifiers = both pushed together" line) → **`Box_ModifierKeys`** (the Modifier 1/2 rebind rows,
  at top) → **`HeaderRow`** (column labels) → **`Scroll_Bindings`** (command rows). *(A KB&M / Controller
  device sub-tab split is deferred — one combined list for M1.)*
- **`WBP_RebindRow` — FOUR-COLUMN, PER-CELL-MODIFIER model (REVISED 2026-07-22, Peter):** a row =
  **`Action | KB+M primary | KB+M secondary | Gamepad`** (no trailing Mod columns). The two KB columns map to
  Enhanced Input's native **First / Second key slots** (keyboard device); Gamepad = First slot on the gamepad
  device. **Each of the 3 binding cells is a `VerticalBox`** = a key/glyph **button** on top
  (`Txt_KBPrimary` / `Txt_KBSecondary` / `Img_GamepadGlyph`) over a **`M1 ☐  M2 ☐` row** (small label +
  checkbox per modifier). So **modifier flags are PER-BINDING** (per device+slot), not per-command — a
  command's KB-secondary can be Shift+1 (M1 on that cell) while its gamepad is LT+X (M1 on that cell),
  independently. Widgets (all vars): `Txt_Label`; per cell `Cell_*` VBox → `Btn_*`(+`Txt_*`/`Img_GamepadGlyph`)
  + `Mods_*` HBox → `Chk_{KB1,KB2,Pad}Mod{1,2}`. Capture → `ValidateRebind` → conflict prompt or commit
  (`MapPlayerKey` on the right Slot+device + `AsyncSaveSettings`); the cell's M1/M2 flags feed the chord.
  - **KB+M secondary doubles as the "add a modifier to a key" slot** (e.g. bind slot 8 to Shift+1 without
    leaving WASD) — the modified-key (chord) capture is a **post-M1** refinement; the column is its home.
  - **`Tab` is `IA_PhoneMenu`'s KB secondary** (Esc primary + Tab secondary — Esc kills PIE). Built into
    `IMC_KBM` (PhoneMenu = [Escape, Tab]).
- **Conflict is per-CONTEXT.** Today one context ("walking" = our IMC set); a future "driving" context =
  separate IMCs where keys may legitimately repeat. `ValidateRebind` scope = same key + same modifier-set
  **within one context** (context = which IMC set is active). One context in M1.
- **Rows built from the profile** (`GetPlayerMappingRows`); modifier IAs (`IA_ModLT`/`ModLB`, now
  player-mappable, display "Modifier 1"/"Modifier 2") populate `Box_ModifierKeys` at top; the rest go in
  `Scroll_Bindings`, each seeding its Mod1/Mod2 checkbox defaults from the pad-layer flags.
- **`InitTab`**: load the active profile, populate rows, preselect glyphs + KB key text, seed each row's
  Mod1/Mod2, read the scalar rows from `UOgmMgaGameUserSettings`.
  - **CANONICAL ORDER (2026-07-24, Peter-ratified) — driven by a `CanonicalMappingOrder : Array<Name>`
    member var** (52 mapping names, category-grouped, on `WBP_ControlsTab`). InitTab **iterates that list**
    (not `GetPlayerMappingRows` directly) and `Map·Find`s each row by name; because the list is grouped,
    first-seen section creation yields canonical **section** order (Modifiers · 1st Person · 3rd Person ·
    General Actions · On-Foot) *and* stable **row** order for free. **Why:** a loaded Enhanced Input save
    lists *customized* rows first in `GetPlayerMappingRows` → iterating the map directly drifts both section
    and row order after any rebind. Order lives in one editable place (no `NN_` prefix on functional mapping
    names → no save-key churn). List content = the `ControlName_*` keys in `ST_Settings` (every mappable row
    has one). Move/Look **parent** rows (`Move_FP`/`Look_FP`, the stick binding) sit before their 4 directional
    rows so they collapse cleanly into the future axis sub-groups.
  - **FUTURE WORK (deferred, if needed) — functionalize + leftover-safety.** M1 ships the minimal loop-swap
    (list loop only; a name absent from the profile is skipped; a profile row absent from the list currently
    would **not** render). Harden later by extracting the row-build body into a function `AddMappingRow(Name)`
    called from the list loop, then a second pass over `GetPlayerMappingRows` calling it for any `Key` where
    `CanonicalMappingOrder.Contains(Key)` is false → a new/unlisted control can never silently vanish. Also the
    candidate home if the order ever moves to a `UDataAsset`/C++ (game-side, not the reusable `AridG` plugin).
- **`ApplyTab`**: rebinds are already committed live (deferred-apply is awkward for input capture);
  `ApplyTab` persists the scalar rows + the two layer bools + `SaveSettings`. **Reset to Defaults** =
  profile `ResetToDefault()` + repopulate — now the settings-wide `ResetTab` override, see
  [TB — Settings Menu](TB-settings-menu.md) §"Reset to Defaults".
- **CLEARING a binding — the X button (built 2026-07-25).** Each of the three binding cells carries a
  small **`Btn_Clear*`** ("✕") beside the key button, inside a `KeyLine_*` HorizontalBox (key button
  `Fill 1.0`, X `Automatic`); tooltip key `Controls_ClearBinding`. Clear = **`MapPlayerKey` with
  `NewKey` left at `EKeys::Invalid`** at that cell's address (KB1 = `First`/`KBM`, KB2 =
  `Second`/`KBM`, Pad = `First`/`Gamepad`), `bCreateMatchingSlotIfNeeded` **false**, then
  `AsyncSaveSettings` → `RefreshDisplay` (which already renders an invalid key as *Not Bound*). No
  "make invalid key" node is needed — an untouched `FKey` pin **is** `Invalid`; and the create flag must
  stay false or clearing an empty cell would *create* an empty mapping.
  - **`UnMapPlayerKey` is NOT a clear — it is a reset-to-default** (`EnhancedInputUserSettings.cpp:1271`);
    it and `ResetAllPlayerKeysInRow` belong to the Reset button, not the X.
  - **Why an invalid key sticks (UE 5.8 semantics, verified in engine source).**
    `MapPlayerKey` on an existing mapping calls `FPlayerKeyMapping::SetCurrentKey(Invalid)`;
    `GetCurrentKey()` returns `IsCustomized() ? CurrentKey : DefaultKey` where
    `IsCustomized() == (CurrentKey != DefaultKey)`. So a cleared **primary** slot (real `DefaultKey`)
    reads back Invalid and **serialises** — the save filter is `Mapping.IsDirty() || Mapping.IsCustomized()`.
  - **Edge case worth knowing:** a **created secondary** slot has `DefaultKey == Invalid` too (the engine
    stamps Invalid for slots that never existed in the IMC). Clearing one therefore leaves it neither
    dirty nor customized, so it silently **drops out of the save** on the next write. Harmless — the row
    ceases to exist and `RefreshDisplay` pre-seeds unbound cells anyway — but it means "cleared empty
    secondary" and "never had a secondary" converge on disk.
  - **Locked rows** (`Jump`, `Interact`) hide their M1/M2 checkboxes and **disable both the set and the
    clear button**, so a locked binding cannot be edited or emptied from the screen. The locked-name set
    lives in one place (a pure function on the row) rather than duplicated literals in
    `CaptureChainOnCapture` and `RefreshDisplay`; the durable home is C++ beside `ValidateRebind`.
- **Row-layout STANDARD** (label Fill .4 / control Fill .6 / trailing Auto-right; checkboxes
  HAlign_Left) per the settings house style — see [TB — Settings Menu] and the
  `settings-screen-architecture` note.

Follows the same activation gotchas as the other tabs (real `BP_OnActivated` override → `InitTab`;
the switcher only activates a visible child when the shell is activated).

## Scalar rows (config on `UOgmMgaGameUserSettings`)

Sliders/toggles, applied live, same pattern as Audio volumes (CB-settings-menu §3). Add `config`
fields + Get/Set; no engine rebind involved:

- KB+M: Mouse Sensitivity H/V, Invert Mouse X/Y, Mouse Smoothing, **Hold Threshold** (0.15–0.50s).
- Controller: Stick Sensitivity H/V, Invert Stick X/Y, Inner/Outer Dead Zone, Trigger Sensitivity,
  Rumble, Aim Assist, Hold Threshold.

(Dead zones can also be driven through Enhanced Input modifiers later; for M1 store as scalars and
wire consumption with gameplay. Mark which are cosmetic-only until a pawn reads them.)

## Layer model — RESOLVED: **A (layer-IMCs)** (Peter, 2026-07-21)

Both were valid UE patterns for the modifier layers; A was ratified. Comparison kept for rationale.

| | **A — layer IMCs (recommended)** | **B — chorded actions** |
|---|---|---|
| Structure | 4 gamepad IMCs (Base/LT/LB/LB+LT), pushed on modifier hold | 1 IMC; slot 4-10 actions carry a **Chorded Action** trigger (chord = `IA_ModLT`/`LB`) |
| Rebind fit | **Clean** — each slot is a plain key mapping in its layer IMC; native `MapPlayerKey` per row; layer = owning IMC | **Awkward** — user-settings rebinds the *base key*; the chord modifier isn't a player-mappable key, so "which modifier" isn't natively rebindable |
| "modifier shadows base button" | Free (context priority + consume) | Needs careful trigger/blocker setup so base X doesn't also fire |
| Runtime | Needs a small push/pop component on modifier press/release (deferred to gameplay anyway) | Declarative, no swap component |
| Conflict scope | Layer = owning IMC (clean partition for the registry) | Layer = the chord on the trigger (must read triggers to partition) |

A composes far better with the **native-rebind** decision locked this session; B's only win
(no runtime swap component) is moot in M1 (no runtime consumption yet). This **supersedes the earlier
`controls-screen-design` note's "Chorded Actions" framing** — that framing described how EI *can*
model layers, not a locked choice.

## Runtime input handling (deferred — post-M1, needs the pawn; design captured 2026-07-22)

> **Generalized 2026-07-22 →** these runtime pieces are now part of the **Control State Machine**
> ([TB — Control State Machine](TB-control-state-machine.md)): the modifier swap is its nested sub-machine,
> menu gating is its Menu state, and it adds mode states (1st/3rd person, vehicle) + a self-heal maintenance
> pass. The rebind screen is being re-cut into **collapsible categories = the CSM's control sets**.

M1 builds the *edit + persist + validate* surface; **nothing consumes the bindings at runtime yet**. Two
runtime pieces are designed but not built:

**Modifier-layer swap (the Approach-A runtime).** `IMC_Pad_Base` stays active (priority 0). A small state
machine on the **Mod1/Mod2 IAs' `Started`/`Completed`** events computes the current combo
(none / Mod1 / Mod2 / both) and **adds the matching higher-priority layer IMC** (`_LT` / `_LB` / `_LBLT`),
removing it on release; higher priority + consume-input shadows the base face buttons. **Reconciled with the
per-command checkboxes:** the 4 layer IMCs are **derived from the per-command Mod1/Mod2 flags** — on Apply,
rebuild each layer IMC from the flags (a command with Mod1 set lands in the Mod1 IMC), then the state machine
swaps the regenerated IMCs. (Alternative that skips the state machine: build **chorded-action triggers** from
the flags — EI evaluates the chord natively, no swap code. Same flag state either way; decide at build time —
state machine = explicit/debuggable, chords = less code.)

**Menu / pause gating.** **No separate gameplay KB+M IMC for the menu.** Gate the gameplay IMCs OFF while a
menu is up, two native options: **(a)** remove `IMC_KBM` + `IMC_Pad_*` on menu open, re-add on close
(M1-simple); **(b)** EI **native input-mode filtering** — the IMCs already carry `inputModeFilterOptions` /
`inputModeQueryOverride`; tag gameplay IMCs to a "Game" input-mode tag (needs `bEnableInputModeFiltering` in
the EI dev settings) so switching to "Menu" mode auto-gates them (scalable). On top of either, **CommonUI
routes input to the active `CommonActivatableWidget`** (menu consumes its own navigation input) and pausing
stops world tick — so the menu uses CommonUI input, not the gameplay IMCs.

## Remaining open items (all major forks now closed)

- **Player-added KB modifiers** (Ctrl/Shift + digit) — desired (post-M1); build on optional KB
  modifier-layer IMCs mirroring the pad. Confirm the exact interaction (dedicated modifier IAs the
  player assigns Ctrl/Shift to, vs a fixed Ctrl/Shift layer) when we tackle it.
- **User-data co-location** — Peter wants the EI save slot alongside `GameUserSettings.ini` /
  `Aridecan.ini`. This is the existing deferred user-file relocation task (`writable-data-locations`);
  M1 uses default save-slot location, relocation handled as one cross-cutting pass.
- **Per-HUD control profiles** — structure now (empty `ProfileIdString`); Peter undecided on shipping
  the UI. (TB-settings-menu D2.)

## Build order

1. **Editor-closed C++ / config (now):** enablement (§Project enablement 1-3: DefaultInput.ini flags +
   legacy-mapping strip + Build.cs `CommonInput`); scaffold `UAridGInputRebindLibrary` +
   `EAridGModifierLayer` + the scalar `config` fields on `UOgmMgaGameUserSettings`; full build.
2. **Editor-open (Peter drives):** author the `IA_` set + the `IMC_` set with default bindings +
   `PlayerMappableKeySettings` names; the `UCommonInputBaseControllerData` glyph assets.
3. **WBP:** `WBP_RebindRow` + `WBP_ControlsTab` (device sub-tabs, grouped layer rows, checkboxes,
   capture→validate→commit), hosted at Controls index 4; localize labels into `ST_Settings`.
4. **Verify** in Standalone (rebind persists across relaunch; conflict warn/swap; glyph swap on pad
   change). No gameplay consumption to test in M1.

## Related
- [CB — Controls](../creative-briefs/CB-controls.md) · [CB — Settings Menu](../creative-briefs/CB-settings-menu.md) §3
- [TB — Settings Menu](TB-settings-menu.md) §Controls · [TB — Boot Loader](TB-boot-loader.md)
- [M1 — Framework & CI](../milestones/M1-framework-and-ci.md) WS4
- Session notes: `controls-screen-design`, `settings-screen-architecture`, `naming-conventions-cpp`
</content>
</invoke>
