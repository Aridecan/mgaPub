# TB — Landscape → Mesh Terrain Migration

## Overview

Migrates the Terridyn City terrain from a UE **Landscape** (heightmap/heightfield) to **Mesh Terrain** ("Mega Mesh" / Mesh Partition) authored with the UE 5.8 **MeshTerrainMode** experimental plugin (already enabled in `OGMMGA.uproject`).

The driver is **resolution**, not aesthetics. The current Landscape is 8129 × 8129 vertices at 198.45 cm/vert. Road beds need finer height fidelity than that grid can provide, producing the diagonal **sawtooth** that the road-conformity stack documents as its visible precision floor (RT_RoadHeights 4096² = ~3.94 m/texel; see [TB-road-conformity-brush](TB-road-conformity-brush.md) → *Precision limits*). A mesh lets us **densify vertices locally** under road beds — and only there — instead of paying a uniform grid resolution everywhere or round-tripping the whole 16 km through a fixed-resolution render target.

Second motivation: a heightmap is **2.5D** (one Z per XY). A mesh is true 3D, so it supports **caves, overhangs, undercuts, and tunnels** — relevant to the off-grid/underground infrastructure, the Founders' Cut quarry walls, and any subterranean space. A heightfield simply cannot represent these.

This is a planning/architecture brief. Implementation is phased and gated on a throwaway prototype spike (Phase 0).

> **Status (2026-06-18):** planning. UE 5.8. No conversion work started. Road-conformity workflow is being **redone natively for mesh** (not baked once) — see Phase 2.

---

## The vehicle — MeshTerrainMode

UE 5.8 **Experimental** engine plugin (`Engine/Plugins/Experimental/MeshTerrainMode`). "A suite of interactive tools for creating and editing **Mesh Partitions** in the Editor." Built on MeshModelingToolset / MeshLODToolset / MeshPartition. Output is a Nanite-capable, World-Partition-aware **Mega Mesh** composed of section actors.

**Conversion is a re-import, not an in-place transform.** The `Import Heightmap` tool *"Create a new Mega Mesh from a heightmap"* (`MeshPartitionHeightmapImportTool`) takes the **same Gaea heightmap** we already use, with import options for Heightmap / Mesh / Sections / **LocationVolumes**. The LocationVolumes option should map onto the existing 8×8 volume grid (4 proxies per volume). So we re-derive the terrain as a mesh from the source heightmap rather than risking a fragile live-Landscape conversion.

### Tool inventory mapped to goals

| Goal | MeshTerrainMode tool(s) |
|---|---|
| Conform terrain to road beds + locally densify (kill sawtooth) | **Spline Modifier** + **Spline Remesh**; **Project Modifier**; **Remesh Modifier** |
| Caves / overhangs / tunnels | **Boolean Modifier** (CSG subtraction) |
| Conform to a reference surface | **Height Sculpt** ("sculpt height based on a reference surface") |
| General sculpt | Height Smooth, Height Flatten, Slope Erode, Noise, Lattice, Patch / Instanced Patch / Texture Patch, Mesh Layer |
| Section / WP management | Convert, Split, Merge, Resection, **Stitch**, Expand |
| Import | **Import Heightmap**, Create Rectangle |

Modifiers are a **non-destructive stack** (the Modifiers submode), which is a structural upgrade over the destructive Landscape edit-layer brush.

---

## What breaks (the work lives here)

The Landscape is not standalone — two systems are welded to it through the heightmap/edit-layer API, which a mesh does not have.

### 1. Road-conformity brush (the centerpiece — being redone)

`BP_RoadConformityBrush` is a `LandscapeBlueprintBrush` that writes a 16-bit packed heightmap into a `Roads` edit layer via a SceneCapture → RT_RoadHeights → blend-material pipeline ([TB-road-conformity-brush](TB-road-conformity-brush.md)). On a mesh there is **no heightmap edit layer to write into**, and the entire SceneCapture/render-target chain — the source of the sawtooth — is obsolete.

**Replacement design — spline-driven mesh conformity:**

> Drive a **Spline Modifier (+ Spline Remesh)** from the existing road centerline splines (`generate_city_grid.py`, RoadGeo spline actors). The terrain mesh conforms to, and densifies along, the actual road geometry — non-destructively, at whatever vertex density the road bed needs, with no render-target quantization. The sawtooth disappears at the source because real vertices snap to the road rather than resampling a low-res texture.

This also lets us **invert the current flow**: today RoadGeo actors sample height *from* the Landscape and drift when terrain is later sculpted; with a spline-driven modifier, one spline source can drive both the road mesh and the terrain conformity, eliminating the drift the brush existed to fix.

**Load-bearing unknown:** whether the Spline / Project modifier can be created and configured **procedurally** (from script/Python, across thousands of segments over 16 km) or is manual-per-spline in the UI. This is the scalability question and the primary target of the Phase 0 spike.

### 2. River channel geometry — water is already decoupled (NOT a break)

The water system here uses **`WaterBodyCustom`** actors ("water slugs"), not Water Body River — a deliberate choice for full manual control over placement and behaviour. Custom water bodies are placed mesh-surface actors with **no spline terrain-carving and no WaterBrush edit layer**, so "Affects Terrain" is off and the water is **fully decoupled from terrain authoring**. The water actors carry over to mesh terrain unchanged.

What remains is purely terrain geometry: the **river channel must exist in the mesh**. Import the **canyon** variant (channel baked into the source heightmap) as the Mega Mesh, then sculpt embankments/locks via Height Sculpt / Spline modifier and Boolean for undercut banks. The survey/spline scripts that shaped the channel still apply to the source heightmap; the water-body placement is independent of all of it.

(Example custom body: `PersistentLevel.WaterBodyCustom_UAID_…` under `Content/__ExternalActors__/MGA/Maps/TerridynCity2Map/`.)

### 3. Ground material

Landscape uses weightmap layer-blend. Mesh terrain uses a standard mesh material plus MeshTerrainMode's **Paint** submode. The palette/cel ground stack (`MF_UVsToParameters`, `MF_SelectColorFromPalette`; see [TB-materials](TB-materials.md)) needs a mesh-terrain variant, and the per-zone blend the weightmaps provided becomes vertex/texture paint.

### 4. PCG / building-generator height sampling

Generators that query **Landscape height** (e.g. the "few cm of terrain above road ⇒ needs a bridge" logic) must switch to **mesh line-traces**. Audit all `LandscapeHeight`/landscape-query nodes in the PCG and road/building generators.

### 5. RT geometry cost

The terrain becomes a Nanite mesh; its ray-tracing representation is the Nanite fallback / RT proxy. Given the always-resident RT lesson ([[rt-geometry-residency-city]] — 1.7 GiB pinned by procedural meshes), **measure terrain RT footprint early** in the spike rather than discovering it at scale.

---

## Phased plan

### Import source — export the *current* Landscape, not raw Gaea (decided 2026-06-18)

`UHeightmapImportTool` accepts a **heightmap file only** (`HeightmapFile`); it cannot sample a Landscape actor directly. So the source is an **export of the current Landscape heightmap**, which preserves all sculpting (river channel, locks, embankments, road-conformity edit-layer composite) — preferable to re-importing the unedited Gaea base.

Workflow: Landscape Mode → **Manage → Export Heightmap** as **16-bit** (load all WP regions first; the export stitches proxies) → feed the file to Import Heightmap.

Import params to match the existing landscape 1:1 (from the road-brush calibration constants): `MeshSize ≈ (1,613,200, 1,613,200, 152,826)` cm (8128 × 198.45 cm/side; Z −133,570→+19,256), `MeshResolution = 8129×8129` (or less for a crop), `SectionsGeneration = Automatic`. For the full run enable `bSaveAndUnload` (streams sections out as built — addresses the ~48 GB memory worry) and `bCreateLocationVolumes` (maps to the existing volume grid).

**Resolution nuance:** the export inherits the Landscape's 8129 base resolution, so it preserves macro terrain but does **not** itself sharpen road beds. The sawtooth fix comes from the spline-modifier conformity pass (local densification under roads) applied *after* import — see Phase 2.

### Import strategy — full-res single import is too slow; options (2026-06-18)

`FHeightmapImporter` (the import backend) is **C++-only** (`MESHPARTITIONEDITOR_API`, no Blueprint/Python binding) — so each import is a manual UI op unless we write a commandlet. Internally it **already auto-partitions into a section grid, builds sections on parallel threads, and finalizes each on the game thread**. The 8129² hang is therefore the *game-thread finalize + Nanite build of ~250 sections*, not missing tiling.

**Overnight result (2026-06-18→19):** the 8129² import ran **6.6 h without completing** — CPU pegged (1 core, stepping to 2), working set crept 0.82→2.81 GB (~0.3 GB/hr), never idle, never crashed. Not deadlocked, just grinding far too slowly. **Verdict: full-res single import is non-viable. Force-killed.**

**Key reframe — the base mesh does NOT need full resolution.** Matching the Landscape's 8129² grid is the mistake. This migration's whole premise is that road-bed resolution is added *locally* via the spline modifier (Phase 2), so the base import should be **coarse**: `2048²` (or even `1024²`) imports in minutes and is plenty as the base. Use full res nowhere.

**Risk — section actors are hidden from the outliner; cleanup/lifecycle is opaque (2026-06-19).** Mega Mesh section actors don't appear in the World Outliner (the tool manages them), so there's no obvious way to select/delete leftover sections by hand — a real management gap. **Mitigations:** (a) they ARE findable programmatically — `find_actors` by the MeshPartition/section class returns them regardless of outliner visibility (this is how the 3 stacked generations were caught), so MCP can always enumerate + bulk-delete orphans; (b) the World Partition window and outliner "show hidden/all" filter can surface them editor-side; (c) delete the Mega Mesh *through the MeshTerrainMode tool* (Sections submode) rather than deleting the `MeshPartition` actor by hand — the tool likely cleans up sections, manual actor-delete may orphan them (UNTESTED — verify before relying on it). Author the production workflow so terrains are deleted via the tool or scripted cleanup, never by hand.

**Gotcha — re-importing STACKS terrains, it doesn't replace (2026-06-19).** Each Import Heightmap run spawns a *new* `AMeshPartition` + its own section actors; the previous import's sections stay in the level. Doing several full-extent imports into one level left **3 overlapping terrains (UAID generations …1607/…1742/…1562) z-fighting**, which presented as fixed "leopard-spot" grey blobs that were independent of the material calc, the cache texel size, and volume reload (it looked like a material bug but wasn't). **Always import into a fresh empty level, or delete the old MeshPartition + all its section actors before re-importing.**

**Gotcha — `Save and Unload` hides the result (2026-06-19).** A correct 2048² full-size import (Size `1613200 × 1613200 × 152826`) appeared as an empty/black world because **Save and Unload was checked**: it builds sections, writes them to disk, and immediately unloads them. With **Create Volumes OFF** there are no location volumes to reload them, so the editor shows nothing — the terrain exists on disk, just unloaded. **For inspection spikes, leave Save and Unload UNCHECKED** (a coarse mesh holds fine fully loaded). Pair Save-and-Unload with Create Volumes ON only for the large production import.

Options, least-tedious first:
1. **Single import with `bSaveAndUnload=true`** (+ Explicit `SectionsResolution`, + `bCreateLocationVolumes`), possibly at 4096² rather than 8129². Saves+unloads each section as built → bounds memory, lets the full build drain. **Try first.**
2. **Coarse manual tiling** — e.g. **4×4 = 16 imports of ~2048²** (not 64×1024²). Each tile imports at origin, then is repositioned. Claude can **auto-position via MCP** (set each `AMeshPartition` transform), so tedium = the 16 file-picks only.
3. **Commandlet automation** — a small editor commandlet looping `FHeightmapImporter::Import(params)` over tiles; fully scriptable. Needs the C++/VS toolchain (ties to the session's VS 2026 setup). `FHeightmapImporter::GetSectionInfos(params)` (static) returns the section grid for given params.

**Tile-grid math** (landscape SW corner `(−806,500.79, −806,500.79)`, span ≈ 1,613,002 cm/side, from the road-brush calibration): for an N×N grid, tile size = `1,613,002 / N` cm; tile (i,j) min corner = `(−806,500.79 + i·tile, −806,500.79 + j·tile)`. 4×4 → 403,250 cm/tile. **Tiles must share a 1-vertex boundary overlap** or cracks appear at seams. Z: map PNG 0→`MeshSize.Z=152,826`, anchor actor Z so PNG 0 = `−133,570` (LandscapeMinZ) to match the original landscape.

### Phase 0 — Spike (throwaway crop) — GATE

Heightmap-import a small region as a Mega Mesh and answer the unknowns before committing the 16 km:

- Is the output Nanite? How does it section vs the 32×32 WP / 8×8 volume grid?
- What are the vertex-density controls (uniform vs local refinement)?
- **Can a Spline / Project modifier be created and configured from script/Python?** (the scalability gate)
- RT footprint + memory cost of a mesh terrain region.
- Material hookup path (does the palette/cel ground material bind cleanly?).

Decision: go / no-go and manual-vs-procedural conformity strategy.

#### Phase 0 — full-res import CONFIRMED working (2026-06-19, from build log)

A full import (explicit sections + Save-and-Unload + 8×8 volumes, fresh level) **succeeded in tens of seconds** and the build log gives hard numbers:
- **Total `134,217,728` triangles = exactly 8192²×2 → full-resolution** (NOT a coarse 2048 fallback). The earlier 6.6 h hang was purely the *Automatic-sectioning + no-unload* path; the explicit + Save-and-Unload path builds the same 134 M tris in ~tens of seconds.
- **Nanite is ON** — every section logs `NaniteBuild` with clusters. So `stat rhi` shows only the rendered Nanite subset (~37k), not source.
- **Per interior section: ~525k verts / ~813k tris / ~6,460 Nanite clusters** (edge/partial sections ~176k/273k).
- **~820 MB transient memory per section build** — this is *why* Save-and-Unload is mandatory at scale (a few hundred sections × 820 MB resident = the OOM/hang). Streaming keeps it to a handful at once.
- Read these from the Output Log via the MCP Logs toolset (`GetLogEntries` pattern `Nanite|triangles|Section`); console exec (e.g. `Nanite.Stats`) is NOT available over MCP — only cvar reads + structured tools + log reads.
- Benign `nearly zero tangents/bi-normals` warnings on flat areas — ignore.

**Memory behaviour of loaded full-detail regions (2026-06-19).** 7 downtown regions loaded at full 8192² detail: **Working Set 30.6 GB, Commit ~60 GB, on a 64 GB box (12.6 GB free).** Left idle ~16 min, **Working Set trimmed 30.6 → 9.1 GB** (free RAM recovered to 27.7 GB) in discrete GC/trim steps — confirms UE's spike-then-release. BUT **Commit stayed pinned at ~60 GB** the whole time: it's a working-set *trim* (cold pages → page file), not a *free*. **Implications:** physical headroom recovers when idle (editor stays usable), but **Commit (~8.5 GB/region) is the binding ceiling** — ~7 full-detail regions ≈ the limit on 64 GB; revisiting a trimmed region pages back from disk (hitch). Reinforces **coarse base + region-at-a-time editing** for production, not the whole city at 8192².

#### Phase 0 findings so far (2026-06-18, via MCP)

First poke — `Create Rectangle` in a throwaway level (`TerridynCityRiverMap`) produced a working Mega Mesh. Inspected over MCP:

- **Actor model:** `AMeshPartition` actor → a `UMeshPartitionComponent` ("MegaMeshEditorComponent", the editor authoring component) + a `UMeshPartitionDefinition` data asset. Built render sections are separate from the editor component.
- **Material:** driven by the **Definition** data asset, default `/MeshPartition/DataAssets/MPD_Default` (material `M_MeshPartition_Default`). The Mega Mesh uses a **material-cache / channel** system (`channelTexelSize=100` = 1 texel/m, `materialCacheTexelSize=0.25`), not landscape-style weightmap shading. **Phase 1 implication:** author a project `MPD_*` definition wrapping the cel ground material, and confirm cel shading survives the material cache (or disable caching). This is a data-asset swap — cleaner than weightmaps, but a different model.
- **RT control is per-component and granular:** `bVisibleInRayTracing` and `bRayTracingFarField` are exposed on the component → terrain RT visibility / far-field is a per-Mega-Mesh lever (ties to [[rt-geometry-residency-city]]).
- **WP/streaming is NOT automatic:** the default rectangle is `bIsSpatiallyLoaded=false`, `runtimeGrid=None` (single non-streamed actor). For the 16 km terrain, spatial loading + sectioning must be configured; how Heightmap Import + Split produce streamable sections is still open.
- **Still open (need real data, not a flat rectangle):** Nanite enablement + actual vert density / local-refinement control (lives on built sections); heightmap-import sectioning + memory; live spline-modifier smoke test.
- **Tooling note:** MCP works but the listener drops intermittently (`socket closed`); retries succeed. See [[unreal-mcp-server]].
- **Material is baked through a channel cache, not rendered direct (2026-06-19).** The Mega Mesh bakes the Definition's material into cached channel textures at `ChannelTexelSize` (default **100 ≈ 1 texel/m**; "smaller = more expensive textures"; also `MaterialCacheTexelSize=0.25`). A simple 2-tone height material cached clean, but a detailed slope/noise material (`MI_MeshLandscape`) aliased into **grey amoeba blobs over green** — fine detail undersampled at 1 texel/m. Macro layer (green flats / orange steep banks) rendered correctly. **Fixes:** lower `ChannelTexelSize` (e.g. 100→10) then **rebuild** (Save-and-Unload sections bake the cache at import and persist it — they won't update until rebuilt/re-imported), or keep viz materials macro-only. **Phase-1 implication:** the cel ground material must be authored *for* this channel-cache bake (or the cache res raised) — it can't just be dropped on as-is.
- **Import sizing — `8129×8129` in one shot is impractical (2026-06-18).** A full-res single import (~66 M verts / ~132 M tris, ~250 auto-sections) pegged one core for **60+ min without finishing** and never completed; the build appears largely single-threaded and the busy game thread blocks MCP so progress can't even be inspected. **Guidance:** prototype at **`MeshResolution` ≤ 2048×2048**, and for full-res use a **cropped region + `bSaveAndUnload` + `bCreateLocationVolumes`** so sections build and stream out incrementally rather than as one in-RAM monolith. Also note: default `MeshSize` (1024×1024×250 cm) produces a ~10 m near-flat speck — must set real extents `(1,613,200, 1,613,200, 152,826)` cm.

### Phase 1 — Material parity
Author a mesh-terrain ground material from the existing palette/cel stack; validate the Paint submode covers what the Landscape weightmaps did.

### Phase 2 — Road conformity (centerpiece)
Build spline-modifier conformity driven by road splines. Confirm the sawtooth is gone at high local density. Settle manual-per-spline vs procedural application based on Phase 0. Define the new RoadGeo ↔ terrain source-of-truth (single spline drives both).

**Spline-modifier road cross-section recipe (2026-06-19, from hands-on).** The Spline modifier shapes a road bed via a parametric cross-section:
- **Edge Falloff → Use Edge Falloff Curve** (enable) + an **Edge Falloff Curve** asset = the road **cross-section profile** (flat top = road base width; descending tail = graded shoulders/embankment). Authored ONCE, reused on every road.
- **Spline-point scale X/Y** = per-point width. Engine wiring: `bUseSplineScaleForFalloff` (default on) multiplies `FalloffDistance`/`PlateauDistance` by spline point **scale.Y**. Set scale so the curve's flat top supports the road base.
- **Spline points** = road path + grade (centerline at road height).
- **`WriteMode = Positions | Weights`** → deforms terrain AND paints a road weight channel for material.
- **Reuse model:** one shared cross-section curve + a per-tier width *scale* maps onto the 4 road tiers (L1 20 m / L2 25 m / L3 35 m / H1 40 m+). The network then reduces to: shared curve + splines from `generate_city_grid.py`/RoadGeo + per-tier scale → scriptable once the manual recipe is locked.

### Phase 3 — River channel + first cave
Import the canyon variant (channel in the mesh). Sculpt embankments/locks via Height Sculpt / Spline modifier. `WaterBodyCustom` actors are terrain-independent and need no migration work — just confirm they still sit correctly in the new channel. **First Boolean cave test** to prove the 3D-topology win.

### Phase 4 — PCG height-sampling
Swap Landscape-height queries to mesh traces across the PCG and generators.

### Phase 5 — Full conversion + validation
Full 16 km import; WP/streaming validation (memory parity vs the ~48 GB Landscape import); cook/pak exclusion check (mirror the editor-only discipline from the road-conformity brief); regression pass on roads, river, buildings.

---

## Open questions

- ~~**Procedural spline-modifier application** — scriptable, or manual-per-spline?~~ **API-confirmed scriptable (2026-06-18), pending live smoke test.** `USplineModifier` (`MeshPartitionEditor`) is a `BlueprintSpawnableComponent` exposing `BP_SetSplineComponent(USplineComponent*)`, `BP_SetAffectedMeshPartition(AMeshPartition*)` / `BP_BindToNearestMeshPartition()`, and `UpdateSplineData()`. Editor-time loop: spawn spline modifier → set spline → bind to Mega Mesh → update, driven by Python/Editor Utility over the road network. Risk: Experimental 5.8 API, may shift across point releases.
- **Heightmap import scriptable?** The `Import Heightmap` tool is an interactive tool builder; scriptability unconfirmed. Low priority — import is a one-time step, unlike per-road conformity.
- **Mega Mesh section size vs the 32×32 WP grid** — does LocationVolumes import align sections to the existing volume grid, or do we re-partition?
- **Memory** — does mesh import avoid or worsen the ~48 GB Landscape-import RAM spike?
- **Local density limits** — how fine can vertices go under road beds before section size / Nanite cluster cost bites?
- **Material** — does the existing cel ground material bind to mesh terrain unchanged, or need a dedicated master?
- **Water render on mesh terrain** — `WaterBodyCustom` placement is terrain-independent, but confirm the runtime water-info/underwater-post-process and buoyancy still resolve correctly over mesh terrain (low risk; rendering, not authoring).
- **Experimental risk** — MeshTerrainMode is Experimental in 5.8; API/asset stability across 5.8.x point releases is a project risk to track.

---

## Related

- [TB-road-conformity-brush](TB-road-conformity-brush.md) — the Landscape system being replaced; precision limits = the motivation
- [TB-landscape-river-pipeline](TB-landscape-river-pipeline.md) — Gaea import + river pipeline; canyon/canyonless variants reused here
- [TB-materials](TB-materials.md) — ground material stack to port
- [terridyn-city-grid](../../mgaPriv/world/terridyn-city-grid.md) — 32×32 WP grid the sections must respect
- `mga/reference/generate_city_grid.py` — road centerline source for the spline modifier
- MeshTerrainMode docs: https://dev.epicgames.com/community/learning/knowledge-base/nK7J/unreal-engine-introduction-to-mesh-terrain
