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

- **Each of the 5 in-scope loadables populated** with placeholder content that demonstrably
  **mounts (B3)** and **activates (B7)** — the "cube appears only when its pak is present" proof, per
  tier + per loc pack. For the LocText packs, that content is a **machine-translated pass over the
  project's 8 string tables** (#18) — currently they hold zero assets, so there is nothing to cook.
  French is the leading candidate: a plausible real ship language, and ~15–20% longer than English so
  it surfaces UI overflow while the screens are still cheap to change.
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
| **3** | Video/Audio settings backend (`UOgmMgaGameUserSettings` + Scalability + submixes) | I write C++, Peter wires UMG | M | **DONE** (P4 113/114), verified in Standalone — Video + UI-scale + 8-channel submix audio + the `WBP_SettingsTabBase` tab framework. Outstanding = **packaged-build verification** + polish (#16) |
| **4** | Controls rebind (Enhanced Input user-settings; `IA_`/`IMC_` assets; rebind panel) — build spec: [TB — Controls Rebind](../technical-briefs/TB-controls-rebind.md) | split | M | **NEARLY DONE** (P4 116–132) — category-cut `IA_`/`IMC_` assets, collapsible categorized screen, click-to-capture rebind, layer- *and* context-scoped conflict detection, canonical order, per-cell clear, settings-wide Reset, full loc pass. Outstanding: gamepad glyphs in cells, Move/Look axis sub-groups, M1/M2 checkbox function, and **the scalar rows — config fields exist since CL 116 but have no UI** (#15) |
| **5** | DLC content — populate the in-scope loadables with placeholder content | Peter drives, I guide | M | **BARELY STARTED** — all 8 plugins exist, but Spicy/SuperSpicy hold only their `GameFeatureData` + `ST_ContentAdvisory`, and the **loc packs contain zero assets**. Mount/activate proof unbuilt; ChunkIds still `-1`. **Scope now 5 loadables — LocVoice cut (#19).** LocText content comes from the MT string-table pass (#18), which must land **before** the WS6 cook spike |
| **6** | CI cook all paks/DLCs (`mga-weekly Full`; tier + loc paks; leak gate) | I drive (build scripts) | L→M* | **NOT STARTED — critical path.** Base single-pak cook green (first green 2026-07-12). Multi-pak split unreproduced on 5.8; DLC-on-DLC release-version chaining unresolved; **and the tiers now carry BINARIES** (the Spicy/SuperSpicy C++ provider modules), so the cook must build + stage per-platform modules — heavier than the content-only 5.3 demo |

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

### The real remaining unknown — DLC-on-DLC dependencies (the spike's actual job)
The 5.3 video cooked ONE DLC. M1 has **5 in scope with a dependency graph** (`SuperSpicy → Spicy`;
`LocText_<Tier> → <Tier>`) — the `LocVoice_* → <Tier>` edges are gone with the voice cut (#19), which
takes the graph from 8 nodes to 5 but does **not** remove the hard part: `SuperSpicy → Spicy` is still a
DLC depending on a DLC. Open: how `-BasedOnReleaseVersion` handles that — probably a **chained release
version** (base → cook Spicy stamping a release *incl.* Spicy → cook SuperSpicy based on that) or a fixed
cook order. First thing the spike nails down.

**Second unknown, new since the 5.3 recipe: the tiers now carry BINARIES.** Spicy and SuperSpicy grew C++
modules for the content-advisory providers, so the DLC cook must build and stage a per-platform tier
module, not just content. The 5.3 demo was content-only — this part is not a replay.

### WS6 leak-audit discipline (the real correctness constraint)
- Necessary: every DLC asset under its own plugin `Content/` (`/Spicy/…` etc.) — done.
- Also necessary: **base never hard-references into a tier folder** — base exposes data-driven hooks;
  the tier fills them on activation (TB-boot-loader R10, additive-not-same-path). A stray hard
  `/Game → /Tier` ref leaks DLC into the base pak / breaks base when DLC absent. The **audit gate**
  cooks then greps each pak manifest to confirm no cross-boundary leak.

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
