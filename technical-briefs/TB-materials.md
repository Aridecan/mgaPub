# TB — Master Materials and Instance Naming

## Overview

This brief locks down the master material set, the features each master exposes, and the naming pattern for material instances built on them. It is the implementation map for the system described in [Art Direction](../gdd/art-direction.md) — the cel shader, the 98-entry palette, the double-index Color 1 / Color 2 model, and the four-texture (Primary / SARE / MOHW / Definition) stack.

Once this brief is followed for every authored asset, recolouring, costume variants, faction palettes, weather states, and DLC overrides are all material-instance edits. Masters are added only when a new shading model or pipeline feature is required.

All masters live under `Content/MGA/Materials/Master/`. All instances live next to the asset they belong to (per [TB-content-directory](TB-content-directory.md)) or in `Content/MGA/Materials/Shared/` if reused across asset categories.

---

## Master Materials

| Master | Cel | Outline | Palette | Textures | Special features | Used for |
|--------|:---:|:---:|:---:|:---:|---|---|
| `M_Master_Character` | ✓ | ✓ | ✓ | Pri+SARE+MOHW+Def | Subsurface scattering on SARE.R; blush via Primary B-mix; preview-only debug colour switch | Female body skin, NPC skin |
| `M_Master_Hair` | ✓ | ✓ | ✓ | Pri+SARE+MOHW+Def | Anisotropic highlight band driven by MOHW.B (highlight intensity); two-sided; dithered alpha for hair-card transitions | Front and back hair |
| `M_Master_Eye` | ✓ | ✓ | ✓ | Pri+SARE | Iris emissive boost; no MOHW or Definition — eye reads at small screen size | Eyeballs and iris discs |
| `M_Master_Cloth` | ✓ | ✓ | ✓ | Pri+SARE+MOHW+Def | No SSS; warp/weft Color 1 / Color 2 blend; MOHW.A drives wet-fabric darkening; physics-friendly (Chaos Cloth compatible) | Costumes, magical girl uniforms, vehicle interior fabric |
| `M_Master_Architecture` | ✓ | ✓ | ✓ | Pri+SARE+MOHW, Def optional | Tri-planar fallback for large slabs; outline weight reduced vs character; MOHW.A drives rain-wet surface state; works for vertical surfaces only — horizontal slabs use `M_Master_Ground` | Buildings, props, mech panels, kerbs |
| `M_Master_Foliage` | ✓ | ✓ | ✓ | Pri+SARE+MOHW | Two-sided; WPO wind animation; optional translucency for thin leaves; MOHW.A drives wet-leaf darkening | Trees, plants, mountain vegetation |
| `M_Master_Water` | — | — | ✓ (limited) | custom | Translucent + refractive; scrolling normals; foam mask; depth fade; surface domain only — does not use the standard texture stack | River, lakes, puddles |
| `M_Master_Glass` | — | ✓ silhouette only | ✓ | Pri+SARE | Translucent + fresnel rim; outline drawn from the host mesh, not the glass shell; two-sided (compiled in); MOHW skipped — glass surface props are model-driven | Windows, vehicle canopies |
| `M_Master_Decal` | ✓ instance toggle | — | ✓ | Pri only | Deferred decal domain; palette-aware; cel banding is a per-instance switch (anime panel sticker = on; dirt smear = off) | Graffiti, signage, posters, dirt, blood, lane markings |
| `M_Master_Ground` | **—** | **—** | ✓ (full palette) | Pri+SARE+MOHW | Multi-layer vertex blend; tri-planar for slopes; MOHW.A is the puddle/wet mask driving roughness blend; **no cel banding, no inverse hull** | Roads, sidewalks, plazas, alleys, courtyards |

### Why `M_Master_Ground` skips cel and outline

A flat ground surface has near-constant N·L across its area, so the 4-tone band collapses to a single band — the cel pass does zero visible work for non-zero shader cost. Inverse hull on co-planar ground meshes produces no useful silhouette (the rim faces lie in the same plane). Continuous PBR shading also reads cleaner under wetness, puddle, and decal-layered conditions, which roads need.

Vertical kerbs and the median curb-face still want cel banding — those use `MI_*` instances on `M_Master_Architecture`, not `M_Master_Ground`.

### Ground palette discipline

`M_Master_Ground` instances may draw from the **full 98-entry palette**, not a constrained environment subset. The discipline is editorial — environment artists should stay within the natural greys/browns/asphalt range — but the master does not enforce a subset.

**Why the master allows the full palette:** debug visualisation. Bright unnatural colours on otherwise-realistic ground meshes are a useful tool for marking a mesh's bounds during development (the river dams currently render bright red for exactly this purpose). Locking the master to a subset would force a per-asset master swap to enable debug colours, which defeats the purpose.

---

## Instance Naming Patterns

Three patterns by master family. The prefix is always `MI_`.

| Master family | Pattern | Examples |
|---|---|---|
| Character / Hair / Eye / Cloth | `MI_<Owner>_<Slot>` | `MI_Meghan_Skin`, `MI_Meghan_Hair_Front`, `MI_Meghan_Eye_L`, `MI_Tierney_Coat` |
| Architecture / Foliage / Glass / Decal / Water | `MI_<Set>_<Element>[_<Variant>]` | `MI_Downtown_Wall_Brick`, `MI_Downtown_Wall_Brick_Wet`, `MI_Foliage_Maple_Summer`, `MI_River_Surface_Default` |
| Ground | `MI_Ground_<Surface>[_<Tier>]` | `MI_Ground_Asphalt_L2`, `MI_Ground_Asphalt_L3`, `MI_Ground_Sidewalk_Concrete`, `MI_Ground_Plaza_Tile` |

---

## Hierarchy Depth

**Two levels only**: `M_Master_*` → `MI_*`. No intermediate `MI_Type_Base` → `MI_Asset_Specific` chains.

UE's parameter override chain already lets a single instance's parameters propagate to its dependents at runtime via dynamic material instances or per-component overrides. An extra static-asset tier mostly creates confusion about which instance carries the source-of-truth values for a parameter and complicates the DLC same-path override mechanism (see TB-content-directory).

If a parameter set is genuinely shared across many assets — for example, a faction colour scheme used by several costume instances — author it as a **Material Parameter Collection** (UE asset, prefix `MPC_`) and reference it from each instance, rather than introducing an intermediate material instance.

---

## UV Channel Convention

Every authored mesh in MGA reserves UV channels by purpose. Material functions and master materials hardcode these indices so that miswiring is impossible at the call site.

| UV channel | Purpose | Sampled by |
|---|---|---|
| **UV0** | Primary, SARE, and MOHW textures | `MF_UVsToParameters` Primary/SARE/MOHW samples, all standard PBR-style channels |
| **UV1** | Definition (inline detail) texture | `MF_UVsToParameters` Definition sample |
| UV2–UV7 | Reserved — special-purpose only (tri-planar overlays, lightmap bakes, custom mesh data) | Variant functions only (e.g. `MF_UVsToParameters_UV` taking an explicit Vector2) |

The 0/1 split exists because the Definition texture is authored on a layout independent of the diffuse mapping (the inlines need to be positioned and scaled per-line, not per-zone). Every other texture in the stack — Primary palette mask, SARE surface props, MOHW extended props — is sampled on UV0, sharing the same authoring layout.

### Channel allocation

The full per-texture channel layout (canonical reference is [Art Direction](../gdd/art-direction.md)):

| Texture | UV | R | G | B | A |
|---|---|---|---|---|---|
| Primary | 0 | Color 1 zone selector (`Round(R × MaxIndex)`, clamped 1..MaxIndex) | Color 2 zone selector (same math on G) | Mix factor | Alpha |
| SARE | 0 | Subsurface scattering | Ambient occlusion | Roughness | Emission boost |
| MOHW | 0 | Metallic | Outline weight modulator | Highlight band intensity | Wetness mask |
| Definition | 1 | Inline line data (channel use TBD) | — | — | — |

`MaxIndex` is a `MF_UVsToParameters` input that each master sets to its zone count (default 30). Primary R/G must be authored in `1/N` steps to land cleanly on zones — see [Art Direction](../gdd/art-direction.md#primary-texture--rgba) for the full encoding rule.

---

## Adding a New Master

A new master is justified only when at least one of the following is true:

- New shading model needed (e.g. cloth-shading model, hair-shading model)
- New rendering domain (decal, post-process, UI, light function)
- New pipeline feature compiled into the shader (translucency, two-sided, WPO, dithered alpha)
- A material change that cannot be expressed as parameter values on an existing master

Colour, palette assignment, zone selectors, mix ratios, blush amount, outline weight, emission boost — none of these justify a new master; they are instance parameters.

---

## Open Items (deferred from `art-direction.md`)

These remain unresolved but do not block the master list above:

- 4-tone band thresholds: globally uniform vs per-master parameter
- Signature colour palette assignments (partial)
- Definition-texture line colour: fixed vs palette-sampled
- Inverse hull weight / scaling values

---

## Related

- [Art Direction](../gdd/art-direction.md) — palette, cel shader, double-index model, Primary/SARE/MOHW/Definition texture roles
- [TB-content-directory](TB-content-directory.md) — directory structure, asset prefix conventions, texture suffixes
- [Character Pipeline](../gdd/character-pipeline.md) — body mesh, hair, clothing pipeline that feeds these masters
