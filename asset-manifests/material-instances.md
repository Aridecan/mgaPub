# Material Instances — Manifest

A list of authored material instances, grouped by their master from [TB-materials](../technical-briefs/TB-materials.md). Status column tracks whether the instance exists in-engine yet or is still on placeholder material.

Naming follows the patterns in TB-materials. New instances should be added here when they are first identified, and the status updated as they are authored.

---

## Material Functions (`MF_*`)

Shared material-function library that masters compose from. Live under `Content/MGA/Materials/Functions/`.

| Function | Inputs | Outputs | Purpose | Status |
|---|---|---|---|---|
| `MF_UVsToParameters` | `PrimaryTexture`, `SARETexture`, `MOHWTexture`, `DefinitionTexture` (all Texture2D, with placeholder fallback textures: opaque-red `1,0,0,1` for Primary, transparent-black `0,0,0,0` for the rest) | Primary: `Color1Index`, `Color2Index`, `Mix`, `Alpha` (palette indices clamped via `Round(channel × 30)` to `[1, 30]`); SARE: `SSS`, `AO`, `Roughness`, `EmissionBoost`; MOHW: `Metallic`, `OutlineWeight`, `Highlight`, `Wetness`; Definition: line value(s) | The single texture-sampling function. Samples Primary / SARE / MOHW on UV0 and Definition on UV1, then exposes every channel as a named scalar output. Replaces the earlier single-texture `MF_UVsToColorIndex` function. Each master drops one of these into its graph and wires the outputs to the shading model. | Authoring |
| `MF_SelectColorFromPalette` | `PaletteIndex` (scalar, 1-indexed) | Selected colour (Vector3 RGB) | Converts a 1-indexed palette slot (1..99) into UV coordinates over a 16×7 palette grid texture and samples the colour. Used twice per master (once each for Color 1 and Color 2 lookups), with the two outputs feeding a Lerp driven by `MF_UVsToParameters`'s Mix output. | Authoring |
| `MF_UVsToParameters_UV` | same as `MF_UVsToParameters` plus a `UV` (Vector2) override | same as `MF_UVsToParameters` | Variant that takes an explicit UV input — for special cases like tri-planar `M_Master_Ground` that don't sample on UV0. | Needed |

---

## `M_Master_Decal` — Lane markings

Flat decal-domain materials projected onto road meshes. Cel banding off (dirt-style decal). Colour drawn from palette.

| Instance | Use | Status |
|---|---|---|
| `MI_RoadMark_YellowSolid` | Single solid yellow lane line | Needed |
| `MI_RoadMark_YellowBroken` | Single broken/dashed yellow lane line | Needed |
| `MI_RoadMark_YellowDouble` | Double solid yellow lane line | Needed |
| `MI_RoadMark_WhiteSolid` | Single solid white lane line | Needed |
| `MI_RoadMark_WhiteBroken` | Single broken/dashed white lane line | Needed |

---

## `M_Master_Ground` — Road and sidewalk surfaces

Non-cel, no inverse hull. Multi-layer vertex blend + tri-planar fallback for slopes.

| Instance | Use | Replaces | Status |
|---|---|---|---|
| `MI_Ground_Asphalt_Road` | Standard road mid-segment surface | `GreyboxMat` | Placeholder |
| `MI_Ground_Asphalt_Intersection` | Intersection surface (allows future divergence — worn paint, oil staining, different wear pattern) | `GreyboxMat` | Placeholder |
| `MI_Ground_Sidewalk_Primary` | Standard sidewalk slab | `GreyboxMat` | Placeholder |
| `MI_Ground_Sidewalk_Corner` | Sidewalk corner mesh (wraps intersection corners) | `GreyboxMat` | Placeholder |
| `MI_Ground_Sidewalk_CurbCut` | Curb-cut ramp surface (wheelchair / driveway access) | `GreyboxMat` | Placeholder |

---

## `M_Master_Architecture` — Road infrastructure (vertical surfaces)

Cel-shaded; tri-planar fallback for large vertical slabs.

| Instance | Use | Replaces | Status |
|---|---|---|---|
| `MI_RoadInfra_Abutment` | Road abutment — vertical retaining / end wall structure | `GreyboxMat2` | Placeholder |

---

## Greybox legend

Placeholder materials currently in scene that this manifest is replacing:

| Greybox | Replaced by |
|---|---|
| `GreyboxMat` | `MI_Ground_Asphalt_Road`, `MI_Ground_Asphalt_Intersection`, `MI_Ground_Sidewalk_Primary`, `MI_Ground_Sidewalk_Corner`, `MI_Ground_Sidewalk_CurbCut` |
| `GreyboxMat2` | `MI_RoadInfra_Abutment` |

---

## Related

- [TB-materials](../technical-briefs/TB-materials.md) — master materials, naming patterns, hierarchy rules
- [TB-content-directory](../technical-briefs/TB-content-directory.md) — directory layout for materials and instances
- [Art Direction](../gdd/art-direction.md) — palette, cel shader, double-index model
