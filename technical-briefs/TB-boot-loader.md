# TB — Boot Loader (ABM Implementation)

## Overview

This technical brief defines how the **ABM (Aridecan Boot Manager)** — designed in
[Boot Manager](../gdd/boot-manager.md) — is actually implemented against Unreal Engine
**5.8**'s startup sequence. It maps the ABM's phased lifecycle onto concrete engine hook
points, defines the boot state machine, and specifies which screen mechanism each state
uses.

The guiding principle (Peter's framing): **each screen the player sees is an observable
state of the ABM state machine.** The boot sequence and the screen sequence are the same
thing viewed from two sides.

**Standing design rule: reuse UE's own systems wherever they already do the job.** The ABM
adds only the logic UE lacks (config-driven tier selection, known-topology mount order,
the screen state machine); anything the engine already provides — resolution/window
application, config parsing, pak mounting primitives, preload/loading screens — is used
as-is rather than reimplemented.

> **Engine-version caveat.** All class/function/delegate names and `LaunchEngineLoop.cpp`
> line numbers below are from the local UE 5.8 source at `d:/uegit` and are accurate as of
> the 2026-07-09 source dive. Line numbers drift between engine syncs — treat the
> *ordering* and *API names* as authoritative, the line numbers as navigational hints.

---

## The Core Constraint: Two Rendering Regimes

UE does not give us a series of independent full-screen "screens" we can freely author. It
gives us **two regimes**, split by whether the engine (and therefore the UObject system,
UMG, and the game's pak-based string tables) is alive yet:

| | **Regime 1 — Pre-Engine** | **Regime 2 — Post-Engine** |
|---|---|---|
| **When** | During `FEngineLoop::PreInit`, before `GEngine` exists | After `GEngine::Init()`, on the boot map |
| **Screen tech** | `EarlyStartupScreen` (PreLoadScreen system) — **Slate only, no UObjects** | Full **UMG** widgets |
| **Interactivity** | Minimal (non-interactive splash/progress) | Full (buttons, input) |
| **Localized text** | Only via the **preload localization container** (`FPreLoadSettingsContainerBase::GetLocalizedText()`), independent of game string tables | Yes — from the mounted `LocText-<lang>` pak's string tables |
| **What happens here** | Read config, resolve language, **mount paks** | Run the pak **load-vector lifecycle** (Init→Config→Ready), age gate, warnings, main menu |
| **Implementation** | Custom module @ `ELoadingPhase::PostConfigInit` | Custom `UGameInstance` + boot map + boot `GameMode` |

**Consequence for the screen count:** the pre-engine half is *one* persistent Slate screen
whose content updates as the state advances (logo → "loading" → progress). The distinct,
authored, interactive screens all live in Regime 2 on the boot map.

**Why the split is unavoidable:** pak *mounting* (making files readable) can happen very
early, but a pak's **load vector** (its blueprint entry point that registers name +
dependencies and runs Init/Config/Ready per [Boot Manager](../gdd/boot-manager.md)) is a
UObject/Blueprint — it cannot execute until the engine is up. So mounting is Regime 1;
the phased lifecycle is Regime 2.

---

## Engine Startup Facts (UE 5.8, cited)

Ordered timeline through `Engine/Source/Runtime/Launch/Private/LaunchEngineLoop.cpp`:

| # | Step | Location | Notes |
|---|------|----------|-------|
| 1 | `PreInitPreStartupScreen()` begins | `:~1728` | cmdline, output devices, logging |
| 2 | **`UGameUserSettings::PreloadResolutionSettings()`** | `:~2832` | Reads resolution + window mode from `GameUserSettings.ini` → populates `GSystemResolution`. **The engine applies our graphics config here, for free.** |
| 3 | Console variables / scalability loaded | `:~2878` | |
| 4 | `FSlateApplication::Create()` | `:~3118` | Slate layer up; PreLoadScreen can now draw |
| 5 | **Config system ready** | `FConfigCacheIni::InitializeConfigSystem()` | Fires `FCoreDelegates::TSConfigReadyForUse()`. Modules @ `PostConfigInit` run around here. |
| 6 | `PreInitPostStartupScreen()` | `:~3447` | splash / early movie |
| 7 | **Default pak mount** | `:~3630` | `FCoreDelegates::OnMountAllPakFiles.Execute(PakFolders)` — the engine's own mount of `InstalledContent/.../Paks` |
| 8 | `ProcessNewlyLoadedUObjects` | `:~3721` | CDOs created |
| 9 | `GEngine` created | `:~4779` | `GameEngine` class from `[/Script/Engine.Engine]` |
| 10 | `GEngine->Init()` | `:~4829` | Engine subsystems (`UEngineSubsystem::Initialize`) init here |
| 11 | `FCoreDelegates::OnPostEngineInit` | `:~4835` | GameInstance exists; **no map loaded yet** |
| 12 | `UGameEngine::CreateGameWindow()` | `GameEngine.cpp:~1304` → `:464` | Reads `GSystemResolution` → creates the `SWindow` at the right size/mode, **no resize flash** |
| 13 | `GEngine->Start()` → `StartGameInstance()` | `:~4879` → `GameEngine.cpp:1335` | Reads `GameDefaultMap` (`GameInstance.cpp:671`) and `Browse()`s to it (`:687`) |
| 14 | `OnFEngineLoopInitComplete` | `:~4951` | Engine fully ready |

**Key API surface:**

- **Pak mounting** — `FPakPlatformFile::Mount(const TCHAR* PakFilename, uint32 PakOrder, ...)`
  (`IPlatformFilePak.cpp:5998`). Higher `PakOrder` = searched first (shadows lower). Batch:
  `MountAllPakFiles(folders, wildcard)`. Delegates: `OnMountAllPakFiles`, `MountPak`,
  `MountPaksEx`, and the post-mount multicast `GetOnPakFileMounted2()`
  (`CoreDelegates.h:118-152`). Patch paks (`_P_*.pak`) auto-bump `PakOrder += 100 * chunkVersion`.
- **Config** — after `TSConfigReadyForUse()`, read our file via
  `GConfig->GetString(Section, Key, Out, TEXT("Aridecan.ini"))`. Pre-config, use raw
  `IPlatformFile` if ever needed (not needed in current design).
- **Resolution/window** — owned by `UGameUserSettings` + `GSystemResolution`. We do not
  touch it; we only ensure `GameUserSettings.ini` exists (first-launch default written by us).
- **Boot routing** — custom `UGameInstance::StartGameInstance()` override; set
  `GameInstanceClass=` and `GameDefaultMap=/Game/Maps/BootMap` in `DefaultEngine.ini`.
- **Pre-engine screen** — module @ `PostConfigInit` calls
  `FPreLoadScreenManager::Get()->RegisterPreLoadScreen(...)` with an
  `EPreLoadScreenTypes::EarlyStartupScreen`.

---

## The Boot State Machine

Each row is an observable state. "Screen" = what the player sees during that state.

### Regime 1 — Pre-Engine (module @ `PostConfigInit` + one `EarlyStartupScreen`)

| State | Screen shown | Engine hook | Action |
|-------|-------------|-------------|--------|
| **B0 · Splash** | Studio/engine logo (**text-free**, language-neutral) | Slate up (`:3118`); EarlyStartupScreen registered | Present; engine has already applied resolution/window from `GameUserSettings.ini` |
| **B1 · Config** | (same screen, now "Loading…") | `TSConfigReadyForUse()` | Read `Aridecan.ini`: **language, region, enabled content tiers, installed languages** |
| **B2 · Language resolve** | (same screen) | — | Fallback chain: config lang → host OS locale → `en` → first available `LocText-*` |
| **B3 · Mount** | (same screen + progress) | Direct `FPakPlatformFile::Mount()` calls | Mount official paks by **known-topology priority**, honoring config: skip unowned/disabled tiers; auto-exclude anything whose dependency is absent. Mount `Main` + `LocText-<lang>` at minimum. |
| — | | Engine continues to `GEngine::Init()` → boot map | Hand-off |

**On official-content priority (why B3 needs no dependency graph read):** the ABM knows the
full official topology (finite, per [Boot Manager](../gdd/boot-manager.md)), so `PakOrder`
is assigned from a **known scheme**, not derived from reading each pak. Illustrative order
(higher = searched first):

```
regionOverride-<region>   ← highest
LocVocSuperSpicy / LocTextSuperSpicy / SuperSpicy
LocVocSpicy / LocTextSpicy / Spicy
LocVoc-<lang> / LocText-<lang>
Main                       ← lowest
```

Dependency-graph *derivation* is a **mod** concern (MBM), not the ABM — see the MBM note below.

### Regime 2 — Post-Engine (custom `UGameInstance` → boot map → boot `GameMode`, UMG)

| State | Screen shown | Engine hook | Action |
|-------|-------------|-------------|--------|
| **B4 · Route to boot map** | (engine loading screen) | `StartGameInstance()` override | `Browse()` to `/Game/Maps/BootMap` instead of the menu map |
| **B5 · Age gate** | UMG: "This game is intended for a mature audience…" (localized) | Boot `GameMode::BeginPlay` | Interactive Yes/No. `LocText-<lang>` is mounted, so text is localized. Fail → quit. |
| **B6 · Warnings / legal** | UMG: content warnings, legal splash (localized) | — | Advance on input/timer |
| **B7 · Package lifecycle** | UMG: progress ("Initializing…") | Boot `GameMode` drives it | Run the ABM 4-phase lifecycle over mounted paks: **Load** (run each pak's load-vector BP → register name + deps) → **Init** → **Config** (dependency-ordered, self-coordinated via signals) → **Ready** |
| **B8 · Main menu** | UMG: main menu | On all-Ready | Reveal the main-menu widget (see map decision below). ABM is complete. |

The **MBM (Mod Boot Manager)** does *not* run here — it runs at session lifecycle (new
game / load), where mod GUID-dependency resolution and the missing-dependency dialog apply.

---

## Pak Runtime Manifest

Every pak — official *and* mod — ships with a **sidecar JSON manifest** (`<pak>.manifest.json`,
a loose file beside the `.pak`). This is the pak's load-time identity + dependency
declaration, read by the ABM (official) or MBM (mod). It **replaces the mandatory blueprint
load-vector** of the original design (resolves Boot Manager open item O2).

**Do not confuse this with the mod *catalog* schema** in [CB — Mod Manager](../creative-briefs/CB-mod-manager.md).
That schema is **server-side** — it describes a mod for *discovery/download* (URLs,
thumbnail, `compatible_versions`, description). The runtime manifest below travels *with*
the installed pak and drives *loading*. They share identity vocabulary and must stay
consistent, but the catalog carries download/browser fields the runtime manifest omits, and
the runtime manifest carries load fields (`tier`, `language`, `region`, `load_vector`) the
catalog omits.

### Schema

```json
{
  "schema_version": 1,
  "kind": "official",              // "official" (ABM, name-id) | "mod" (MBM, guid-id)
  "id": "Spicy",                   // predefined name (official) OR UUID v4 (mod)
  "version": "1.0.0",              // semver — feeds save/build compatibility
  "tier": "Spicy",                 // Base|Spicy|SuperSpicy|LocText|LocVoc|RegionOverride|Mod
  "language": null,                // "ja" for LocText-ja / LocVoc-ja, else null
  "region": null,                  // "japan" for regionOverride-japan, else null
  "dependencies": ["Main"],        // DIRECT only, one level; names (official) or GUIDs (mod)
  "incompatible": [],              // ids known to conflict (mirrors catalog incompatible_mods)
  "compatible_versions": ["*"],    // game-build compat (mods); official ships-with-build
  "load_vector": null              // OPTIONAL BP path; null = passive content-only pak
}
```

### Rules

- **M1 — Sidecar, both kinds.** Uniform `<pak>.manifest.json` beside every pak. Rationale:
  readable **before mount**, so the MBM can resolve mod dependency order and raise the
  missing-dependency dialog without mounting anything; one code path for official + mod. The
  mod download bundle zips `pak + manifest` together.
- **M2 — Optional load-vector.** The JSON does registration (id + dependencies). A pak points
  to a `load_vector` blueprint **only if it has runtime init work** (e.g. Spicy's
  clothing-destruction system). Content-only paks — all localization, all region overrides,
  most asset packs — set `load_vector: null` and layer in as passive content. Far less BP
  plumbing than the original mandatory-load-vector design.
- **M3 — One identity.** A mod's `id` (GUID) is simultaneously its dependency-graph node,
  its mount identity, **and** its [Save System](../gdd/save-system.md) extension GUID for
  namespaced persistence. A pak never carries two IDs.
- **M4 — Parse path (reuse UE):** Regime 1 (pre-engine) parses with plain
  `FJsonSerializer` → `FJsonObject` (no UObject reflection required). Regime 2 may use
  `FJsonObjectConverter` → a `USTRUCT` (`FAridecanPakManifest`) once reflection is up.
- **M5 — Official mount order is still known-topology (R4).** Official manifests are read to
  *drive the B7 lifecycle and validate*, not to compute mount priority. Only the MBM derives
  order from declared dependencies (mods).

---

## Diagnostics & Failure Handling

### Logging (reuse UE verbosity)

The ABM uses UE's own logging — a dedicated category, not a custom framework:

```cpp
DECLARE_LOG_CATEGORY_EXTERN(LogAridecanBoot, Log, All);
```

Verbosity is controllable without a rebuild — `DefaultEngine.ini [Core.Log] LogAridecanBoot=Verbose`,
or `-LogCmds="LogAridecanBoot Verbose"` on the command line. Message-level convention:

| Verbosity | Used for |
|-----------|----------|
| `Error` | Phase timeout, a missing **critical** pak (`Main` / resolved `LocText`), manifest parse failure → precedes exit |
| `Warning` | Recoverable oddities — an excluded optional tier, a skipped unowned pak, a missing *optional* dependency auto-excluded per known topology |
| `Display` / `Log` | Normal milestones — each state transition (B0→B8), phase entry/exit, mount summary counts |
| `Verbose` | **Debug level: the resolved pak search order dump**, per-package registration (id + declared deps), dependency-resolution steps |
| `VeryVerbose` | Per-file mount detail, per-signal (`Inited`/`Configed`) traffic |

**O5 resolved:** the search-order output ships at `Verbose`. It's always in the code, off by
default, and switched on via the log config when debugging load order — no separate
"debug build."

### Phase Timeouts (per-phase, config-driven)

Every waiting phase has its **own** timeout — a stuck mount and a stuck `Configed` reply are
different failure profiles and deserve different budgets. Timeouts live in `Aridecan.ini`
(`[Boot.Timeouts]`, seconds) so they're tunable without a rebuild. Provisional defaults:

| Phase / state | Default | What it bounds |
|---------------|---------|----------------|
| Config read (B1) | 5 s | reading + parsing `Aridecan.ini` |
| Mount (B3) | 30 s | mounting all owned/enabled paks |
| Load (B7) | 15 s | running each pak's `load_vector` BP → registration |
| Init (B7) | 10 s | waiting for all `Inited` replies |
| Config (B7) | 10 s | waiting for all `Configed` replies |
| Ready (B7) | 5 s | final `Ready` broadcast settle |

**On timeout → Error + log + exit.** When a phase's budget expires, the ABM logs at `Error`
**which packages are still pending** (e.g. the paks that never sent `Configed`), so the
failure is actionable, then exits gracefully via `FPlatformMisc::RequestExitWithStatus(false, <code>)`
— *not* a `Fatal`/crash. If a resolved `LocText` pak is already mounted, the exit is preceded
by a localized fatal-error dialog; pre-localization failures exit to log only.

**O4 resolved:** per-phase timeouts, config-driven, defaults above (TBD-tunable); timeout →
Error-log the pending packages → graceful exit.

---

## Configuration Split

| Setting | Lives in | Applied by |
|---------|----------|-----------|
| Resolution, window mode, monitor | `GameUserSettings.ini` (`UGameUserSettings`) | **Engine**, at `:2832` — no ABM code |
| Text language, voice language, region | `Aridecan.ini` (custom) | ABM @ `PostConfigInit` (B1–B2) |
| Enabled content tiers, installed languages | `Aridecan.ini` (custom) | ABM @ B3 (which paks to mount) |
| Per-phase boot timeouts (`[Boot.Timeouts]`) | `Aridecan.ini` (custom) | ABM (Diagnostics § defaults) |
| Log verbosity (`LogAridecanBoot`) | `DefaultEngine.ini [Core.Log]` (engine) | Engine log system |
| Graphics quality / audio volumes | `GameUserSettings.ini` (extend `UGameUserSettings`) | Engine + settings menu |

First launch (no `GameUserSettings.ini`): the engine falls back to defaults for
resolution; the ABM writes a default `Aridecan.ini` after detecting OS locale (B1–B2).

---

## Implementation Shape

Three artifacts:

1. **`AridecanBootModule`** — a runtime module with `"LoadingPhase": "PostConfigInit"` in
   the `.uplugin`/`.Build` descriptor. Registers the `EarlyStartupScreen`, reads
   `Aridecan.ini`, resolves language, and mounts paks (states B0–B3). Pure Slate + raw
   pak API; **no UObjects**.

2. **`UAridecanGameInstance : UGameInstance`** — overrides `StartGameInstance()` to
   `Browse()` to the boot map (B4). Set in `DefaultEngine.ini`:

   ```ini
   [/Script/Engine.GameMapsSettings]
   GameInstanceClass=/Script/AridecanBoot.AridecanGameInstance
   GameDefaultMap=/Game/Maps/BootMap
   ```

3. **`BP_BootMap` + `ABootGameMode`** — the lightweight front-end map. `BeginPlay` runs the
   UMG state machine (B5–B8): age gate, warnings, the package Load/Init/Config/Ready
   lifecycle, then the main menu.

---

## Resolved Decisions

- **R1 — Graphics config source:** reuse `UGameUserSettings` / `GameUserSettings.ini`.
  The engine already applies it at the correct pre-window moment; duplicating it in a
  custom file would fight the engine's own `PreloadResolutionSettings` timing. *(Resolves
  the open question from the boot-loader design discussion.)*
- **R2 — Pre-engine screen is one updating Slate screen,** not N screens. Distinct
  interactive screens live in Regime 2.
- **R3 — Age gate is a UMG screen on the boot map (B5),** not a pre-engine preload screen —
  it needs interactive Yes/No, which the preload system handles poorly.
- **R4 — Official pak order is assigned from known topology,** not derived from reading
  paks. Graph derivation is reserved for the MBM (mods).
- **R5 — B0 splash is text-free.** No preload localization container; a language-neutral
  logo/mark. (Resolves O3.) Keeps the first frame free of any string dependency.
- **R6 — Main menu is a widget on the boot map,** not a separate map. Submenu navigation
  uses **CommonUI's activatable widget stack** (`UCommonActivatableWidget` +
  `UCommonActivatableWidgetStack`) — push/pop submenus, back-button, gamepad focus for free
  (reuse-UE). Entering gameplay is one clean `OpenLevel` teardown of the front-end map.
  (Resolves O1.)
- **R7 — Pak runtime manifest = sidecar JSON, both kinds.** See the Pak Runtime Manifest
  section (rules M1–M5). Replaces the mandatory blueprint load-vector; the BP is now an
  optional per-pak field. (Resolves O2.)
- **R8 — Per-phase timeouts, config-driven, fail-hard.** Each waiting phase has its own
  `[Boot.Timeouts]` budget; expiry → `Error`-log the pending packages → graceful exit
  (not a crash). See Diagnostics & Failure Handling. (Resolves O4.)
- **R9 — UE-native logging via `LogAridecanBoot`.** Verbosity-controlled; the pak search
  order dumps at `Verbose`. (Resolves O5.)

## Open Items

- **O4 — Config-phase timeout (B7):** what happens if a pak never signals `Configed`
  (infinite wait vs. timeout + error) — inherited from Boot Manager open items.
- **O5 — Does the ABM search order get logged to file** for debugging? (Boot Manager open
  item; cheap to add here.)
- **O6 — CI/cook interaction:** whether the boot map + module cook cleanly and whether the
  `PostConfigInit` mount ordering behaves identically in a packaged build vs. editor —
  feeds the planned **TB-ci-cook**.
- **O7 — Load-vector interface:** the `load_vector` field is a BP path (M2), but the
  interface/base-class that BP implements to receive the Load/Init/Config/Ready calls and
  emit `Inited`/`Configed` signals is unspecified. Spec this when building B7 (only paks
  with runtime init need it).

---

## Related

- [Boot Manager](../gdd/boot-manager.md) — ABM/MBM design, pak names, phased lifecycle (this TB implements it)
- [TB — Content Directory](TB-content-directory.md) — pak ↔ directory mapping, DLC same-path override
- [Content Architecture & DLC](../gdd/content-and-dlc.md) — content tiers, load stack
- [CB — Mod Manager](../creative-briefs/CB-mod-manager.md) — MBM / mod pak conventions
- [Save System](../gdd/save-system.md) — per-playthrough content configuration
