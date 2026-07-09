# TB — Settings Menu (Implementation)

## Overview

This technical brief is the implementation spec for [CB — Settings Menu](../creative-briefs/CB-settings-menu.md).
It defines **where each setting persists**, **how it applies**, and **which UE system it
reuses**. The guiding rule is the standing one — [reuse UE's own systems](../gdd/boot-manager.md)
wherever they do the job; the settings menu is almost entirely a thin UI over
`UGameUserSettings`, the **Scalability** system, **Enhanced Input** user settings, and
audio **submixes**.

The settings UI is a **CommonUI** front-end (`UCommonActivatableWidget` on a
`UCommonActivatableWidgetStack`), the same stack used by the main menu (see
[TB — Boot Loader](TB-boot-loader.md) R6). It is a single widget reachable from two entry
points — the **Title Menu** (front-end/boot map) and the **in-game** Phone / Lost-Phone
menu — so it must not assume an active gameplay world.

> **Engine-version caveat.** Class/function names below are from the local UE 5.8 source at
> `d:/uegit` (2026-07-09 dive). Treat API names as authoritative, exact signatures as
> navigational.

---

## Storage Architecture

Every setting lands in one of four backends, split on one key axis — **global** vs
**per-playthrough**:

| CB category | Backend (reuse) | Scope | Persist location |
|---|---|---|---|
| 1. Display (res/window/vsync/quality/FoV/HDR) | `UMGAGameUserSettings : UGameUserSettings` + **Scalability** | Global | `GameUserSettings.ini` |
| 2. Audio (volumes, subtitles) | `UMGAGameUserSettings` fields → **submix** sends | Global | `GameUserSettings.ini` |
| 3. Controls (KB+M / controller rebinds) | **`UEnhancedInputUserSettings`** (`USaveGame`) | Global *(multi-profile ready)* | Enhanced Input save slot |
| 4. Camera (drone orbit, per-device) | `UMGAGameUserSettings` fields | Global | `GameUserSettings.ini` |
| 5. Gameplay (difficulty, autosave, HUD, notifications) | `UMGAGameUserSettings` fields | Global | `GameUserSettings.ini` |
| 6. **Content** (adult toggles, streamer mode) | **`content_config`** (Save System) | **Per-playthrough** | playthrough folder |
| 7. Accessibility | `UMGAGameUserSettings` fields | Global | `GameUserSettings.ini` |
| 8. Language / subtitle | **`Aridecan.ini`** (the ABM reads at boot) | Global | `Aridecan.ini` |

**The split rule:** global settings → `GameUserSettings.ini` + `Aridecan.ini` + the Enhanced
Input save; per-playthrough → `content_config` in the active playthrough folder. Switching
playthroughs at the title menu swaps content settings with it (mandated by
[Save System](../gdd/save-system.md)).

**Content section, two contexts:**
- **Title Menu** (no active playthrough): the Content section edits the **global default**
  that New Game inherits (per [CB — Main Menu](../creative-briefs/CB-main-menu.md) UC2 step 2).
- **In-game**: it edits the **active playthrough's** `content_config`.

**One custom settings object.** `UMGAGameUserSettings` (subclass of `UGameUserSettings`,
registered via `[/Script/Engine.Engine] GameUserSettingsClassName=/Script/MGA.MGAGameUserSettings`)
holds every global non-input, non-language setting as `UPROPERTY(config)` fields — camera,
accessibility, gameplay, audio volumes — *alongside* the engine's built-in resolution /
scalability members. One object, one file, native load/save.

---

## Apply Model

Three apply behaviours, per setting class:

| Behaviour | Applies to | Mechanism |
|---|---|---|
| **Live** | Audio, camera, gameplay toggles, accessibility, scalability quality | Set field → `ApplyNonResolutionSettings()` / direct submix or CVar; no confirm |
| **Confirm-or-revert** | Resolution, window mode | Native video-mode flow (below) |
| **Hot-swap** | Language (D1) | Remount `LocText`/`LocVoc` pak + reload string tables (below) |

### Resolution — native confirm/revert

Reuse `UGameUserSettings`'s built-in flow — do **not** reinvent it:

1. `SetScreenResolution()` / `SetFullscreenMode()` → `ApplyResolutionSettings(false)` (applies live)
2. Show "**Keep these settings? Reverting in 15 s…**" dialog
3. Confirm → `ConfirmVideoMode()` + `SaveSettings()`
4. Timeout / reject → `RevertVideoMode()` (restores `LastConfirmed*`) → the
   `OnGameUserSettingsVideoRevert` delegate refreshes the UI

Dirty-state queries (`IsScreenResolutionDirty()`, `IsFullscreenModeDirty()`) drive the
"Apply" button enable state.

### Language — hot-swap, **main-menu only** (D1)

**Language is changed only at the Title Menu, never in the in-game settings menu.** This is
the key simplification: at the main menu no gameplay scene is active and no localized VO
dialogue is playing, so a swap only ever has to refresh **menu text** — there is no
in-progress voice line to interrupt. The in-game (Phone / Lost-Phone) settings menu **hides
the Language category entirely** (see Menu UI).

**Primary: live hot-swap (text).** On change at the Title Menu: write the choice to
`Aridecan.ini`, ask the ABM to mount the new `LocText-<lang>` pak, reload string tables and
rebroadcast so every menu `FText` refreshes (`FInternationalization::SetCurrentCulture` +
string-table reload + a UI refresh event). A `LocVoc-<lang>` change swaps the voice pak too,
but since no dialogue is playing at the menu it simply takes effect when gameplay next
starts — no mid-scene voice handling needed. Text and voice languages are independent (per
[Content & DLC](../gdd/content-and-dlc.md)).

**Fallback (D1):** if even the menu-text live refresh proves fragile, degrade to
**apply-on-next-launch** — persist to `Aridecan.ini`, show a "restart to apply" note; the ABM
resolves language from `Aridecan.ini` at boot (B1–B3 of [TB — Boot Loader](TB-boot-loader.md))
regardless, so the fallback is free.

---

## Display & Scalability

`UMGAGameUserSettings` inherits all resolution/window/vsync handling. Quality maps onto UE's
**12 Scalability groups** — no custom quality system:

| CB Display setting | Scalability group / mechanism |
|---|---|
| Quality Preset (Low/Med/High/Ultra) | `SetOverallScalabilityLevel(0..3)` → `sg.*` groups; "Custom" = any per-group override → `GetOverallScalabilityLevel()` returns -1 |
| Shadow Quality / Distance | `SetShadowQuality` (`sg.ShadowQuality`) |
| Global Illumination (Off/Baked/Lumen) | `SetGlobalIlluminationQuality` (`sg.GlobalIlluminationQuality`) |
| Reflections | `SetReflectionQuality` (`sg.ReflectionQuality`) |
| Texture Quality | `SetTextureQuality` (`sg.TextureQuality`) |
| Effects Quality | `SetVisualEffectQuality` (`sg.EffectsQuality`) |
| Anti-Aliasing | `SetAntiAliasingQuality` (`sg.AntiAliasingQuality`) |
| View Distance | `SetViewDistanceQuality` (`sg.ViewDistanceQuality`) |
| Ambient Occlusion / Shading | `sg.ShadingQuality` / related CVars |

**Preset naming:** UE levels are Low/Med/High/**Epic**/**Cinematic** (0–4); the CB uses
Low/Med/High/**Ultra**. Map Ultra → Epic (3); reserve Cinematic (4) if we ever expose it.
Scalability persists to `[ScalabilityGroups]` in `GameUserSettings.ini` via
`Scalability::SaveState()` — automatic through `ApplyNonResolutionSettings()`.

**Upscaling (DLSS / FSR / XeSS):** integrated as UE plugins; availability **detected at
runtime** and unavailable options greyed out. Mutually exclusive — one active. Frame
Generation shown only for DLSS on supported GPUs.

**Windowed free-resize:** the CB flags this as needing OS-level window-message handling
(WM_SIZING) that UE doesn't provide, under a **no-partial-cross-platform** rule. Honour that
rule: **defer** aspect-locked free-resize; Windowed mode uses the standard resolution
dropdown until it can be done on Windows, Linux, and Mac alike.

---

## Audio

- **Submix split (reuse, build now):** two `USoundSubmix` trees — **Game Audio**
  (SFX/voice/ambient/UI) and **Music**. The per-channel volume sliders (Master/Music/SFX/
  Voice/Ambient/UI) drive submix send levels. Needed regardless of any device routing.
- **Dual-device Music output (UC3) — DEFERRED (D-audio).** Routing Music to a *separate OS
  output device* is **not native** in UE 5.8: mixer device-swap is global; one `FMixerDevice`
  targets one hardware device. The only clean path is a **custom Windows-first
  `IAudioEndpointFactory`** (a `UEndpointSubmix` for Music opening its own WASAPI session),
  with real sample-rate/latency/drift caveats. Deferred behind the no-partial-cross-platform
  rule. **Note:** the soundtrack is Ovani Sound (stream/monetization-safe), so UC3's licensing
  premise is largely moot — confirm Ovani's terms and the feature may stay optional
  indefinitely. The "Music Output device" dropdown is hidden until/unless the endpoint plugin
  ships on all three platforms.

---

## Controls (Rebinding)

Backend is entirely **Enhanced Input user settings** — reuse; we build only the UI and the
policy on top.

- **Backend:** `UEnhancedInputUserSettings` (a `USaveGame`, per-LocalPlayer; enable via
  `bEnableUserSettings=true` in Enhanced Input developer settings). Persists to its own save
  slot; `AsyncSaveSettings()` after edits.
- **Rebind:** `MapPlayerKey(FMapPlayerKeyArgs{MappingName, NewKey, Slot}, FailureReason)`;
  reset via `ResetAllPlayerKeysInRow` / profile `ResetToDefault()`.
- **Conflict detection:** built-in **query** `GetMappingNamesForKey(FKey, OutNames)` returns
  every action already using a key. The CB's "warn + offer swap/cancel" is **our UI** on top
  of that query — the engine does not auto-prevent conflicts.
- **Controller modifier layers (None / LT / LB / LT+LB):** **not** a native slot concept
  (`EPlayerMappableKeySlot` is multi-key-per-action, not modifier layers). Implement the four
  layers as **four Input Mapping Contexts** swapped on trigger-hold; the rebind UI edits one
  layer at a time; conflict detection runs across all layers.
- **Per-HUD-profile rebinds (D2 resolved):** map onto Enhanced Input's **multiple key
  profiles** (`CreateNewKeyProfile` / `SetActiveKeyProfile`). Natively supported — structure
  allows it; wire the UI later.
- Mouse/stick sensitivity, invert, dead zones, hold-threshold: `UMGAGameUserSettings` fields
  (global), applied live.

---

## Content (Per-Playthrough)

- Persists to the active playthrough's **`content_config`** (Save System), never global.
- **Age acknowledgement** required on first access before any non-Base toggle; once
  acknowledged, categories (Cursed Uniform, Inhibition/Embarrassment, Explicit DLC, Combat
  Intensity) toggle freely.
- **Streamer Mode** applies the Base-tier preset to the active playthrough, overriding
  individual toggles; Streaming Platform dropdown appears when on.
- Explicit-DLC toggles grey out unless the relevant pak is mounted (ABM tells us what's
  installed).

---

## First-Launch Wizard

Reuses the boot map, not a separate system. **First launch = absence of `Aridecan.ini`.**
When the ABM finds no config (B1), it writes OS-locale defaults; the boot map then shows the
wizard *before* the main menu (between B6 warnings and B8 menu in
[TB — Boot Loader](TB-boot-loader.md)):

1. **Language** — game + subtitle language → `Aridecan.ini`
2. **Display** — resolution + window mode only → `GameUserSettings.ini`
3. **Content** — adult categories + age acknowledgement; the streamer/creator question
   (from the Save System first-boot flow) is folded in here, not a separate dialog → global
   default `content_config`

**Skip** at any point → main menu with defaults. Never repeats (config now exists). All
wizard settings are editable later in the full menu.

---

## Menu UI

- **CommonUI activatable stack** (shared with the main menu): a category list (Display /
  Audio / Controls / Camera / Gameplay / Content / Accessibility / Language) pushing a panel
  widget per category; back-button and gamepad focus handled by the stack.
- **Two entry points, one widget:** Title Menu (front-end/boot map) and in-game Phone /
  Lost-Phone menu. Must not assume a gameplay world. **Context-gated categories:** the
  **Language** category is shown **only at the Title Menu** (hidden in-game — see Apply
  Model); the **Content** category resolves to global-default (title) vs active-playthrough
  (in-game).
- **Input-device auto-detect:** swaps prompts (A / Enter) on last-used device; full
  controller + KB/M from launch (CB requirement) — reuse CommonUI input routing.
- Expand/collapse panels (Display → Advanced, Difficulty → Custom) are widget visibility
  toggles, not separate screens.

---

## File Locations (D3 — industry standard)

Follow the standard UE-on-Windows convention; don't fight the engine:

| Data | Location |
|---|---|
| Player saves (backup-precious) | `Documents/My Games/MGA/Saves/` |
| Custom `Aridecan.ini` (language/region/tiers/timeouts) | `Documents/My Games/MGA/Config/` |
| `GameUserSettings.ini`, Enhanced Input save | UE default per-user Saved dir (co-located under `Documents/My Games/MGA/` if the user dir is redirected) |

This updates [Save System](../gdd/save-system.md)'s `Documents/MGA/` note to the "My Games"
standard. Saves are the backup-critical set; settings are cheap to reconfigure.

---

## Resolved Decisions

- **D1 — Language changed at Title Menu only; hot-swap primary, restart fallback.**
  Main-menu-only means a swap refreshes menu text only (no in-scene voice to interrupt).
  Live `LocText` remount + string-table reload; degrade to apply-on-next-launch if fragile.
- **D2 — Per-HUD control profiles via Enhanced Input multi-profile.** Native; structure now,
  wire later.
- **D3 — Industry-standard file locations** (`Documents/My Games/MGA/` for saves + custom
  config; engine defaults otherwise).
- **D-audio — Dual-device Music routing deferred.** Submix split built now; separate-device
  dropdown behind the no-partial-cross-platform rule (Ovani likely moots it).
- **Reuse map:** graphics → `UGameUserSettings` subclass + Scalability + native
  confirm/revert; input → `UEnhancedInputUserSettings`; audio → `USoundSubmix`; menu →
  CommonUI (shared with boot map).

## Open Items

- **User-dir redirection mechanism:** confirm how to point the packaged build's Saved dir at
  `Documents/My Games/MGA/` (custom `ISaveGameSystem` / user-dir override) vs. leaving engine
  config in the default location — implementation detail for the build config.
- **Difficulty scope:** confirmed global (under Gameplay, not Content) — flag if any future
  playthrough-specific difficulty lock is wanted.
- Credits location (main menu vs settings vs omitted) — inherited from CB-main-menu.
- Music output device routing plugin — see D-audio; only if the premise un-moots.

## Related

- [CB — Settings Menu](../creative-briefs/CB-settings-menu.md) — the brief this implements
- [TB — Boot Loader](TB-boot-loader.md) — config split, `Aridecan.ini`, CommonUI stack (R6), wizard hook
- [CB — Main Menu](../creative-briefs/CB-main-menu.md) — title menu, New Game content inheritance
- [CB — Controls](../creative-briefs/CB-controls.md) — binding tables
- [Difficulty Framework](../gdd/difficulty.md) — combat-only difficulty parameters
- [Save System](../gdd/save-system.md) — playthrough folders, `content_config`, file locations
- [Content & DLC](../gdd/content-and-dlc.md) — tiers, localization independence
