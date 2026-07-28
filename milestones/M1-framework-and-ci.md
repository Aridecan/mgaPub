# CoreX-M1 — Front-End Framework + DLC Load/Override + CI Pak Pipeline

> **Status: in progress — workstream table refreshed 2026-07-26** (P4 changes through 132). The
> definition-of-done below is unchanged since 2026-07-18; only the status has moved. This is a living
> tracking doc — check items off as they land.
>
> **Shape of the remaining work:** the front-end pillar is largely built (WS1/WS3 done, WS4 nearly), and
> **the build pillar has not started**. WS6 is the critical path, not merely the biggest risk: it gates
> WS5's mount/activate proof, WS1's real B3 pak mount, *and* the 4th acceptance config — which can only be
> tested in a packaged build, because the editor auto-disables SuperSpicy when Spicy is off.

## Framing — "CoreX" (EA design flow)

**CoreX = proving out the "X" of the game** — the core technologies a *viable* game requires, de-risked
with placeholders before content is built. This is **CoreX-M1**, proving three X's:
1. **The menu system** (boot → dressed main menu + working settings)
2. **Spicy / Super Spicy loading AND override** — DLC tiers mount, activate, and **polymorphically
   override content earlier in the chain** (the whole game's content-override mechanism, proven minimally)
3. **The CI** (cook every pak/DLC automatically)

Placeholder-everything is *correct* here — we're proving the pipes, not filling them.

## One-line goal

**Boot cleanly to a fully-dressed main menu; prove DLC load + override; get the CI cooking every pak/DLC.**
Two pillars: **(A) front-end framework**, **(B) build pipeline**. No gameplay, no saves.

---

## Definition of Done

### Pillar A — Front-end framework
- **Boot flow is visible end-to-end** — every ABM state ([TB — Boot Loader](../technical-briefs/TB-boot-loader.md))
  is a distinct on-screen placeholder so you can always tell which state you're in:
  - **B0–B3** `EarlyStartupScreen` (Slate, no UObjects): logo → "Loading…" → mount progress
  - **B5** age gate (interactive Yes/No, localized, quit-on-No) — **before** any mature tier activates
  - **B6** legal splash + streamer/recording advisory (localized; **not** tier-dependent)
  - **B7** GameFeature activation ("Initializing…") — tiers activate here; **each tier's activation
    (`UGameFeatureAction`) registers its content-advisory override provider**
  - **B7.5 content warning — NEW, post-activation (2026-07-18).** Tier-composed via the
    activation-registered **override providers** (base → Spicy → SuperSpicy polymorphic `Super::` chain),
    NOT string tables. This is the **DLC load + override** test oracle (below). **Reorders
    TB-boot-loader** (content warning was B6, pre-activation) → reconcile that TB.
  - Placeholders = **engine-drawn coloured panels + state-name text (zero art files)**; real art later.
- **Main menu (B8) reachable.** **Settings** and **Exit** functional; **New Game / Continue / Load /
  Mods present but disabled** (no save system in M1).
- **Settings working:**
  - **Video** — resolution, window mode, VSync, quality preset, via a `UGameUserSettings` subclass
    (`UOgmMgaGameUserSettings`); confirm/revert on resolution change.
  - **Audio** — submix volume sliders (master / music / SFX / voice / ambient / UI).
  - **Controls** — **remap the default keyboard + gamepad bindings** (e.g. Forward: W / L-stick-up;
    Action Bar 1: `1` / left face button). Straight rebind over Enhanced Input user-settings. *Not* the
    in-game action-bar content-remap (that's a gameplay feature).
  - **Language** — already shipped (P4 102/104/105).

### Pillar B — Build pipeline

> **SCOPE CHANGE 2026-07-26 (Peter): the LocVoice packs are cut from M1** — the voice side is being
> re-looked at, so carrying three empty skeletons through the cook buys no proof. **8 loadables → 5**
> (Spicy · SuperSpicy · LocText_Base_en-US · LocText_Spicy_en-US · LocText_SuperSpicy_en-US).
> Deferred, not deleted (#19). This also simplifies WS6: the `LocVoice_* → tier` edges drop out of the
> DLC-on-DLC dependency graph the cook spike has to solve.

> **SCOPE CHANGE 2026-07-28 (Peter): the three `*_en-US` LocText packs are replaced by
> `LocText_Base_fr`.** **5 loadables → 3** (Spicy · SuperSpicy · **LocText_Base_fr**). Two independent
> reasons, either sufficient:
>
> 1. **They prove nothing.** en-US is effectively the source culture, so a "translation" pack for it
>    is a no-op by construction — it would demonstrate the loc-pack *mechanism* only in the one case
>    where the mechanism has nothing to do.
> 2. **They cannot be built at all** (#28, found 2026-07-27). A hyphen in a DLC plugin name produces
>    an invalid Zen project id — `FApp::GetZenStoreProjectIdForProject` does no sanitisation — so the
>    cook dies with `Failed to delete oplog on the ZenServer`. This is not a property of these three
>    packs but of every region-qualified culture: `fr` builds, `pt-BR` / `zh-Hans` / `en-GB` cannot.
>    A definition of done cannot require artifacts that are currently impossible to produce.
>
> **`LocText_Base_fr` replaces them and is a stronger proof than all three combined**: a real
> translation of a real target (the Core string tables), cooked as a **5.8 KB** installable pack,
> verified by Peter in a packaged build across install → switch → restart → switch back → restart, and
> confirming the partial-translation fallback (Option (a)) with French line 1 over English lines 2–3.
>
> **What is NOT dropped:** the packs stay in the depot (like LocVoice — defer, do not delete), and
> #28 must still be solved before shipping any regional language. This narrows M1's *proof
> obligation* to what can actually be demonstrated; it does not narrow the loc design.

- **Each of the 3 in-scope loadables populated** with placeholder content that demonstrably
  **mounts (B3)** and **activates (B7)** — the "cube appears only when its pak is present" proof, per
  tier + per loc pack. For the loc pack, that content is a **machine-translated pass over the
  project's string tables** (#18). French was chosen as the leading candidate — a plausible real ship
  language, and ~15–20% longer than English so it surfaces UI overflow while the screens are still
  cheap to change — and it is the one that shipped.
- **CI (`mga-weekly Full`) cooks the full pak set** — base game + every tier pak (Spicy / SuperSpicy)
  + every **LocText** pak — with a **leak-audit gate** (no base↔DLC cross-references).

### Acceptance test — content warning via activation-registered override (Peter, 2026-07-18)
The **post-activation content warning (B7.5) queries an overridable provider**, and each tier's activation
**registers its override** — so the screen is the **observable oracle for DLC load AND override**, WS1/WS5/WS6
testing each other with no separate harness. Four install configs:

| Config | Expected content warning | Proves |
|---|---|---|
| Base | base only | baseline provider |
| Base + Spicy | base + nudity / clothing-destruction | Spicy activates + registers its override (chains `Super::`) |
| Base + Spicy + Super Spicy | + explicit content | full override chain; `SuperSpicy→Spicy` dep satisfied |
| Base + Super Spicy (no Spicy) | **base only** + "SuperSpicy skipped" log | **dependency guard** — missing-dep auto-exclude (TB-boot-loader B3); SuperSpicy's provider never registers |

The 4th is a **negative test**: SuperSpicy depends on Spicy (plugin descriptor **and** — if its provider
class inherits Spicy's — at link time), so it can't activate without Spicy → its override never registers →
base-only. Never explicit-without-the-nudity-layer.

### Override mechanism (the CoreX-M1 "prove the X" centerpiece)
- **Base:** `UAridGContentAdvisoryProvider` (`virtual GetWarnings()`); `UAridGContentAdvisorySubsystem`
  (GameInstanceSubsystem) holds the active provider.
- **Spicy:** `USpicyContentAdvisoryProvider : UAridGContentAdvisoryProvider`, `GetWarnings()` =
  `Super::GetWarnings()` + spicy line. A `UGameFeatureAction` in Spicy's `GameFeatureData` `Actions` array
  registers it on `OnGameFeatureActivating(FGameFeatureActivatingContext&)`, unregisters on
  `OnGameFeatureDeactivating`. **One reusable** `UGameFeatureAction_RegisterContentAdvisory` (framework
  module) with a `TSubclassOf<UAridGContentAdvisoryProvider> ProviderClass` UPROPERTY — each tier's
  GameFeatureData holds an instance with `ProviderClass` set (data-configured, no per-tier action code).
- **SuperSpicy:** `USuperSpicyContentAdvisoryProvider : USpicyContentAdvisoryProvider`, chains `Super::`
  again; its own `UGameFeatureAction` registers it as most-derived.
- Widget (B7.5) → subsystem's active provider → polymorphic chain → base+spicy+super composed. **Proves
  later-DLC-overrides-earlier via standard polymorphism, registered at activation** — the content-override
  tech the whole game needs. C++ makes the tier dependency real at link time; a Blueprint override variant
  is the moddable alternative (decide per case). This is the general mechanism for Spicy/SuperSpicy
  overriding base clothing/body/content later, proven here on the smallest possible surface.
- **Activation hooks (UE 5.8, verified — see [[gamefeature-activation-hooks]] memory):** per-plugin
  `UGameFeatureAction` (in the GameFeatureData `Actions` array) does the override registration; a global
  `IGameFeatureStateChangeObserver` in AridGBoot (`UGameFeaturesSubsystem::Get().AddObserver`) drives B7
  progress + the "SuperSpicy skipped" log. Child provider classes added via editor New-C++-Class into the
  tier module; add the base (and Spicy, for SuperSpicy) module to the tier `Build.cs` deps.

### Explicit non-goals (out of M1)
Save system · New Game → level / any gameplay · in-game action-bar *content* remapping ·
upscalers (DLSS/FSR/XeSS) · dual-device audio routing · real (non-placeholder) art.

---

## Workstreams

Status as of **2026-07-26** (P4 through change 132). Issue numbers are `Aridecan/mgaPub`.

| # | Workstream | Owner | Effort | Status |
|---|-----------|-------|--------|--------|
| **1** | Boot-flow screens + placeholders (incl. legal B5/B6) | Peter drives (UMG/Slate), I guide | M | **DONE** (P4 105–112) — B0 Slate splash → B5 age gate → B6 legal → B7 progress → B7.5 content advisory → B8 menu. Remaining polish: real art (deferred by design); B7 auto-advance on *real* activation; **B3 driven by a real pak mount — gated on WS6** |
| **2** | Main-menu wiring (Settings + Exit; disable the rest; optional build stamp / SubscribeStar) | Peter drives, I guide | S | **PARTIAL** (P4 98–100) — seven buttons exist; Settings pushes, Exit quits. Outstanding: **disable New Game / Continue / Load Game / Mods** + build stamp (#14). The temp Continue→content-warning dump hook has been pulled |
| **3** | Video/Audio settings backend (`UOgmMgaGameUserSettings` + Scalability + submixes) | I write C++, Peter wires UMG | M | **DONE** (P4 113/114) — Video + UI-scale + 8-channel submix audio + the `WBP_SettingsTabBase` tab framework. **VIDEO + UI SCALE VERIFIED IN PACKAGED BUILDS (Peter, 2026-07-27), including persistence:** his standing routine on each new build is drop the resolution, switch to windowed, lower the UI scale, restart — and all three survive. That covers the apply path *and* the `GameUserSettings.ini` save/load round-trip, which is the half most likely to fail silently (and did: the UI-scale slider drove nothing until P4 143). **AUDIO slider verification is DEFERRED TO M2 (Peter, 2026-07-27):** with no sound in an M1 build a submix slider that silently fails and one that works are indistinguishable, so the check only becomes meaningful alongside M2's ambient bed and footsteps — it lands in M2 Pillar C. Polish remains under #16 |
| **4** | Controls rebind (Enhanced Input user-settings; `IA_`/`IMC_` assets; rebind panel) — build spec: [TB — Controls Rebind](../technical-briefs/TB-controls-rebind.md) | split | M | **NEARLY DONE** (P4 116–132, **156**) — category-cut `IA_`/`IMC_` assets, collapsible categorized screen, click-to-capture rebind, layer- *and* context-scoped conflict detection, canonical order, per-cell clear, settings-wide Reset, full loc pass. **2026-07-27 (P4 156), both wired + verified by Peter: gamepad glyphs in cells** (`UAridGInputGlyphLibrary` — per-KEY lookup, because `CommonActionWidget` resolves through the *live* input device and would blank the gamepad column the moment someone typed) **and M1/M2 checkboxes seeded from pad-layer membership** (`UAridGInputLayerLibrary` — reads the layer IMCs, so a chord stays defined by the asset that decides behaviour). Outstanding: Move/Look axis sub-groups and **the scalar rows — config fields exist since CL 116 but have no UI** (#15). **The M1/M2 checkbox TOGGLE behaviour is DEFERRED TO CoreX-M3 (Peter, 2026-07-27)** — seeding is done, but making a tick *move* a binding between layer contexts cannot be verified until something consumes the bindings, and no action bar exists before M3. Same reasoning the TB already applies to runtime consumption of bindings generally |
| **5** | DLC content — populate the in-scope loadables with placeholder content | Peter drives, I guide | M | **DONE (2026-07-28)** — **scope is 3 loadables**: LocVoice cut (#19, 2026-07-26) and the three `*_en-US` LocText packs replaced by `LocText_Base_fr` (2026-07-28 — they prove nothing *and* are uncookable per #28). All three demonstrably mount and activate in a **packaged build**: Spicy and SuperSpicy carry `GameFeatureData` + `ST_ContentAdvisory` and compose the advisory 1→2→3 lines, with the negative case (SuperSpicy without Spicy) correctly refused and reported; `LocText_Base_fr` is a 5.8 KB installable translation verified across install → switch → restart. B3 now names what is installed and *why* anything was skipped. Remaining: ChunkIds still `-1` (cook-stage work, not a proof gap) |
| **6** | CI cook all paks/DLCs (`mga-weekly Full`; tier + loc paks; leak gate) | I drive (build scripts) | L→M* | **LARGELY DONE.** Base + per-tier + per-loc-pack cooks all green on 5.8, including the tier **BINARIES** (Spicy/SuperSpicy C++ provider modules) that made this heavier than the content-only 5.3 demo. Base tier leak fixed + gated (P4 137/138/140/141); 4-config acceptance matrix passes on real CI artifacts; French shipped as a 5.8 KB installable pack; **DLC-on-DLC dependency resolved via `-DlcPluginOnly`, not the planned release-version chain** (#26, P4 153), with tier isolation asserted by `audit.ps1 -DlcStageRoot`. Remaining: ChunkIds/cook-stages, and the IoStore-vs-`.pak` container question (TB-ci-cook O-F) |

\* **WS6 de-risked (2026-07-18):** Peter shipped per-plugin pak emission in **UE 5.3** (demo: a cube that
appears/disappears with its pak) — so this is *reproduce on 5.8*, not research. The enabling discipline —
**each DLC's content isolated in its own plugin `Content/`** — is already how the project is structured.

### Mechanism — RESOLVED (Peter's 5.3 recipe, `Imports/Video4Script.docx`, 2026-07-18)
Tiers use the **`-DLCName` + `-BasedOnReleaseVersion` DLC plugin cook**, NOT chunk-IDs. Proven in 5.3
(the cube appears only when its pak is copied into the base `Paks/` = optional, additive DLC pak). Command:
```
RunUAT BuildCookRun ... -cook -stage -pak -dlcname=Spicy -DLCIncludeEngineContent \
  -basedonreleaseversion=0.1.0-prod   (separate staging dir)
```
→ pak at `…/Plugins/GameFeatures/<Tier>/Content/Paks/Windows/*`; Jenkins stages into an archive-shaped
`…/Content/Paks`, 7-zips (excl. `.pdb`), ships. **Prerequisite: the base cook must CREATE a release
version** (`0.1.0-prod`) so each DLC diffs against it. Full recipe → [[dlc-pak-cook-recipe]] memory.
**Already free in 5.8:** GameFeatures stable + project scaffolded (plugins, build.cs, `.uplugin`,
AssetManager scan, the loadable plugins) → skip the whole first half of the 5.3 video, go straight to the cook.

### DLC-on-DLC dependencies — RESOLVED 2026-07-27 (#26), and not the way we planned
The 5.3 video cooked ONE DLC. M1 has **5 in scope with a dependency graph** (`SuperSpicy → Spicy`;
`LocText_<Tier> → <Tier>`) — the `LocVoice_* → <Tier>` edges are gone with the voice cut (#19), which
takes the graph from 8 nodes to 5 but does **not** remove the hard part: `SuperSpicy → Spicy` is a DLC
depending on a DLC.

**The planned answer was a chained release version** (base → cook Spicy stamping a release *incl.*
Spicy → cook SuperSpicy based on that). **We did not use it, because it does not work.** A DLC cook
records base packages as *already-cooked* rather than as its own, so `-createreleaseversion` on a DLC
cook yields a registry of that DLC's packages only — not the base∪Spicy union the chain needs. There
is no supported way to merge two cooked asset registries, and the scheme would have made the tiers
strictly order-dependent besides.

**What we used instead: `-DlcPluginOnly`**, which changes the cook rule from *"cook what the base
release lacks"* to *"cook only what lives in THIS plugin"*. `CookRequestCluster.cpp` rejects
out-of-plugin packages with `ESuppressCookReason::NotInCurrentPlugin`, and
`RecordDLCPackagesFromBaseGame` (`CookOnTheFlyServer.cpp`) returns early without reading the based-on
registry at all. The dependency graph stops mattering to the cook entirely: no ordering, no extra
release directories, and the bug becomes inexpressible rather than merely diffed away.

**The trade-off is real and is now a content rule.** Anything a tier references outside its own plugin
is assumed present in the base game; if it is not, the failure is a *missing asset at runtime, not a
cook error*, and the leak audit cannot catch it (it asserts that content which should be absent is
absent, not that content which should be present is present). So tier content must reference only its
own plugin or the base. Shared-but-not-in-base content belongs in the base cook, or in a plugin the
tier depends on — never smuggled in through a dependency walk.

Gated by `CI/audit.ps1 -DlcStageRoot`, which asserts no tier container holds another delivered
plugin's content — an assertion rather than a comment precisely *because* the fix is a cooker flag,
and a flag can be dropped or renamed between engine versions while the cook still goes green.

**Second unknown, new since the 5.3 recipe: the tiers now carry BINARIES.** Spicy and SuperSpicy grew C++
modules for the content-advisory providers, so the DLC cook must build and stage a per-platform tier
module, not just content. The 5.3 demo was content-only — this part is not a replay.

### WS6 leak-audit discipline (the real correctness constraint)
- Necessary: every DLC asset under its own plugin `Content/` (`/Spicy/…` etc.) — done.
- Also necessary: **base never hard-references into a tier folder** — base exposes data-driven hooks;
  the tier fills them on activation (TB-boot-loader R10, additive-not-same-path). A stray hard
  `/Game → /Tier` ref leaks DLC into the base pak / breaks base when DLC absent.
- **Third, and the one that actually bit us: the COOKER itself can be the reference.** See below.

#### The tier leak (found 2026-07-26 in the first packaged base-only build; fixed P4 137)

A base-only install behaved as though both tiers were present: it auto-activated Spicy and
SuperSpicy, registered their advisory providers at rank 1/2, then failed to resolve their string
tables. Root cause chain, each link verified against artifacts rather than inferred:

1. `BuiltInInitialFeatureState: Active` in each tier `.uplugin` does **double duty**. It makes the
   **cook commandlet** register and activate the tier — the activation lines appear in the cook log,
   before the pak step.
2. Registering **loads** the tier's `GameFeatureData`, and the cooker sweeps packages loaded during
   startup into the cook as **unsolicited** — so `Spicy.uasset` / `SuperSpicy.uasset` landed in the
   base artifact. `EnabledByDefault:false` and `ExplicitlyLoaded:true` do not prevent this; they
   govern *plugin* loading, not game-feature state.
3. At runtime the same flag auto-activated both tiers, because every loadable's `.uplugin`
   **descriptor** ships in the base pak. The tier string tables did **not** leak (they are referenced
   only by a C++ path string, so nothing loaded them) — hence "active but no text".
4. `PrimaryAssetTypesToScan` `CookRule: AlwaysCook → Unknown` (P4 136) was correct but **a no-op**:
   `Unknown` means "cook if depended upon", and the cook-time load *is* that dependency.

**This was also the Steam compliance breach** — adult tier data present in the artifact we would
upload. See [Steam adult-content boundary].

**The fix — one rule, one place:** a project GameFeatures policy,
`UAridGGameFeaturePolicy` (`GameFeaturesManagerClassName` in `DefaultGame.ini`), overriding
`InitGameFeatureManager` to filter `LoadBuiltInGameFeaturePlugins`:
- **Base cook** (no `-DLCName`): no DLC-delivered plugin may load at all. Note the filter must
  **reject** these, not merely cap them at `Registered` — `Registered` is precisely the state that
  loads the GameFeatureData, and the engine force-raises any lower auto-state to `Registered` while
  cooking. Note also that a "does the content exist?" test is useless here: during any cook the
  source content is on disk. The cook's question is *"is this the DLC I was asked to build?"*
- **DLC cook** (`-DLCName=X`): X plus its DLC-delivered dependencies, nothing else.
- **Runtime / editor:** gate on whether the plugin's **content** is really installed — loose packages,
  or the plugin's own `.pak` — and on the same being true of everything it depends on. Descriptor
  presence is intent; content presence is truth. The same test removed the phantom `en-US` rows from
  Settings > Language, which had the identical cause.

Config 4 of the acceptance matrix (SuperSpicy without Spicy) falls out of the dependency arm as a
clean skip rather than a mount failure:
`Skipping game feature 'SuperSpicy': 'Spicy' content is not installed.`

#### Two things the availability test cannot be, both learned by failing

**It cannot look for the tier's packages as files.** We cook **IoStore**: cooked packages live in a
`.ucas` addressed by chunk id through the package store and are *not* files — walking the plugin's
`Content/` tree finds nothing even with the tier fully installed and mounted. Nor can it use
`FPackageName::DoesPackageExist`: a GameFeature plugin is `ExplicitlyLoaded`, so its `/Spicy/` mount
point does not exist yet when the built-in scan runs. What it tests instead are the two things that
*are* plain files: loose packages under `Content/` when uncooked, and the tier's own **container**
(`<Plugin><Project>-<Platform>.utoc/.pak`) in the game's `Content/Paks` when packaged.

**The tier container has to be delivered into the game's `Content/Paks`.** The cook leaves it at
`<Tier>/Content/Paks/<Platform>/` and **nothing mounts it there** — the startup pak scan only looks
in the project's `Content/Paks`, and a built-in (file-protocol) GameFeature has no mount step of its
own; it assumes its content is already mounted. Shipping the plugin folder as-is gave a tier that was
detected, loaded, and then killed with *"Game Feature Data package not found … unrecoverable error"*.
Relocating the container — the same step Peter's UE 5.3 recipe performed — is now what `publish.ps1`
builds into the tier archive, and the tier archive contains **nothing but** that container set.

#### Acceptance matrix — PASSING (2026-07-26, on CI artifacts)

`CI/acceptance.ps1` builds all four installs from the staged base plus the DLC stages, runs each
headless, and asserts from the log. Verified end-to-end against artifacts `mga-weekly` actually
produced — base release **b22**, both tier DLCs cooked against it — not a local build:

| Config | Tier containers installed | Outcome |
|---|---|---|
| 1 · base | none | both tiers skipped; base advisory at rank 0 only |
| 2 · +Spicy | Spicy | Spicy loads, overrides at **rank 1**, reaches Active |
| 3 · +Spicy +SuperSpicy | both | full chain: base 0 → Spicy 1 → **SuperSpicy 2**, both Active |
| 4 · +SuperSpicy, no Spicy | SuperSpicy | **SuperSpicy skipped, citing Spicy** — no half-load |

No `Failed to find string table entry` in any configuration. Config 4 is the one that cannot be
produced in the editor at all (it auto-disables SuperSpicy when Spicy is off), which is why the
matrix needs packaged installs.

#### Still open in WS6

- ~~**DLC-on-DLC bleed.**~~ **FIXED 2026-07-27 (#26, P4 153).** A `-DLCName=SuperSpicy` cook also
  staged `Spicy/Content/Spicy.uasset`, because SuperSpicy depends on Spicy and the base release does
  not contain Spicy. Not a compliance problem (both are adult tiers, neither ships on Steam) but it
  is duplication, and it made `publish.ps1` emit a second, content-less `ogmmga-spicy` archive that
  **overwrote the real one**. Fixed by cooking DLCs `-DlcPluginOnly` rather than by the chained
  release version originally planned — see the resolved section above for why the chain does not
  work. Now gated: `audit.ps1 -DlcStageRoot` asserts tier isolation, and the audit stage runs on
  `SCOPE=Dlc` as well as `Full`. Publishing one tier per stage remains as defence in depth.
- **Loc packs are not published at all** — `publish.ps1` only walks `Plugins/GameFeatures`. Moot
  while the packs hold zero assets (#18/#19), but it must grow a `Plugins/Localization` arm.
- **Container-format contradiction** — see TB-ci-cook O-F.

#### The audit gate (`CI/audit.ps1`, Stage 6)

The gate is now a **content assertion**, not a size report: no content from any DLC-delivered plugin
may appear in a base container. The delivered-plugin list is **discovered** from
`Plugins/{GameFeatures,Localization}/*`, so a new tier is covered without editing the script, and the
gate **throws rather than passing vacuously** if that discovery comes back empty. Descriptors are
explicitly allowed through — the match is on the plugin's `Content/` subtree — because the base build
is *supposed* to know a tier could exist; it just must not carry its content.

> **The gate audits `.utoc` as well as `.pak`, and that is load-bearing.** This project cooks
> **IoStore**: the legacy `OGMMGA-Windows.pak` is ~11 MB of descriptors while the real 870 MB payload
> lives in `OGMMGA-Windows.ucas`. The leaked tier assets were **in the `.ucas`** — a pak-only gate
> would have passed the very build that shipped them. `UnrealPak -List` accepts a `.utoc` and lists
> the container's real contents. (Separately: cooking IoStore **contradicts** TB-ci-cook's decision to
> ship legacy `.pak` with `bUseIoStore=false` for ABM mount-order control — that decision needs
> revisiting or the config needs fixing.)

---

## Sequencing

The two pillars parallelize cleanly **by owner**:
1. **Pillar A start:** WS1 boot-flow screens (Peter drives the UMG) — directly the "see every stage" goal;
   legal screens fall out for free.
2. **Pillar B start (parallel):** WS6 pak-routing repro spike (I drive) — reproduce the 5.3 per-plugin
   pak recipe on 5.8 + settle the mechanism choice above. This is the milestone's long pole.
3. Then WS3 (settings backend) ∥ WS2 (menu wiring), WS4 (controls), and WS5 (DLC content — feeds WS6's
   real cook once routing is proven).

---

## Related
- [TB — Boot Loader](../technical-briefs/TB-boot-loader.md) — ABM state machine B0–B8 (the boot flow)
- [TB — CI Cook](../technical-briefs/TB-ci-cook.md) — the 7 cook stages, pak routing, audit gate
- [TB — Settings Menu](../technical-briefs/TB-settings-menu.md) · [TB — Language Settings](../technical-briefs/TB-language-settings.md)
- [CB — Controls](../creative-briefs/CB-controls.md) · [CB — Main Menu](../creative-briefs/CB-main-menu.md)
- [Content Architecture & DLC](../gdd/content-and-dlc.md) — tiers as GameFeature plugins
