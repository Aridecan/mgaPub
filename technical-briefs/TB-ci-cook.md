# TB — CI Cook Pipeline

## Overview

This technical brief defines the **Jenkins CI build/cook pipeline** for MGA: how the single
UE 5.8 project is cooked into the shipping set of paks (base game + GameFeature content tiers
+ localization + region overrides + demo), how procedural content is baked first, how
editor-only tooling is kept out of the paks, and how builds are stamped and distributed on
the weekly cadence.

It is the keystone that ties together:
- [TB — Boot Loader](TB-boot-loader.md) — the paks/plugins this pipeline produces are what the ABM mounts + activates.
- [TB — Content Directory](TB-content-directory.md) — the `Content/` + plugin layout being cooked.
- [TB — Road Conformity Brush § Packaging](TB-road-conformity-brush.md#packaging--cook-behaviour) — the editor-only-asset gate (this TB generalises it).
- [Save System](../gdd/save-system.md) — save-schema versioning + the weekly Bittorrent/SubscribeStar release cadence.

**Standing rule (reuse UE):** the pipeline is built on UE's own `RunUAT BuildCookRun`,
release-version DLC cooking, culture cooking, AssetManager chunking, and the GameFeatures
cooker — not a bespoke cook system. Jenkins orchestrates; UAT does the work.

> **Engine-version caveat.** UAT flags, class names, and file paths are from the local UE 5.8
> source at `d:/uegit` (2026-07-09 dive). Treat flag/API names as authoritative.

---

## Current State (2026-07-09)

- **Jenkins:** up and healthy, 2 executors, **zero jobs defined** → pipeline is greenfield.
- **Project:** `OGMMGA` is a **C++ code project** on UE 5.8 (`d:/Aridecan/perforce/MGA/OGMMGA`),
  in **Perforce**. Cooks are **code+content** (binaries must build), not content-only.
- **Cook config:** near-default — AssetManager scans `Map` + `PrimaryAssetLabel` +
  `GameFeatureData` (AlwaysCook); everything `ChunkId=-1` (single pak); no DLC/chunk/IoStore
  configured yet. CommonUI enabled. GameFeatures enabled.

---

## Container Format: legacy `.pak`, `bUseIoStore = false`

Ship **legacy `.pak`**, not IoStore `.utoc/.ucas`. In 5.8 legacy `.pak` is still the default
for our Win/Linux/Mac targets, and — decisively — the **ABM needs `FPakPlatformFile`
mount-order control** (`MountPaksEx(Order=…)`), which IoStore moves under the engine. IoStore
is revisited only if a console target ever appears. (See [TB — Boot Loader R7](TB-boot-loader.md#resolved-decisions).)

---

## Cook Mechanisms (UE 5.8, cited)

| Need | Mechanism | Key flags / classes |
|---|---|---|
| Headless build+cook+pak+stage | `RunUAT BuildCookRun` | `-project= -build -cook -stage -pak -package -clientconfig=Shipping -platform=Win64 -nop4 -unattended` |
| Run a pre-cook step | Commandlet | `UnrealEditor-Cmd.exe <proj> -run=<Commandlet> -unattended -nullrhi -abslog=<log>` |
| Content tiers (Spicy/SuperSpicy) | **GameFeature plugin cook** | cooker cooks plugins with state ≥ `Registered` (`GetLoadedGameFeaturePluginFilenamesForCooking`) |
| True same-path override (region) | **DLC release-version cook** | `-CreateReleaseVersion=<v>` (base) → `-BasedOnReleaseVersion=<v> -DLCName=<plugin> -ErrorOnEngineContentUse` |
| Localization paks | **Culture cook** | `-cookcultures=en+ja+…` → per-culture paks |
| Optional/distribution split, demo subset | **AssetManager chunking** | `UPrimaryAssetLabel.Rules.ChunkId` → `pakchunk<N>-<platform>.pak` |
| Pak mount priority | path-based order + explicit | `GetPakOrderFromPakFilePath` (4/3/2/1); `FMountPaksExArgs.Order` (ABM sets this) |

**Notes that shape the pipeline:**
- **GameFeature pak location.** By default a GameFeature plugin's content may fold into the
  main pak; routing each tier to its own `Plugins/<Tier>/Paks/…` pak likely needs a **custom
  cook policy / `FCookPackageSplitter`** — flagged **O-A** below.
- **Chunking ≠ override.** Chunks are for *optional/withheld* content, not shadowing. Use
  chunking for the **demo subset** and any withhold-per-channel split; use DLC-cook for
  same-path replacement.
- **DLC bases on the *base release*, not on lower tiers.** `SuperSpicy` cooks against the base
  release version, not against `Spicy` — tiers are independent additive plugins.

---

## Pre-Cook: Procedural Bake (Stage 0)

**The problem:** PCG-spawned content is **transient** — regenerated at `BeginPlay` unless
`bGenerated` was true at save (`PCGComponent.cpp`), so a naive cook can ship *empty* procedural
areas. MeshPartition **compiled sections** (`ACompiledSection`) *are* serialized and
cook-captured, but the **PCG → MeshPartition interop** path (this project's road-bed
conformity) is **likely transient**.

**Therefore Stage 0 runs before every cook:** a commandlet pass that opens the World Partition
levels, **generates PCG + builds MeshPartition, and resaves** the packages so the baked data
is on disk for the cook to capture:

```
UnrealEditor-Cmd.exe OGMMGA.uproject -run=<GenerateAndResave> -unattended -nullrhi -abslog=Stage0.log
```

(Either a custom commandlet, or `ResavePackages` with a PCG-generate hook.) This subsumes the
road-conformity CI open question (generate → build → cook).

> **Spike O-B (must confirm before trusting Stage 0):** empirically verify the bake persists —
> generate + save in-editor, cook, then `UnrealPak.exe -List` the pak and confirm the road-bed
> section meshes are present. The source left PCG-interop persistence *ambiguous*; this test
> settles whether Stage 0 is "resave" or needs a heavier "generate-in-commandlet" step.

---

## The Pipeline (job graph)

Run per build; stages are ordered but parallelisable where noted.

| Stage | Does | Mechanism |
|---|---|---|
| **0 · Procedural bake** | Generate PCG / build MeshPartition, resave levels | commandlet (above) |
| **1 · Build + base cook** | Compile `OGMMGA` binaries; cook base game; stamp a **release version** | `BuildCookRun -build -cook` + `-CreateReleaseVersion=<build>` → `Main` pak |
| **2 · Tier plugins** | Cook `Spicy` / `SuperSpicy` GameFeature plugins (+ their `_SpicyLoc` etc.) | GameFeature cook → `Plugins/<Tier>/Paks/` (O-A) |
| **3 · Region overrides** | Only assets that need true same-path replacement | `-BasedOnReleaseVersion=<build> -DLCName=regionOverride-<r>` |
| **4 · Localization** | Per-language text/voice paks | `-cookcultures=<list>` → `LocText-*` / `LocVoc-*` |
| **5 · Demo subset** | Ch.1 + 3 side jobs slice | chunk/include-list filter → `Demo` variant paks |
| **6 · Editor-only audit gate** | Fail build if editor-only tooling leaked into paks | see below |
| **7 · Package + stamp + reserve** | Stage/pak/package; write build version; reserve mod slot | `-stage -pak -package` + version stamp |

Stages 2–4 depend on Stage 1's release version (they cook against it). Demo (5) reruns the
relevant cooks with the subset filter. Stage 6 gates 7.

---

## Editor-Only Asset Audit Gate (Stage 6)

Generalises the road-brush's packaging discipline into a **build-failing CI gate**. Editor-only
tooling (the road-conformity brush + its `RT_RoadHeights` 268 MB render target + encoding
materials, plus any dev-only actors) must **never** enter a shipping pak.

- **Mechanism:** editor-only actors carry `bIsEditorOnlyActor = true`; UE's reference walker
  then strips everything reachable *only* through them. `Content/Developers/` and
  `MGA/Maps/Test/` are excluded by cook config.
- **The gate:** after cook, run `UnrealPak.exe -List` on each pak and **grep for a denylist**
  (`RoadConformity`, `RoadHeights`, `RoadHeightEncode`, `Developers/`, `/Test/`). Any hit →
  **fail the build**. Plus a **pak file-size regression check** (a 268 MB swing = the easy
  signal an editor-only flag broke). This is cheap and runs every build.

Detail lives in [TB — Road Conformity Brush § Packaging](TB-road-conformity-brush.md#packaging--cook-behaviour);
this TB owns the *gate* (the CI enforcement), that TB owns the *brush-specific* exclusions.

---

## Versioning, Stamping & Distribution

- **Build stamp:** each build writes `v0.x.x — Build YYYY-MM-DD` (the Main Menu displays it —
  [CB — Main Menu](../creative-briefs/CB-main-menu.md)) and this same version is the
  `-CreateReleaseVersion` id, so DLC/region cooks are traceable to a base build.
- **Save-schema version:** builds carry a save-schema version from first public release; the
  save system migrates forward (N → N+1). CI should **fail if the save-schema version changed
  without a migration function** — a cheap static check ([Save System](../gdd/save-system.md)).
- **Weekly cadence:** shipped via Bittorrent / SubscribeStar. The pipeline produces a **full**
  channel (all owned tiers) and a **demo** channel (Demo variant paks only — `Main`/`Spicy`/
  `SuperSpicy` are *not* included, so the demo can't be hacked into the full game). Which tier
  paks a given player may mount is gated by entitlement, not by the cook.
- **Mod slot reservation (Stage 7):** the pak-mount scheme leaves headroom (a reserved
  `PakOrder` band / directory) so runtime-installed mods mount above official content without
  colliding — consumed by the MBM.

---

## Jenkins Job Structure

The cadence is **weekly, with manual on-demand runs** (matching the weekly release schedule) —
not a nightly. One primary parameterised job covers both:

- **`mga-weekly`** — the primary job. **Scheduled weekly** (cron trigger) **and manually
  triggerable** (Jenkins "Build Now" / parameterised) whenever a build is needed off-cadence.
  A `SCOPE` build parameter selects the depth:
  - **`Full`** (default for the scheduled run) — clean Stage 0–7; produces the weekly release
    artifacts (full + demo channels), non-iterative.
  - **`Quick`** (for a fast manual "did I break it?" check) — Stage 0–1 only (bake + build +
    base cook), iterative (`-iterate`), no tier/DLC/demo stages.
- **`mga-audit`** — Stage 6 gate as a reusable step the weekly invokes; also runnable
  standalone for a quick leak check.
- **Agents:** a Windows agent with the built 5.8 toolchain + a Perforce workspace; `-nop4` in
  UAT (the workspace is synced by the Jenkins P4 plugin, not by UAT). Artifacts (paks, logs,
  `-List` outputs, pak-size CSV) archived per build.

### First increment (what to stand up *now*)

The full tier/DLC/demo stages can't run until the content is actually structured as GameFeature
plugins with a demo subset + release-version setup. So the first job to stand up is
**`mga-weekly` in `Quick` scope** — sync P4 → build `OGMMGA` → Stage 0 bake → base cook → audit
gate. That gives continuous "the project builds and cooks" validation and the pipeline scaffold
to extend, while O-A/O-B (tier pak routing, PCG persistence) are resolved on real hardware.

---

## Build Farm & Platform Matrix

The pipeline is designed to fan out to **one build machine per target platform**, on a
**10 GbE** network, with **Perforce + lore on SSD** (the 1821+ NAS) so sync/cook I/O isn't the
bottleneck. The **Jenkins controller** is **Docker06** (Ubuntu 24.04, in the rack by the NAS +
firewall) — orchestration only; the build agents are separate Windows/Linux/Mac machines.

| Target | Priority | Agent label | Machine | Notes |
|--------|----------|-------------|---------|-------|
| **Windows** | Primary | `mga-build-win` | **Katana** | The one being stood up now; `-platform=Win64` |
| **Linux** | Secondary | `mga-build-linux` | TBD | Targets **latest Steam-machine hardware** (SteamOS/Deck-class); `-platform=Linux` |
| **Mac** | Last | `mga-build-mac` | TBD | `-platform=Mac`; lowest priority |

Each platform is the *same* `mga-weekly` stage design pointed at a different agent + `-platform`.
Until the Linux/Mac agents exist, only the Windows job runs; add the others as **sibling jobs
or a declarative `matrix`** (parallel per-platform on their own agents) — a deliberate later
step, not part of the first increment.

---

## Deferred

- **Zen storage server** — *planned future*, not now. When it stands up it gives the build
  farm a shared derived-data / cook cache across the three agents (and pairs with switching to
  **IoStore** `.utoc/.ucas`). Until then: legacy `.pak` + `bUseIoStore=false` (retains ABM
  mount control; the 5.8 default for our platforms). Note: adopting IoStore later means the ABM
  must move off `FPakPlatformFile` mounting — revisit [TB — Boot Loader](TB-boot-loader.md)'s
  mount model at that point. The boot-manager's PCG-persist-on-save question also feeds the
  Zen decision.
- **Distributed build acceleration (Incredibuild/XGE vs FASTBuild)** — *not yet; measure first.*
  These tools farm out **compilation**, not the cook itself. On this project:
  - **C++ compile is small** (content-heavy game, one module on an *installed* engine, prebuilt
    binary plugins) → distributed C++ compilation, the core value prop of both tools, has near-zero
    ROI here.
  - **Shader compilation** is the real farmable cost inside a cook. **UE distributes shaders
    natively via XGE (Incredibuild)** (`r.XGEShaderCompile`); **FASTBuild is C++-only** and its UE
    shader distribution isn't turnkey → for *this* need, **Incredibuild/XGE > FASTBuild**.
  - **Free levers first:** a **warm/shared DDC that survives between weekly builds** (shaders
    compile once, reused every build) + all-cores local compile — often a bigger real win than
    distribution, at zero cost. Then **Zen** (above) is the farm-wide cook/DDC cache.
  - **Helpers must match the target toolchain.** Distribution needs spare *same-OS* cores. Builds
    run at night (~Mon 03:00) when **Odachi + the wife's desktop — both Windows, powerful, and
    Odachi-clones — are free**, so they're **valid XGE helpers for Katana's Win64 shader compiles**
    (no OS mismatch). A future Linux/Mac target would need its own same-OS helpers.
  - **Sequence:** run the first cooks, read the shader-compile time from the log, *then* decide —
    and check the Incredibuild free-tier license fits a SubscribeStar-funded project.

---

## Open Items

- **O-A — GameFeature pak routing.** Confirm the cook can emit each tier plugin to its own
  `Plugins/<Tier>/Paks/…` pak (custom cook policy / `FCookPackageSplitter`?) vs. folding into
  the main pak. Load-bearing for the tier-pak split. (Mirrors TB-boot-loader O9.)
- **O-B — PCG/MeshPartition persistence spike.** Confirm Stage 0 captures the road-bed bake
  (generate → save → cook → `-List`), and whether "resave" suffices or a generate-in-commandlet
  is required.
- **O-C — DLC delta semantics.** The exact "only new/modified vs base" diff of
  `-BasedOnReleaseVersion` isn't fully documented in source — verify what lands in a region
  override pak empirically before relying on it for legal-swap correctness.
- **O-D — Perforce ⇄ Jenkins.** Confirm the P4 sync/label strategy for reproducible weekly
  builds (changelist pinning, the release-version store under `Releases/<build>/` committed or
  archived).
- **O-E — Suppress GameFeature auto-mount at runtime** (paired with TB-boot-loader O8) — a
  cook/packaging concern only insofar as plugin descriptors ship with the right
  `BuiltInInitialFeatureState`.

---

## Related

- [TB — Boot Loader](TB-boot-loader.md) — mounts/activates what this pipeline cooks
- [TB — Content Directory](TB-content-directory.md) — the layout being cooked (tiers = GameFeature plugins)
- [TB — Road Conformity Brush](TB-road-conformity-brush.md) — editor-only exclusion detail
- [Boot Manager](../gdd/boot-manager.md) — ABM/MBM, pak names, load stack
- [Content Architecture & DLC](../gdd/content-and-dlc.md) — tiers, localization independence
- [Save System](../gdd/save-system.md) — save-schema versioning, weekly release cadence
- [CB — Main Menu](../creative-briefs/CB-main-menu.md) — build/version display
