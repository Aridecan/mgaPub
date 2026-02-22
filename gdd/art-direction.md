# MGA — Art Direction

---

## Visual Style

MGA uses an anime/manga aesthetic throughout. Meshes are authored to anime conventions — clean topology, stylised proportions, and geometry that reads well under cel shading. The rendering pipeline is built to reinforce this style at every layer: palette-controlled colour, banded lighting, and drawn outlines, all without relying on post-processing.

**Guiding principle:** The look is achieved in the render pass, not after it. Post-processing is avoided where a geometry or shader solution exists. This keeps overhead low and the frame budget predictable.

---

## Cel Shader — 4-Tone Banded Lighting

Lighting uses a 4-tone cel shader. Diffuse contribution from the primary light source is evaluated against each surface normal and quantised into four discrete bands rather than a smooth gradient.

| Band | Role |
|------|------|
| Highlight | Specular peak; anime-style bright rim |
| Base | The character's intended read colour under normal lighting |
| Shadow | First shadow band; mid-tone drop |
| Deep shadow | Darkest band; used in occlusion and extreme angles |

Bands are determined by the angle between the surface normal and the primary light direction. Band thresholds are tunable per material to allow different materials (skin, fabric, metal) to respond differently to the same light.

---

## Colour Palette

All colour in the game is drawn from a single 64-entry palette. No material uses colour values outside the palette.

| Category | Count | Purpose |
|----------|-------|---------|
| Flesh tones | 8 | Skin across the full character roster |
| Greyscale | 9 | Shadows, metals, neutral elements, UI |
| Signature colours | 6 | Character and faction identity colours |
| Blush tones | 12 | Anime blush, flush, and emotional colour states |
| General | 29 | Clothing, environment, props, secondary colours |
| **Total** | **64** | |

**Palette discipline:** The restricted palette enforces visual consistency across the game. Characters, environments, and UI all speak the same colour language. New assets that require a new colour require a palette discussion — the palette is not expanded casually.

**Blush tones** are designed to blend with flesh tones through the material B channel (see below), creating the anime blush effect as a material parameter rather than a separate render pass.

**Signature colours** correspond to the key characters and factions whose colours appear repeatedly across costume, UI, and environmental storytelling.

---

## Material System — Double-Index Palette

Textures in MGA are not used as direct colour maps. Instead they act as a double index into the palette, with colour selection and blending controlled per material instance. This separates the authored mesh from its colour — a single asset can be recoloured entirely by changing material instance parameters, with no texture repaint required.

### Material Instance Parameters

Each material instance exposes two arrays of palette index slots:

| Parameter | Count | Maps to |
|-----------|-------|---------|
| Color 1 indexes | 30 | Palette entries (1–64) |
| Color 2 indexes | 30 | Palette entries (1–64) |

The 30 Color 1 slots and 30 Color 2 slots define the colour vocabulary available to that material instance. Which slot applies to any given pixel is determined by the primary texture.

### Primary Texture — RGBA

| Channel | Role |
|---------|------|
| **R** | Selects which Color 1 slot (1–30) applies to this pixel region |
| **G** | Selects which Color 2 slot (1–30) applies to this pixel region |
| **B** | Mix ratio — lerp between the selected Color 1 and Color 2 |
| **A** | Alpha |

The R and G channels divide the mesh surface into up to 30 independently addressable colour zones each. The B channel blends between the two selected palette entries, allowing two-tone materials: a fabric with warp and weft colours, skin with a blush layer, hair with a highlight band. Changing the look of any zone is a material instance parameter edit, not a texture repaint.

### Secondary Texture — Surface Properties

A second texture carries surface property data in packed channels:

| Channel | Role |
|---------|------|
| **R** | Subsurface scattering mask / intensity |
| **G** | Ambient occlusion |
| **B** | Roughness |
| **A** | Emission boost |

This texture is authored once per mesh and does not change with colour variations. Subsurface scattering on the R channel is the primary driver of skin warmth and the soft look of flesh in lit areas.

### Definition Texture — In-Line Detail

A third texture is applied using a secondary UV set (beta UV), independent of the primary UV layout. Its role is interior line definition: the fold lines, anatomical detail, fabric seams, and crease marks that give the anime aesthetic its drawn quality.

Because beta UV is independent, definition lines can be positioned and scaled separately from the diffuse mapping. This texture does not interact with the palette system — it carries drawn line data directly.

---

## Outlines

Character and object silhouettes use the **inverse hull** technique: a second render of the mesh at a slightly increased scale, with inverted normals, drawn in the outline colour. The front faces of the hull are culled; only the rim beyond the original silhouette is visible.

**Why inverse hull over post-process edge detection:**
Post-processing approaches for outlines (Sobel edge detection, depth/normal edge passes) introduce overhead that scales with resolution and scene complexity. Inverse hull is a geometry solution — its cost is proportional to mesh complexity, predictable, and keeps the outline entirely within the render pass. Post-processing is not used for outlines.

Interior lines — anatomy, clothing folds, seam detail — are handled by the definition texture and beta UV, not by any outline pass. The combination of inverse hull silhouette and definition texture interior lines produces the full anime line aesthetic without post-process cost.

**Outline colour and weight** are material parameters, allowing different outline treatments per character, costume, or environmental asset class.

---

## Asset Pipeline Summary

| Asset type | Textures | Notes |
|------------|----------|-------|
| Character mesh | Primary (RGBA) + Secondary (RGBA) + Definition (beta UV) | 3 textures per mesh; colour via material instance |
| Costume variant | Material instance only | No additional texture if zones match existing mesh |
| Environment asset | Primary + Secondary; Definition where needed | Same system; fewer colour zones typically required |

**The key efficiency:** colour variation is a material instance change. A costume in a different colour, an NPC skin tone variant, a faction-coloured prop — none of these require new textures. The palette and the double-index system make variation cheap.

---

## Asset Sourcing and Workflow

MGA does not model every asset from scratch. The practical workflow uses existing 3D assets as a starting point, replacing their materials and textures to bring them into the visual system.

### Asset Sources

- **Humble Bundle purchases** — commercial 3D assets acquired through bundle deals
- **Epic Games Store free assets** — assets distributed free through the Unreal Engine marketplace

These assets arrive with their own materials and textures, which are discarded. Only the geometry is retained.

### Conversion Process

1. **Import** — Asset imported into Unreal Engine with Nanite compatibility enabled as a standard step
2. **Material replacement** — Original materials stripped; custom double-index palette material applied
3. **Texture authoring** — Primary RGBA texture, secondary surface properties texture, and definition texture (where needed) authored for the mesh using the beta UV layout
4. **Palette assignment** — Material instance parameters set to assign the appropriate Color 1 and Color 2 palette slots for the asset's role and context

The result is an asset that looks native to MGA's visual language despite not being modelled for it. The palette discipline enforces visual consistency: an asset is only finished when it speaks the same colour vocabulary as the rest of the game.

### Why This Works

The anime/manga aesthetic is achieved at the material and shader layer, not the modelling layer. A mesh with correct topology reads correctly under the cel shader and inverse hull outline regardless of where it originated. The primary constraint is that the geometry is clean enough to outline well — heavily triangulated or noisy meshes may require cleanup before the inverse hull solution reads cleanly.

---

## Open Items

- Inverse hull weight / scaling values — to be calibrated during shader development
- Whether the 4-tone band thresholds are globally uniform or exposed as per-material parameters
- Signature colour assignments — partially established through character identity; to be formally locked into the palette
- Environment palette usage — what subset of the 64 entries is available to environmental assets vs characters
- Whether the definition texture uses a fixed line colour or samples from the palette
