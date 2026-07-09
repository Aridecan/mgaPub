# TB — UE Project Content Directory Structure

## Overview

This technical brief defines the directory layout inside the Unreal Engine project's `Content/` folder. The structure organises all game content by type, supports the three-tier DLC system (Base / Spicy / Super Spicy), separates localization content by language and tier, and accommodates region-specific overrides.

> **⚠️ Revised 2026-07-09 — DLC tiers are now GameFeature plugins.** The original design put tier content under `_Spicy/` / `_SuperSpicy/` inside the base `Content/` folder and relied on a **cook-time `_Spicy/ → MGA/` path remap** to same-path-shadow base assets. That remap is **not a real UE mechanism** — there is no cook-time path remapping. Content tiers are now **GameFeature plugins** (`Plugins/Spicy/Content/…` at mount `/Spicy/…`), which are **additive** (they add content + systems), not same-path overrides. The base game exposes data-driven hooks the tiers fill. True same-path replacement (rare — a region swapping a specific base asset) uses **DLC release-version cooking**. See [TB — Boot Loader § Package Identity & Lifecycle](TB-boot-loader.md#package-identity--lifecycle-game-features) and [TB — CI Cook](TB-ci-cook.md). The `_Spicy/`-based sections below are **superseded** and retained only for history; the still-valid parts are the base `MGA/` tree, naming conventions, and shipping exclusions.

Jenkins handles pak splitting at cook time. During development, the base game lives under `Content/MGA/`; each tier lives in its own GameFeature plugin under `Plugins/<Tier>/Content/`.

---

## Directory Structure

```
Content/
│
├── MGA/                              ← Base game content (→ Main pak)
│   ├── Maps/
│   │   ├── MainWorld/                ← World Partition persistent level + sublevels
│   │   ├── OTC/                      ← Old Terridyn City (level transition, 40 km north)
│   │   ├── Interiors/                ← Interior sublevels (loaded on demand)
│   │   ├── Cinematics/               ← Cinematic/cutscene levels
│   │   └── Test/                     ← Dev/test maps (excluded from shipping)
│   │
│   ├── Characters/
│   │   ├── Base/                     ← Shared body mesh, skeleton, physics asset
│   │   ├── Meghan/                   ← Per-character: front hair, materials, textures, BP
│   │   ├── Tierney/
│   │   ├── NPCs/
│   │   │   ├── Named/               ← Story NPCs with unique assets
│   │   │   └── Generic/             ← Procedural/crowd NPCs
│   │   └── Enemies/
│   │       ├── Human/
│   │       └── Demon/
│   │
│   ├── Hair/                         ← Back hair library (modular, socket-swappable)
│   │
│   ├── Clothing/                     ← Clothing items (modular, per-slot)
│   │   ├── Uniforms/                 ← Magical girl uniform pieces
│   │   ├── Casual/
│   │   ├── Corporate/
│   │   └── Armour/
│   │
│   ├── Weapons/
│   │
│   ├── Vehicles/
│   │   ├── Bicycle/
│   │   ├── Motorbike/
│   │   ├── Hoverbike/
│   │   ├── Cars/
│   │   ├── Barges/
│   │   └── Mechs/                    ← HHI Loadmaster, Titan, Architect
│   │
│   ├── Environment/
│   │   ├── Architecture/
│   │   │   ├── Residential/          ← Apartment blocks, housing
│   │   │   ├── Corporate/            ← Office towers, sky bridges
│   │   │   ├── Industrial/           ← Factories, refineries, warehouses
│   │   │   ├── Civic/                ← Government Tower, memorials
│   │   │   └── Infrastructure/       ← Roads, bridges, stacked highways, monorail
│   │   ├── Props/
│   │   │   ├── Street/               ← Streetlamps, benches, signage, food carts
│   │   │   ├── Interior/             ← Furniture, appliances, decoration
│   │   │   └── Industrial/           ← Cranes, containers, dock equipment
│   │   ├── Landscape/
│   │   │   ├── Materials/            ← Landscape materials (terrain painting)
│   │   │   └── Brushes/              ← Landscape brush assets
│   │   ├── Vegetation/               ← Trees, plants, mountain vegetation
│   │   └── Water/                    ← River, lake, water materials
│   │
│   ├── Items/                        ← Inventory items, collectibles, consumables
│   │
│   ├── UI/
│   │   ├── HUD/                      ← HUD widgets, AR overlays
│   │   ├── Menus/                    ← Main menu, in-game menus, phone UI
│   │   ├── Minigames/                ← Minigame-specific UI (cooking, hacking, etc.)
│   │   ├── Icons/                    ← Item icons, skill icons, map markers
│   │   └── Fonts/
│   │
│   ├── Audio/
│   │   ├── Music/
│   │   ├── SFX/
│   │   │   ├── Combat/
│   │   │   ├── Environment/          ← River, traffic, weather, machinery
│   │   │   ├── UI/
│   │   │   └── Vehicles/
│   │   └── Ambient/                  ← Ambient loops, atmospheric audio
│   │
│   ├── VFX/
│   │   ├── Combat/                   ← Hit effects, mana effects, weapon trails
│   │   ├── Mana/                     ← Aspect-specific visual effects
│   │   ├── Environment/              ← Weather, fire, smoke, water splash
│   │   └── UI/                       ← UI particle effects
│   │
│   ├── Materials/
│   │   ├── Master/                   ← Master material definitions
│   │   ├── Functions/                ← Material functions (shared logic)
│   │   └── Shared/                   ← Shared material instances (cross-asset)
│   │
│   ├── Animations/
│   │   ├── Locomotion/               ← Walk, run, climb, swim
│   │   ├── Combat/                   ← Attack, dodge, block, grapple
│   │   ├── Social/                   ← Dialogue gestures, emotes, reactions
│   │   ├── Vehicle/                  ← Riding, mounting/dismounting
│   │   ├── Montages/                 ← Composite animation montages
│   │   └── AnimBP/                   ← Animation blueprints, blend spaces
│   │
│   ├── Blueprints/
│   │   ├── Core/                     ← GameMode, GameInstance, PlayerController
│   │   ├── Combat/                   ← Combat system, damage, targeting
│   │   ├── AI/                       ← Enemy AI, NPC behaviour, perception
│   │   ├── Interaction/              ← Dialogue, shop, crafting interfaces
│   │   ├── Minigames/                ← Per-minigame logic (9 minigames)
│   │   ├── Systems/                  ← Inventory, skills, almanac, phone, HUD
│   │   ├── Traversal/                ← Parkour, climbing, vehicle control
│   │   └── World/                    ← Streaming, weather, time of day, demographics
│   │
│   ├── Data/
│   │   ├── Tables/                   ← Data tables (items, skills, enemies, recipes)
│   │   ├── Assets/                   ← Data assets (gameplay config, balancing)
│   │   ├── Curves/                   ← Float curves (difficulty, falloff, etc.)
│   │   └── Config/                   ← Runtime configuration assets
│   │
│   ├── Input/
│   │   ├── Actions/                  ← Enhanced Input action assets
│   │   └── Contexts/                 ← Input mapping contexts
│   │
│   └── Sequences/                    ← Level sequences (cutscenes, scripted events)
│
├── _Spicy/                           ← Spicy DLC dev content
│   └── (mirrors MGA/ structure)      ← Override: same relative paths as MGA/
│                                     ← New content: unique paths within _Spicy/
│                                     ← Build pipeline remaps to MGA/ paths in Spicy pak
│
├── _SuperSpicy/                      ← Super Spicy DLC dev content
│   └── (mirrors MGA/ structure)      ← Same convention as _Spicy/
│                                     ← Build pipeline remaps to MGA/ paths in SuperSpicy pak
│
├── Localization/                     ← Base game localization
│   ├── Text/
│   │   ├── en/                       ← → LocText-en pak
│   │   ├── ja/                       ← → LocText-ja pak
│   │   └── .../
│   └── Voice/
│       ├── en/                       ← → LocVoc-en pak (dialogue audio)
│       ├── ja/                       ← → LocVoc-ja pak
│       └── .../
│
├── _SpicyLoc/                        ← Spicy-tier localization
│   ├── Text/                         ← → LocTextSpicy-<lang> paks
│   │   └── .../
│   └── Voice/                        ← → LocVocSpicy-<lang> paks
│       └── .../
│
├── _SuperSpicyLoc/                   ← Super Spicy-tier localization
│   ├── Text/                         ← → LocTextSuperSpicy-<lang> paks
│   │   └── .../
│   └── Voice/                        ← → LocVocSuperSpicy-<lang> paks
│       └── .../
│
├── RegionOverrides/                  ← → regionOverride-<region> paks
│   ├── Americas/
│   ├── Japan/
│   ├── Korea/
│   ├── Europe/
│   └── Oceania/
│
└── Developers/                       ← Per-developer sandbox (excluded from all builds)
    └── PW/
```

---

## DLC via GameFeature Plugins (supersedes "Same-Path Override")

> The original "same-path override via `_Spicy/ → MGA/` remap" model is **withdrawn** — UE has no cook-time path remapping (confirmed in the 5.8 source, 2026-07-09).

**Tiers are additive GameFeature plugins.** Each tier (`Spicy`, `SuperSpicy`, region feature packs) is a plugin at `Plugins/<Tier>/`, its content mounting at `/<Tier>/…` — a **different** path namespace from the base `/Game/…` (`MGA/`). Because the paths differ, tier content cannot *shadow* a base asset; it **adds** to it. The pattern:

1. The **base game** exposes data-driven hooks — a censored placeholder asset referenced softly, a clothing-destruction system stub, etc.
2. The **tier plugin** ships the mature variants as *new* assets under `/<Tier>/…` and, on activation, its `GameFeatureActions` wire them into the base hooks (data-registry entries, injected components, soft-reference redirects).
3. Activation is controlled by the ABM (see [TB — Boot Loader](TB-boot-loader.md)) — the plugin stays `Installed` until the ABM activates it in dependency order.

**When true same-path replacement is genuinely needed** (e.g. a region legally must swap one specific base asset), use **DLC release-version cooking** (`-CreateReleaseVersion` on the base → `-BasedOnReleaseVersion` + `-DLCName` for the override), which cooks the replacement at the *same* `/Game/…` path into its own pak and wins by mount priority. This is the *only* real same-path override mechanism; reserve it for the few assets that need it. See [TB — CI Cook](TB-ci-cook.md).

---

## Pak ↔ Directory Mapping

> **Superseded for the tier rows.** The `_Spicy/ → MGA/` and `_SuperSpicy/ → MGA/` remap entries below reflect the withdrawn model. Under the current design, `Spicy`/`SuperSpicy` cook from their **GameFeature plugin** folders (`Plugins/Spicy/Content/` → `/Spicy/…`, no remap). The `LocText-*`, `LocVoc-*`, and `regionOverride-*` rows remain broadly valid (localization = independent paks; region same-path swaps = DLC release-version cook). Authoritative cook detail lives in [TB — CI Cook](TB-ci-cook.md).

| Pak name | Source directory | Path remapping |
|----------|----------------|----------------|
| `Main` | `Content/MGA/` | None (paths as-is) |
| `Spicy` | `Content/_Spicy/` | `_Spicy/` → `MGA/` |
| `SuperSpicy` | `Content/_SuperSpicy/` | `_SuperSpicy/` → `MGA/` |
| `Demo` | `Content/MGA/` (subset) | None; cooking config includes Ch.1 + 3 side jobs only |
| `SpicyDemo` | `Content/_Spicy/` (subset) | `_Spicy/` → `MGA/`; same subset filter |
| `SuperSpicyDemo` | `Content/_SuperSpicy/` (subset) | `_SuperSpicy/` → `MGA/`; same subset filter |
| `LocText-<lang>` | `Content/Localization/Text/<lang>/` | None |
| `LocVoc-<lang>` | `Content/Localization/Voice/<lang>/` | None |
| `LocTextSpicy-<lang>` | `Content/_SpicyLoc/Text/<lang>/` | None |
| `LocVocSpicy-<lang>` | `Content/_SpicyLoc/Voice/<lang>/` | None |
| `LocTextSuperSpicy-<lang>` | `Content/_SuperSpicyLoc/Text/<lang>/` | None |
| `LocVocSuperSpicy-<lang>` | `Content/_SuperSpicyLoc/Voice/<lang>/` | None |
| `regionOverride-<region>` | `Content/RegionOverrides/<region>/` | Mirrors `MGA/` paths for overrides |

Demo builds ship **only** the Demo variant paks — `Main`, `Spicy`, and `SuperSpicy` are not included. See [Boot Manager](../gdd/boot-manager.md) for details.

---

## Asset Naming Conventions

Standard UE prefix convention for asset types:

| Prefix | Type | Example |
|--------|------|---------|
| `BP_` | Blueprint | `BP_PlayerController` |
| `SM_` | Static Mesh | `SM_StreetLamp_Crystal` |
| `SK_` | Skeletal Mesh | `SK_Body_Female` |
| `M_` | Material | `M_Master_Character` |
| `MI_` | Material Instance | `MI_Meghan_Skin` |
| `MF_` | Material Function | `MF_SlopeAngle` |
| `T_` | Texture | `T_Meghan_Hair_D` (diffuse), `_N` (normal) |
| `A_` | Animation Sequence | `A_Walk_Forward` |
| `ABP_` | Animation Blueprint | `ABP_Character_Main` |
| `AM_` | Anim Montage | `AM_Attack_Light_01` |
| `NS_` | Niagara System | `NS_ManaBlast_Fire` |
| `WBP_` | Widget Blueprint | `WBP_HUD_Main` |
| `DA_` | Data Asset | `DA_WeaponConfig_Sword` |
| `DT_` | Data Table | `DT_Items_Consumable` |
| `IA_` | Input Action | `IA_Attack` |
| `IMC_` | Input Mapping Context | `IMC_OnFoot` |
| `LS_` | Level Sequence | `LS_Ch1_Intro` |
| `SC_` | Sound Cue | `SC_Footstep_Concrete` |
| `SW_` | Sound Wave | `SW_River_Ambient_Loop` |

### Texture Suffixes

| Suffix | Channel |
|--------|---------|
| `_D` | Diffuse / Base Color |
| `_N` | Normal |
| `_R` | Roughness |
| `_M` | Metallic |
| `_AO` | Ambient Occlusion |
| `_E` | Emissive |
| `_ORM` | Packed Occlusion/Roughness/Metallic |

---

## Build Pipeline Requirements

The Jenkins build pipeline needs to:

1. **Cook `Content/MGA/`** → `Main` pak (paths unchanged)
2. **Cook `Content/_Spicy/`** → `Spicy` pak (remap `_Spicy` → `MGA` in asset paths)
3. **Cook `Content/_SuperSpicy/`** → `SuperSpicy` pak (remap `_SuperSpicy` → `MGA`)
4. **Cook localization directories** → per-language, per-tier paks (no remapping)
5. **Cook region overrides** → per-region paks (paths mirror `MGA/` for overrides)
6. **Demo builds**: apply include-list filter (Ch.1 maps + 3 side jobs + asset dependencies) to Main/Spicy/SuperSpicy cooking
7. **Exclude `Content/Developers/`** and `Content/MGA/Maps/Test/` from all shipping builds

### Path Remapping Detail

The path remap applies to cooked asset paths inside the pak file. During development, an asset at:

```
Content/_Spicy/Clothing/Casual/SM_TopA.uasset
```

is cooked into the `Spicy` pak with the path:

```
MGA/Clothing/Casual/SM_TopA.uasset
```

This matches the Base game's path for the same asset. UE's pak priority system then resolves the Spicy version first when both paks are mounted.

The same logic applies to `_SuperSpicy/` → `MGA/` for the `SuperSpicy` pak.

### Shipping Exclusions

The following directories are excluded from all cooked/shipping builds:

- `Content/Developers/` — per-developer sandbox content
- `Content/MGA/Maps/Test/` — development and test maps

These exclusions are enforced in the cooking configuration, not by directory convention alone.

---

## Editor DLC Testing

During development, DLC content lives at different paths than Base (`_Spicy/` vs `MGA/`). Two approaches for testing overrides in-editor:

- **Option A — Editor redirect utility**: An editor utility widget or console command that temporarily redirects asset lookups from `MGA/` paths to `_Spicy/` or `_SuperSpicy/` paths, simulating pak priority within the editor.

- **Option B — Cooked build testing**: Jenkins produces actual pak files with correct path remapping. Test with real pak priority in a cooked local build.

Option B is always available once the build pipeline is functional. Option A is a development convenience — implementation detail deferred to a future TB on the build pipeline.

---

## Mod Content

Mod content is **not** part of this directory structure. Mods are external pak files loaded by the MBM (Mod Boot Manager) during the session lifecycle, not during boot. Mod directory conventions are defined in [CB-mod-manager](../creative-briefs/CB-mod-manager.md).

---

## Related

- [Boot Manager](../gdd/boot-manager.md) — ABM/MBM lifecycle, pak names, search order, phased lifecycle
- [Content Architecture & DLC](../gdd/content-and-dlc.md) — content tiers, load stack, localization architecture
- [CB-mod-manager](../creative-briefs/CB-mod-manager.md) — mod pak structure and conventions
- [Save System](../gdd/save-system.md) — per-playthrough content configuration
- [Character Pipeline](../gdd/character-pipeline.md) — body mesh, hair, clothing pipeline
- [Art Direction](../gdd/art-direction.md) — rendering pipeline, material system

---

## Open Items

- Jenkins pipeline implementation (remapping scripts, cooking configs, include-list filters for Demo builds)
- Editor redirect utility design (Option A for in-editor DLC testing)
- Whether `RegionOverrides/` subdirectories mirror the full `MGA/` tree or only contain the specific assets being overridden (likely the latter — sparse override)
- Automated validation that DLC override paths match existing Base paths (catch typos in directory mirroring)
