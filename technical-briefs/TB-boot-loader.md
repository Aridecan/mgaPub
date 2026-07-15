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
| **What happens here** | Read config, resolve language, **mount paks** (`MountPaksEx`, computed order) | Drive the **GameFeatures state machine** (Registered→Loaded→Active), age gate, warnings, main menu |
| **Implementation** | Custom module @ `ELoadingPhase::PostConfigInit` | Custom `UGameInstance` + boot map + boot `GameMode` |

**Consequence for the screen count:** the pre-engine half is *one* persistent Slate screen
whose content updates as the state advances (logo → "loading" → progress). The distinct,
authored, interactive screens all live in Regime 2 on the boot map.

**Why the split is unavoidable:** pak *mounting* (making files readable) can happen very
early, but **activating a content tier** — transitioning a GameFeature plugin through
`Registered → Loaded → Active` (`UGameFeaturesSubsystem`), which loads its `GameFeatureData`
and runs its `GameFeatureActions` — needs the UObject system and the engine fully up. So
mounting is Regime 1; the GameFeature lifecycle is Regime 2. (See
[Package Identity & Lifecycle](#package-identity--lifecycle-game-features) — the 2026-07-09
pivot from hand-rolled load vectors to GameFeature plugins.)

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
| **B6 · Warnings / legal** | UMG: content warnings, legal splash, **+ streamer/recording advisory** (localized) | — | Advance on input/timer |
| **B7 · GameFeature activation** | UMG: progress ("Initializing…") | Boot `GameMode` drives `UGameFeaturesSubsystem` | For each enabled tier plugin in dependency order: `LoadAndActivateGameFeaturePlugin()` → the engine transitions it `Registered → Loaded → Active` (loads `GameFeatureData`, appends asset registry, opens shader lib, runs `GameFeatureActions`). Deps enforced by the plugin descriptors. |
| **B8 · Main menu** | UMG: main menu | On all-Ready | Reveal the main-menu widget (see map decision below). ABM is complete. |

**B6 streamer/recording advisory (2026-07-15).** As part of the B6 warnings, show a brief
localized notice — *"If you plan on streaming or recording this game, review the **Sensitive
Content** tab in Settings to disable bannable content."* — pointing at the **Sensitive Content**
settings tab (see [TB — Settings Menu](TB-settings-menu.md) / [TB — Language Settings](TB-language-settings.md)).
Folds into B6 (no new boot state); advance on input/timer like the other warnings. **Open sub-item:**
show every launch vs. first-launch-only with a "don't show again" — TBD.

The **MBM (Mod Boot Manager)** does *not* run here — it runs at session lifecycle (new
game / load), where mod GUID-dependency resolution and the missing-dependency dialog apply.

---

## Package Identity & Lifecycle (Game Features)

**2026-07-09 pivot — content tiers are GameFeature plugins, not hand-rolled content paks.**
This reuses UE's own modular-content machinery instead of reimplementing it, and it
**dissolves the previously-designed sidecar JSON manifest** (dropped): a GameFeature plugin
already carries everything the manifest did.

### What GameFeatures give us for free

| Previously hand-rolled | GameFeatures native equivalent |
|---|---|
| Load → Init → Config → Ready lifecycle | `Installed → Registered → Loaded → Active` state machine (`UGameFeaturesSubsystem`) |
| Sidecar JSON manifest (identity + dependencies) | the **`.uplugin`** — identity + `Plugins`/`PluginDependencies` list (itself pre-mount-readable JSON) |
| Blueprint load-vector | **`GameFeatureActions`** (the plugin's own activation logic) |
| Manual asset-registry append, shader-lib open | **automatic** per plugin, on state transition |
| Extension save-GUID plumbing | the plugin identity doubles as the [Save System](../gdd/save-system.md) extension key |

Each content tier (`Spicy`, `SuperSpicy`, region-specific feature packs) is a GameFeature
plugin with `"ExplicitlyLoaded": true` and `"BuiltInInitialFeatureState": "Installed"`.

### ABM ⇄ GameFeatures: who mounts, who activates

The ABM keeps **mount-order control** — GameFeature auto-mounting would otherwise mount in
filesystem-scan order, not our computed order. So:

1. Keep the tier plugins at **`Installed`** (not auto-activated) → engine does **not**
   auto-mount them.
2. **Regime 1 (B3):** the ABM mounts every enabled pak itself via
   `FPakPlatformFile::MountPaksEx(Order = <computed>)` in known-topology order.
3. **Regime 2 (B7):** the ABM calls `LoadAndActivateGameFeaturePlugin()` per tier in
   dependency order; the subsystem sees the pak already mounted and runs the
   Registered→Loaded→Active transitions (asset registry, shaders, actions).

**`bUseIoStore = false`** (legacy `.pak`, the 5.8 default for our Win/Linux/Mac targets) — so
the ABM retains `FPakPlatformFile` mount control. IoStore's `.utoc/.ucas` would move mounting
under the engine and remove that control.

### Same-path override vs. additive (the design consequence)

GameFeature content lives at `/<TierName>/…`, **not** `/Game/…`, so it **cannot shadow** a
base asset at the same path. Tiers are therefore **additive**: the base game exposes
data-driven hooks (e.g. the clothing-destruction system stub, censored placeholders) and the
tier plugin *adds* the mature variants + activates the system via `GameFeatureActions`. The
rare case that needs true **same-path replacement** (a region swapping a specific base asset)
uses **DLC release-version cooking** (`-BasedOnReleaseVersion`, overrides by mount priority) —
see [TB — CI Cook](TB-ci-cook.md). This corrects [TB — Content Directory](TB-content-directory.md)'s
old `_Spicy/ → MGA/` path-remap, which was never a real engine mechanism.

### Localization & mods

- **Localization** (`LocText-*`, `LocVoc-*`) stays as independent paks — culture-cooked
  (`-cookcultures`) or lightweight content plugins; the ABM mounts them in Regime 1. Text and
  voice remain independent.
- **Mods** are also plugins (`.uplugin` = their manifest), delivered post-ship via `file://`
  plugin URLs. The **MBM** scans mod dirs, parses each `.uplugin` for dependencies, computes a
  topological order, `MountPaksEx()` in that order, then `LoadGameFeaturePlugin(file://…)`.
  The mod *catalog* schema in [CB — Mod Manager](../creative-briefs/CB-mod-manager.md) is a
  separate, server-side thing (download/browse) — unchanged.

---

## Diagnostics & Failure Handling

### Logging (reuse UE verbosity)

The ABM uses UE's own logging — a dedicated category, not a custom framework:

```cpp
DECLARE_LOG_CATEGORY_EXTERN(LogAridGBoot, Log, All);
```

Verbosity is controllable without a rebuild — `DefaultEngine.ini [Core.Log] LogAridGBoot=Verbose`,
or `-LogCmds="LogAridGBoot Verbose"` on the command line. Message-level convention:

| Verbosity | Used for |
|-----------|----------|
| `Error` | Phase timeout, a missing **critical** pak (`Main` / resolved `LocText`), `.uplugin` parse or GameFeature mount/activation failure → precedes exit |
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
| Mount (B3) | 30 s | mounting all owned/enabled paks (`MountPaksEx`) |
| Register (B7) | 10 s | GameFeature → `Registered` (descriptor + asset-registry append) |
| Load (B7) | 15 s | GameFeature → `Loaded` (`GameFeatureData` + content, shader lib) |
| Activate (B7) | 10 s | GameFeature → `Active` (`GameFeatureActions` run) |

**On timeout → Error + log + exit.** When a phase's budget expires, the ABM logs at `Error`
**which GameFeature plugins are stuck** (and in which target state), so the failure is
actionable, then exits gracefully via `FPlatformMisc::RequestExitWithStatus(false, <code>)`
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
| Log verbosity (`LogAridGBoot`) | `DefaultEngine.ini [Core.Log]` (engine) | Engine log system |
| Graphics quality / audio volumes | `GameUserSettings.ini` (extend `UGameUserSettings`) | Engine + settings menu |

First launch (no `GameUserSettings.ini`): the engine falls back to defaults for
resolution; the ABM writes a default `Aridecan.ini` after detecting OS locale (B1–B2).

**Writable-data location (policy):** all runtime-written user data — `Aridecan.ini`, save games,
logs — goes to the **OS per-user app-data dir** (`FPlatformProcess::UserSettingsDir()/<Project>/…` →
`%LOCALAPPDATA%\OGMMGA\…` on Windows), **not** the game folder. That dir is guaranteed writable
regardless of install location, so writes never hit "permission denied" (the failure mode of a
`Program Files`/locked install). Shipping builds default logs to **Fatal/off**. `GameUserSettings.ini`
stays engine-managed in the game folder for now — its default-vs-user **layering already works**
(`Config/DefaultGameUserSettings.ini` = shipped defaults, user file = overrides); relocating the user
file to the per-user dir is a deferred project-wide redirect.

---

## Implementation Shape

Three artifacts:

1. **`AridGBootModule`** — a runtime module with `"LoadingPhase": "PostConfigInit"` in
   the `.uplugin`/`.Build` descriptor. Registers the `EarlyStartupScreen`, reads
   `Aridecan.ini`, resolves language, and mounts paks (states B0–B3). Pure Slate + raw
   pak API; **no UObjects**.

2. **`UAridGGameInstance : UGameInstance`** — overrides `StartGameInstance()` to
   `Browse()` to the boot map (B4). Set in `DefaultEngine.ini`:

   ```ini
   [/Script/Engine.GameMapsSettings]
   GameInstanceClass=/Script/OGMMGA.AridGGameInstance
   GameDefaultMap=/Game/Maps/BootMap
   ```

3. **`BP_BootMap` + `AAridGBootGameMode`** — the lightweight front-end map. `BeginPlay` runs the
   UMG state machine (B5–B8): age gate, warnings, then drives `UGameFeaturesSubsystem` to
   activate the enabled tier plugins in dependency order, then the main menu.

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
- **R7 — Content tiers are GameFeature plugins (2026-07-09 pivot); sidecar manifest dropped.**
  Identity + dependencies come from the `.uplugin`; lifecycle from the
  `Registered→Loaded→Active` state machine; activation logic from `GameFeatureActions`. The
  ABM mounts paks in computed order (`MountPaksEx`, plugins kept at `Installed` so the engine
  doesn't auto-mount) then drives activation. `bUseIoStore=false` to retain mount control.
  See Package Identity & Lifecycle. (Supersedes the old sidecar-manifest resolution of O2.)
- **R8 — Per-phase timeouts, config-driven, fail-hard.** Each waiting phase has its own
  `[Boot.Timeouts]` budget; expiry → `Error`-log the stuck plugins → graceful exit
  (not a crash). See Diagnostics & Failure Handling. (Resolves O4.)
- **R9 — UE-native logging via `LogAridGBoot`.** Verbosity-controlled; the pak search
  order dumps at `Verbose`. (Resolves O5.)
- **R10 — Tiers additive, not same-path shadowing.** GameFeature content is at `/<Tier>/…`;
  base exposes data hooks the tier fills. True same-path replacement (rare, e.g. region
  swaps) uses DLC release-version cooking — see [TB — CI Cook](TB-ci-cook.md).

## Open Items

- **O6 — CI/cook interaction:** whether the boot map + module cook cleanly and whether the
  `PostConfigInit` mount ordering behaves identically in a packaged build vs. editor —
  addressed in **[TB — CI Cook](TB-ci-cook.md)**.
- **O8 — Suppress auto-mount reliably.** Confirm that `ExplicitlyLoaded` +
  `BuiltInInitialFeatureState: Installed` reliably prevents the engine from auto-mounting a
  tier plugin's pak before the ABM's `MountPaksEx` (so we never double-mount). Test on a
  packaged build.
- **O9 — GameFeature pak location at cook.** Confirm whether the cooker can route each tier
  plugin's content to its own `Plugins/<Tier>/Paks/` pak (may need a custom cook policy /
  `FCookPackageSplitter`) vs. it folding into the main pak. Feeds [TB — CI Cook](TB-ci-cook.md).

---

## Related

- [Boot Manager](../gdd/boot-manager.md) — ABM/MBM design, pak names, phased lifecycle (this TB implements it)
- [TB — CI Cook](TB-ci-cook.md) — how the tier plugins / paks are actually cooked
- [TB — Content Directory](TB-content-directory.md) — Content/ layout (tiers now = GameFeature plugins)
- [Content Architecture & DLC](../gdd/content-and-dlc.md) — content tiers, load stack
- [CB — Mod Manager](../creative-briefs/CB-mod-manager.md) — MBM / mod pak conventions
- [Save System](../gdd/save-system.md) — per-playthrough content configuration
