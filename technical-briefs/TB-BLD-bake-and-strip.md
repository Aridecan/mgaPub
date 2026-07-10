# TB — BLD Bake-and-Strip (shippable-build pipeline)

## Overview

The WorldBLD / RoadBLD / CityBLD plugins are **editor-time authoring tools** — the vendor's
intended shipping path is to **bake their generated geometry to static meshes and remove the
plugins before packaging** (they ship only Editor binaries by design; see the RoadBLD
"Debugging Compile/Packaging Issues" docs). This brief specs that pipeline for MGA:

```
1. Author   — build the world with BLD tools (plugins enabled, editor only)   [ongoing]
2. Bake     — convert generated geo actors -> StaticMesh assets + StaticMeshActors
3. Strip    — disable the BLD plugins for the packaged/cook build
4. Cook     — clean, plugin-free, shippable cook  (this becomes CI Stage 0 + a clean cook)
```

This supersedes the abandoned attempt to compile the plugins *into* the game (see
[TB — CI Cook](TB-ci-cook.md); that attempt was reverted, changes 88-92). It aligns with the
road-conformity / PCG-MeshPartition direction, which already moves toward baked mesh geometry.

> **Status (2026-07-10):** design. Nothing baked yet — MGA's world is currently **all live BLD
> actors**. Live-editor introspection (read-only) established the facts below.

---

## Bake targets

All generated geometry actors derive from a common base — **`AWorldBLDGeo`**
(`/Script/WorldBLDRuntime.WorldBLDGeo`) — which is the bake filter.

| Domain | Actor classes | Source geometry |
|--------|---------------|-----------------|
| Roads (RoadBLD) | `ARoadGeo` (⊂ `AWorldBLDGeo`); authored via `ADynamicRoad` / `ADynamicRoadNetwork`, `ARoadIsland` | dynamic mesh (`BlueprintDynamicMeshComponent`), rebuilt from `RoadControlSplineComponent` |
| City (CityBLD) | `ACityBlockGeo` (⊂ `AWorldBLDGeo`), `AModularBuildingActor`, `ADynameshActor`; authored via `ACityBlock` / `ACityParcel` | dynamesh, async via `DynameshGenerationSubsystem` |

---

## Automation — two paths, prefer A

The vendor's interactive **"Save as Mesh"** (select actors → menu action) is
`URoadBLDBakeToMeshLibrary` (+ `URoadBLDBakeToMeshSettings`) and CityBLD's
`UStaticBuildingController`, invoked from the plugins' Slate UI. **These are NOT registered
as BlueprintCallable / exec nodes** — a commandlet can't call them through normal reflection.
So:

### Path A — drive the vendor bake from an Editor Utility Blueprint *(CONFIRMED — chosen)*

**Confirmed 2026-07-10 (EUB node-palette test, O1):** the vendor bake **is** exposed as
editor-utility-callable nodes (they're editor-only, hence invisible to the runtime-actor
reflection scan). Available nodes:
- **`Bake RoadGeo To Mesh`** — input: **`TArray<ARoadGeo*>`**. The automation hook: gather all
  `ARoadGeo` in a level and pass the array (no selection needed).
- **`Bake Selected RoadGeo To Mesh`** — no inputs; operates on the editor selection (good for the
  sample test).
- **`Spawn Static Building on Parcel`** — CityBLD's static-building path.

So the tool: an **Editor Utility Blueprint** (headless-invokable via `run`/commandlet for CI)
that, after a synchronous rebuild, gathers the geo actors and calls these vendor bake nodes.
**Materials / Nanite merging are handled by the vendor's bake** (`URoadBLDBakeToMeshSettings`:
`bMergeAllIntoOneMesh`, `bReplaceSourceActors`, `savePackagePath`, `mergeSettings`) — the reason
A beats B. Path B (custom GeometryScript) is retained below only as a fallback.

### Path B — custom GeometryScript bake *(guaranteed; fallback)*

Confirmed BlueprintCallable/Python-callable in this editor: `CopyMeshToStaticMesh`,
`MakeGeometryScriptCreateNewStaticMeshAssetOptions`, `DynamicMeshActor→CopyPropertiesToStaticMesh`,
engine `MergeStaticMeshActors`/`JoinStaticMeshActors`. A commandlet / EUB:
1. **Force a synchronous high-quality rebuild** (see below).
2. For each `AWorldBLDGeo`-subclass actor: copy its dynamic mesh → `UStaticMesh` asset
   (`CopyMeshToStaticMesh`) → spawn `AStaticMeshActor` at the same transform.
3. **Replicate material merging** the vendor does — `MergeStaticMeshActors` / MeshMerge — since
   a raw copy won't proxy-bake materials.
4. Remove/hide the source BLD actors (on a preserved copy — see Reversibility).

**Verdict:** Path A if the EUB test passes (vendor handles materials); otherwise Path B with an
explicit material-merge step. Both are automatable and headless-capable.

---

## Critical ordering — rebuild before bake

Road/city geometry is generated **asynchronously**; baking without a fresh, complete rebuild
captures **stale or low-quality** meshes. Before any bake:
- Roads: `RoadBLD|RoadNetwork|RecreateRoadNetwork` + `AbortAndWaitForPendingRebuild`
  (both BlueprintCallable); honour `BuildHighQualityRoadsOnToolExit`.
- City: force `DynameshGenerationSubsystem` completion before copying.

*(Alt hook for city: `CityBLD|Parcel|SpawnStaticBuildingOnParcel` / `FStaticBuildingSpawnRules`
suggests CityBLD can spawn **static** buildings during generation — possibly cleaner than
post-hoc baking. Evaluate in the sample phase.)*

---

## Discipline — validate, and keep it reversible

- **Sample-bake first.** Bake a handful of road pieces + one intersection + one building, then
  **inspect the output**: materials intact, collision present, visual match, draw-call / tri
  count sane, Nanite as intended. Do **not** bulk-convert the world before this passes.
  - **Sample validation PASSED (2026-07-10):** baked 2 roads + intersection (36 `RoadGeo`) →
    `/Game/RoadBLD_Generated/BakedMeshes/…_Baked`: 1 merged StaticMesh, ~87k tris / ~50×40 m,
    **5 real material slots correctly sectioned**, **collision present (complex-as-simple** —
    correct for roads), Nanite off, no orphan assets. Bake fidelity confirmed → bulk automation
    green-lit. (Defaults used: `Merge All Into One Mesh` on, `Spawn Merged Actor` on,
    `Replace Source Actors` OFF, save to `/Game/RoadBLD_Generated/BakedMeshes/`.)
- **Reversible / non-destructive.** Baking the world is largely one-way. Do it on a **Perforce
  branch**, and **preserve the BLD authoring actors** (bake into a parallel sublevel or keep the
  authoring level) so roads can be re-tweaked and re-baked (e.g. after terrain re-conform).
  Never bake over the only copy of the authoring data.

---

## Strip — remove the plugins from the shipping build

Once a level is fully baked (only `AStaticMeshActor`s, no `AWorldBLDGeo`/`ADynamicRoad`/…
references), the BLD plugins are pure authoring tools with nothing to contribute at runtime:
- **Disable** `WorldBLD` / `RoadBLD` / `CityBLD` for the packaged/cook build (uproject plugin
  disable, or a shipping target that excludes them).
- Verify no cooked asset references a plugin class (the bake removes the refs; a reference
  check gates it).
- Result: the cook loads **no BLD plugins** → none of the plugin compile/link issues apply →
  clean shippable build.

---

## CI integration

This makes the [TB — CI Cook](TB-ci-cook.md) **Stage 0 procedural bake real** (it's currently a
stub): CI runs the bake commandlet/EUB (rebuild → bake → save) as a pre-cook pass over the WP
levels, then cooks **plugin-free**. The whole plugin-compilation saga disappears from CI —
the cook never sees the plugins. (Also resolves the earlier O-B PCG/MeshPartition persistence
concern in the same "bake procedural content before cook" spirit.)

---

## Open items

- ~~**O1 — EUB callability test**~~ **RESOLVED 2026-07-10 → Path A.** Vendor bake IS
  editor-utility-callable: `Bake RoadGeo To Mesh` (array), `Bake Selected RoadGeo To Mesh`
  (selection), `Spawn Static Building on Parcel`. Automate the vendor bake via an EUB.
- **O2 — Material fidelity for Path B (fallback only)** — exact merge approach to match the vendor's proxy/Nanite
  output.
- **O3 — CityBLD `SpawnStaticBuildingOnParcel`** as a cleaner static-building path vs post-hoc bake.
- **O4 — Bake granularity + draw-cost policy** — `RoadGeo` is per-cell (≈36 actors per 2 roads →
  the whole city is thousands), so bulk-bake **per-block/region groups** ("Merge All Into One
  Mesh" per group), not one city-wide mesh — for draw calls + WP streaming. Also decide the
  **LOD/Nanite policy** for the bulk output (the sample had no LODs + Nanite off; a whole city
  needs Nanite-on or generated LODs).
- **O7 — Bake placement/pivot.** Sample merged actor spawned **off-location** (shape/materials
  correct — transform-only). Diagnosed: the RoadBLD bake tool controls the pivot itself (the
  "Show Advanced Merge" panel is empty — no MeshMerge pivot option exposed); it bakes verts
  relative to one point (local bounds asymmetric, e.g. Y −2138..+38387 = geometry mostly north
  of pivot) but spawns the actor at the selection center (measured: actor at (266521, 74515, 0),
  a source RoadGeo at (266149, 57042, 0)) → offset. **RESOLVED — the merge pivot = a source `RoadGeo`'s origin.** Confirmed empirically (2026-07-10):
  setting the baked merged actor's Location = the source RoadGeo's Location aligns it **exactly**.
  So the automation just: record the anchor RoadGeo's world location (likely the first in the
  baked array — confirm which during implementation) → bake → set the merged actor's Location to
  it. Deterministic, one line. (Bounds-snap fallback and `Replace Source Actors`=ON path remain
  as alternatives but aren't needed.) Not a pipeline blocker.
- **O6 — Lane-line material remap** — baked roads use RoadBLD greybox sample materials
  (`M_Greybox_SolidYellowLine/WhiteLine`) for lane markings; remap those slots to MGA
  equivalents (ties into the re-material-to-MGA-style pipeline). Non-blocking.
- **O5 — Authoring/shipping level split** — how to keep authoring actors while shipping baked
  meshes (parallel sublevels? separate maps? a bake output folder).

---

## Related

- [TB — CI Cook](TB-ci-cook.md) — Stage 0 bake becomes this; cook goes plugin-free
- [TB — Road Conformity Brush](TB-road-conformity-brush.md) / PCG-MeshPartition — the baked-mesh direction this aligns with
- [Boot Manager](../gdd/boot-manager.md) / [Content & DLC](../gdd/content-and-dlc.md) — GameFeature DLC tiers are unaffected (separate from BLD authoring)
