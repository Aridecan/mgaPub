# TB — Boot Loader: Implementation Plan, Diagrams & UE6 Migration

Companion to **[TB — Boot Loader](TB-boot-loader.md)** (the *architecture*). That brief says
**what** the ABM is and **why**; this one is the **build plan**: the concrete file/class
skeleton, the framework-first-with-stubs order, editor-load safety, Jenkins packaging
integration, the bring-up→test→prune logging plan, and the **UE6/Verse migration strategy**.

**Directive this encodes (Peter, 2026-07-12):**
1. **Document first, then implement.**
2. **Framework first with stubbed pak detection** — build the real ABM skeleton, but make pak
   detection/mount a **stub** returning known-good defaults, so **the editor keeps loading
   correctly** and we aren't blocked on the multi-pak cook.
3. **Then get the Jenkins scripts packaging** the framework.
4. **Then fill in the stubs** (real pak detection/mount) once the cook emits separate paks.
5. **Testing:** log heavily during bring-up ("log every line"), write up a **test-suite
   execution**, then **prune** the transient logs down to the permanent debug surface.

> **Reuse-UE:** every screen/mount/config mechanism is an existing engine system (PreLoadScreen,
> `FPakPlatformFile`, `UGameFeaturesSubsystem`, CommonUI, `UGameUserSettings`). The ABM adds only
> the glue UE lacks. See TB-boot-loader R1–R10.

---

## 1. Consolidated Flow Diagram

```
                         ENGINE STARTUP (LaunchEngineLoop.cpp)
  ┌──────────────────────────── REGIME 1 — PRE-ENGINE ────────────────────────────┐
  │  AridGBoot module @ ELoadingPhase::PostConfigInit                            │
  │  ONE persistent Slate EarlyStartupScreen (no UObjects); GATED to !GIsEditor     │
  │                                                                                 │
  │   B0 Splash ──▶ B1 Config read ──▶ B2 Language resolve ──▶ B3 Mount paks        │
  │   (logo,        (Aridecan.ini:      (cfg → OS locale →      ┌───────────────┐   │
  │    text-free)    lang/region/        en → 1st LocText)      │  Phase A: STUB │   │
  │                  tiers/langs)                               │  logs intended │   │
  │                                                             │  order; mounts │   │
  │  engine hooks: Slate up (:3118) · TSConfigReadyForUse ·     │  nothing extra │   │
  │                (default pak mount :3630)                    │  Phase C: real │   │
  │                                                             │  MountPaksEx() │   │
  │                                                             └───────────────┘   │
  └───────────────────────────────────┬─────────────────────────────────────────┘
              GEngine::Init() ─▶ StartGameInstance() ─▶ Browse(BootMap)
  ┌───────────────────────────────────▼──── REGIME 2 — POST-ENGINE ───────────────┐
  │  UAridGGameInstance → /Game/Maps/BootMap → AAridGBootGameMode                     │
  │  UMG + CommonUI activatable widget stack                                        │
  │                                                                                 │
  │   B4 Route ──▶ B5 Age gate ──▶ B6 Warnings ──▶ B7 GF activation ──▶ B8 Main menu│
  │   (to Boot     (UMG Yes/No,    (content/legal,  (UGameFeatures-     (WBP_MainMenu│
  │    Map)         localized;      advance)         Subsystem: tiers   on stack)   │
  │                 No→exit)                          →Active, dep order)    │       │
  │                                                                          ▼       │
  │                                              Settings (WBP_Settings) push/pop on │
  │                                              CommonUI stack · Language dropdowns │
  │                                              enumerate LocText_*/LocVoice_*      │
  │                                                                                 │
  │   New Game / Continue ─▶ OpenLevel(gameplay) = one clean teardown of front-end  │
  └─────────────────────────────────────────────────────────────────────────────┘
```

Screens = observable ABM states (TB-boot-loader's guiding principle). The dashed box (B3) is
the **only** stubbed seam in Phase A.

---

## 2. Module & Class Layout (file skeleton)

Boot infra is **isolated in one plugin** (`AridGBoot`) plus three thin classes — this is
the UE6 port surface (§7). Nothing boot-related lives loose in the game module.

```
OGMMGA/Plugins/AridGBoot/                        ← new runtime plugin (its own module)
  AridGBoot.uplugin                              ← Modules[0].LoadingPhase = "PostConfigInit"
  Source/AridGBoot/
    AridGBoot.Build.cs                           ← deps: Core, Projects, PakFile, PreLoadScreen,
                                                       Slate, SlateCore, EngineSettings; (Engine,
                                                       GameFeatures only in the Regime-2 helpers)
    Public/
      AridGBootModule.h                          ← IModuleInterface; owns Regime-1 lifecycle
      AridGBootLog.h                             ← DECLARE_LOG_CATEGORY_EXTERN(LogAridGBoot,...)
      IAridGPakMountPlan.h                               ← THE STUB SEAM (§4): compute+mount official paks
      AridGBootSettings.h                        ← Aridecan.ini read/write, defaults, timeouts
    Private/
      AridGBootModule.cpp                        ← StartupModule: register EarlyStartupScreen,
                                                       hook TSConfigReadyForUse → B1/B2/B3
      AridGPreLoadScreen.{h,cpp}                 ← FPreLoadScreenBase (EarlyStartupScreen);
                                                       Slate-only splash/progress, state text
      AridGPakMountPlan_Stub.cpp                         ← Phase A: logs intended order, mounts nothing
      AridGPakMountPlan_KnownTopology.cpp                ← Phase C: real MountPaksEx() in computed order
      AridGBootSettings.cpp

OGMMGA/Source/OGMMGA/                                ← existing game module (thin boot glue only)
  Public/AridGGameInstance.h    Private/…cpp      ← UGameInstance: StartGameInstance() → BootMap (B4)
  Public/AridGBootGameMode.h            Private/…cpp      ← AGameModeBase: B5–B8 state machine (thin;
                                                       delegates activation to a C++ helper)
  Public/AridGBootFlowComponent.h       Private/…cpp      ← (optional) UActorComponent holding the B5–B8
                                                       state enum + GameFeature-activation driver,
                                                       so AridGBootGameMode stays a shell (UE6-friendly, §7)

OGMMGA/Content/MGA/Maps/BootMap.umap                ← lightweight front-end map
OGMMGA/Content/MGA/UI/
  WBP_MainMenu   WBP_Settings   WBP_AgeGate   WBP_BootProgress  [WBP_FirstLaunchWizard]
                                                    ← CommonUI activatable widgets (thin views)
```

**Config (DefaultEngine.ini):**
```ini
[/Script/Engine.GameMapsSettings]
GameInstanceClass=/Script/OGMMGA.AridGGameInstance
GameDefaultMap=/Game/MGA/Maps/BootMap
GlobalDefaultGameMode=/Script/OGMMGA.AridGBootGameMode   ; BootMap can also set this per-map
```

---

## 3. Framework-First Build Order (the phases)

### Phase A — Skeleton that boots to a main menu, pak detection STUBBED
Goal: **editor loads unchanged; a package boots BootMap → age gate → main menu → settings.**
No dependency on the multi-pak cook.

1. Create the **`AridGBoot`** plugin/module (`PostConfigInit`), `LogAridGBoot`, and the
   `EarlyStartupScreen` — splash + a status line driven by the state enum. **Gate all of
   Regime 1 behind `!GIsEditor && FApp::IsGame()`** (§5) so editor startup is untouched.
2. `AridGBootSettings`: read `Aridecan.ini`; if absent, **write defaults** (detect OS locale).
3. **`IAridGPakMountPlan` = STUB** (`AridGPakMountPlan_Stub.cpp`): logs the *intended* known-topology
   order + "STUB: mounting nothing beyond engine default (single Main pak)". Returns success.
4. `UAridGGameInstance::StartGameInstance()` → `Browse(BootMap)` (B4), guarded for PIE (§5).
5. `BootMap` + `AAridGBootGameMode` (+ optional `AridGBootFlowComponent`): B5 age gate → B6 warnings →
   **B7 GameFeature activation** (drive `UGameFeaturesSubsystem` for `Spicy`/`SuperSpicy`; in
   Phase A tiers may stay `Active` so this is a no-op verify) → B8 reveal `WBP_MainMenu`.
6. `WBP_MainMenu` → `WBP_Settings` on the CommonUI stack; Settings **Language** category
   enumerates the installed `LocText_*`/`LocVoice_*` plugins (real data, empty packs OK).
7. Set the three `DefaultEngine.ini` keys. **This also fixes the packaging bug** where the
   build booted `/Engine/Maps/Templates/OpenWorld` (see [TB — CI Cook](TB-ci-cook.md) notes).

Exit criteria A: editor opens normally; `-game`/PIE and a **local package** both reach the
main menu; settings opens; logs show B0→B8 in order.

### Phase B — Jenkins packaging green with the framework
- The `AridGBoot` plugin + BootMap + widgets cook and package (extend `cook.ps1` only if a
  new module needs it; a code plugin builds with the game target).
- The packaged build boots to the main menu (not the engine template). Confirm the
  `PostConfigInit` module + `EarlyStartupScreen` behave identically packaged vs. editor (O6).
- Keep the *BLD-disable + (later) the loc/tier plugins in the cook set.

Exit criteria B: `mga-weekly` Full produces a package that **boots to the main menu**.

### Phase C — Fill the stubs (real pak detection/mount)
Gated on the **cook Stage 2–5 multi-pak split** (separate `Spicy`, `SuperSpicy`, `LocText_*`,
`LocVoice_*` paks) — see [TB — CI Cook](TB-ci-cook.md) O-A / dlc-build-plan.
1. Swap `AridGPakMountPlan_Stub` → `AridGPakMountPlan_KnownTopology`: compute `PakOrder` from the known
   topology (TB-boot-loader §B3) and `MountPaksEx()` the owned/enabled paks in Regime 1 (B3).
2. Flip tiers `BuiltInInitialFeatureState: Active → Installed` (TB-boot-loader R7/O8) so the
   engine doesn't auto-mount; B7 activation now does real work in computed order.
3. `Aridecan.ini` drives which tiers/langs mount; per-phase timeouts (TB-boot-loader R8).
4. Wire the first-launch wizard (optional) + the localized age-gate text from the mounted
   `LocText-<lang>` pak.

Exit criteria C: a package with tiers/loc disabled vs. enabled mounts the correct pak set in
the correct order (verified via the `Verbose` order dump), tiers reach `Active`.

---

## 4. The Stub Contract (`IAridGPakMountPlan`)

The seam is explicit so filling it later is a drop-in, not a refactor:

```cpp
// IAridGPakMountPlan.h — the ONE thing Phase A stubs and Phase C implements for real.
class IAridGPakMountPlan
{
public:
    virtual ~IAridGPakMountPlan() = default;
    // Called in Regime 1 / B3. Reads AridGBootSettings (enabled tiers, langs, region),
    // computes the known-topology PakOrder, and mounts the owned/enabled official paks.
    // Returns the ordered list actually mounted (for the Verbose dump + tests).
    virtual TArray<FMountedPak> ComputeAndMount(const FAridGBootSettings& Cfg) = 0;
};
// AridGPakMountPlan_Stub:          logs the intended order, mounts nothing, returns {Main}.
// AridGPakMountPlan_KnownTopology: real MountPaksEx() per TB-boot-loader §B3.
```

Selection is a single factory (`#if` / a cvar / a settings flag) so we can A/B the stub vs.
real plan even after C lands — useful for regression testing packaged builds.

---

## 5. Editor-Load Safety (do NOT fight the editor)

The historically painful part. Rules:
- **Regime 1 runs only in cooked game runtime.** Guard `StartupModule`'s screen-register +
  the config/mount work behind `!GIsEditor && FApp::IsGame()`. In the editor the module loads
  but does nothing → editor startup is byte-for-byte unchanged.
- **`StartGameInstance()` override guards PIE.** Only force the BootMap route for a real game
  launch; in PIE the editor picks the level, so the override must early-return for PIE (or
  BootMap must be PIE-launchable without side effects). Never break "Play" in the editor.
- **Tier/loc plugins stay editor-loadable** (already verified — Spicy/SuperSpicy reach Active,
  loc packs mount; see memory `dlc-loadables-scaffold`).
- **BootMap is inert** — no gameplay actors, no auto-run logic beyond the boot GameMode; safe
  to open in the editor for authoring the widgets.

---

## 6. Logging & Test Plan (log-heavy → test-suite → prune)

**Bring-up (log every line):** every state transition, every decision (which paks *would*
mount, language fallback steps, each GameFeature transition), temporarily at `Display`/`Verbose`
— even below the permanent convention — so first runs are fully traced. `LogAridGBoot`
already exists (TB-boot-loader R9); crank `-LogCmds="LogAridGBoot VeryVerbose"`.

**Test-suite execution (enumerate the boot scenarios), each with an expected log signature:**

| # | Scenario | Expect |
|---|----------|--------|
| T1 | Fresh first launch (no `Aridecan.ini`) | defaults written; OS-locale detected; B0→B8 |
| T2 | Returning launch (config present) | config honored; no rewrite |
| T3 | Editor open + PIE | Regime 1 skipped; PIE plays; no BootMap force |
| T4 | Local package (Phase A) | boots BootMap → main menu; stub logs intended order |
| T5 | Age gate: No | graceful exit, no crash |
| T6 | Missing/!resolved `LocText` | (Phase C) Error + graceful exit |
| T7 | Disabled tier in `Aridecan.ini` | (Phase C) tier + its loc auto-excluded; order dump correct |
| T8 | Phase timeout (inject a stall) | (Phase C) Error names the stuck plugin; graceful exit |
| T9 | Packaged, tiers Active→Installed | (Phase C) no double-mount (O8); tiers reach Active |

**Prune:** once the suite passes and we're happy, drop transient lines to the permanent
convention (TB-boot-loader Diagnostics table): `Display` = milestones/mount summary, `Verbose`
= resolved order dump + per-package detail, `VeryVerbose` = per-file. The debug surface stays
in the code, off by default, switchable via log config — nothing to re-add later.

---

## 7. UE6 / Verse Migration Strategy  *(the headline concern)*

UE6 (~2028–2029, ≈ our first-playable) deprecates **Blueprints + Actors** (memory
`ue6-verse-migration`). The mitigation is **isolation + documentation** so the port is a
contained, well-mapped job — not archaeology.

**Isolation:** *all* boot infra is the `AridGBoot` plugin + the three OGMMGA glue classes
(§2). The UE6 port surface is that one plugin + three files, nothing else in the game.

**Keep the deprecated bits THIN.** The parts UE6 deprecates are the Regime-2 **Actor**
(`AAridGBootGameMode`) and **Blueprint/UMG** widgets. So:
- `AAridGBootGameMode` holds **no logic** — it delegates the B5–B8 state machine to a plain C++
  driver (`AridGBootFlowComponent` or a `UObject`), so a UE6 rewrite re-hosts the shell, not the logic.
- UMG widgets are **thin views** over C++/config state (bind to the driver + settings), so the
  UE6 UI re-skin doesn't touch flow logic.
- Regime 1 (the C++ module, Slate, pak API, config) is **engine infrastructure**, more likely
  to have a direct UE6 equivalent than gameplay Actors.

**Per-touchpoint migration table** (maintained as we implement; each engine API the ABM
touches, tagged with UE6 risk + the port note):

| Engine touchpoint | Used in | UE6 risk | Migration note |
|---|---|---|---|
| `FPreLoadScreenManager` / `EarlyStartupScreen` | B0–B3 | Med | Slate preload; expect a UE6 analogue — keep our screen a thin `FPreLoadScreenBase` |
| `FPakPlatformFile::MountPaksEx` | B3 | Med | Mount API may shift (esp. if UE6 forces IoStore); the `IAridGPakMountPlan` seam localizes it |
| `FCoreDelegates::TSConfigReadyForUse` / `GConfig` | B1 | Low | Config system is stable core |
| `UGameInstance::StartGameInstance` | B4 | **High** | GameInstance is a UObject/engine class likely reworked; keep the override a 2-line `Browse()` |
| `UGameFeaturesSubsystem` | B7 | Low–Med | GameFeatures is strategic for Epic; likely persists/evolves cleanly |
| `AGameModeBase` (Actor) | B5–B8 host | **High** | Deprecated in UE6 — thin shell (above) so only the host changes |
| UMG / CommonUI widgets | B5–B8 UI | **High** | Deprecated — thin views so re-skin ≠ rewrite |
| `UGameUserSettings` | graphics/audio | Low | Engine-owned; we only extend it |

**Documentation rule (Peter's condition):** every engine-API touchpoint carries an inline
`// UE6: <risk/plan>` comment mirroring this table, so the code *is* the port manual and this
doc is the index. No undocumented engine coupling ships.

---

## 8. Coupled / open items

- **Cook Stage 2–5 multi-pak split** — gates Phase C (nothing to mount until separate paks
  exist). See [TB — CI Cook](TB-ci-cook.md) O-A, memory `dlc-loadables-scaffold` (ChunkIds).
- **O6** packaged-vs-editor `PostConfigInit` parity, **O8** suppress tier auto-mount
  (Active→Installed), **O9** GameFeature pak routing — all from TB-boot-loader, all land in
  Phase B/C.
- **GameFeature initial state** currently `Active` (scaffold default) → `Installed` in Phase C.

---

## Related
- [TB — Boot Loader](TB-boot-loader.md) — the architecture this plan implements
- [Boot Manager](../gdd/boot-manager.md) — ABM/MBM design
- [TB — CI Cook](TB-ci-cook.md) — the cook that produces the paks the ABM mounts
- [Content Architecture & DLC](../gdd/content-and-dlc.md) — tiers, load stack
- [CB — Main Menu](../creative-briefs/CB-main-menu.md) · [CB — Settings Menu](../creative-briefs/CB-settings-menu.md)
