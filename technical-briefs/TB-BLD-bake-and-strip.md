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

### Path A — drive the vendor bake from an Editor Utility Blueprint *(best fidelity; test first)*

The one unknown: the bake function *may* be a `CallInEditor` UFUNCTION reachable only from an
**Editor Utility Blueprint** graph (editor-only nodes are filtered out of runtime-actor
graphs, so the reflection scan couldn't see it; the RoadBLD/CityBLD C++ headers aren't shipped
to confirm). **Cheap deciding test:** create a throwaway Editor Utility Blueprint and search
its node palette for `RoadBLDBakeToMesh` / `StaticBuildingController`. If present:
- An EUB selects all target actors → calls the vendor bake with `URoadBLDBakeToMeshSettings`
  (`bMergeAllIntoOneMesh`, `bReplaceSourceActors`, `savePackagePath`, `mergeSettings` incl.
  Nanite). **Materials/merging are handled by the vendor** — this is why A is preferred.
- The EUB is invokable headlessly (`run` an editor utility / commandlet), so it slots into CI.

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

- **O1 — EUB callability test (decides Path A vs B).** Create a throwaway Editor Utility
  Blueprint; check whether `RoadBLDBakeToMeshLibrary` / `StaticBuildingController` bake nodes
  appear. Highest-priority next step.
- **O2 — Material fidelity for Path B** — exact merge approach to match the vendor's proxy/Nanite
  output.
- **O3 — CityBLD `SpawnStaticBuildingOnParcel`** as a cleaner static-building path vs post-hoc bake.
- **O4 — Bake granularity** — per-actor vs merged-per-block/region (draw calls vs streaming/edit
  granularity); interacts with World Partition + the block/grid structure.
- **O5 — Authoring/shipping level split** — how to keep authoring actors while shipping baked
  meshes (parallel sublevels? separate maps? a bake output folder).

---

## Related

- [TB — CI Cook](TB-ci-cook.md) — Stage 0 bake becomes this; cook goes plugin-free
- [TB — Road Conformity Brush](TB-road-conformity-brush.md) / PCG-MeshPartition — the baked-mesh direction this aligns with
- [Boot Manager](../gdd/boot-manager.md) / [Content & DLC](../gdd/content-and-dlc.md) — GameFeature DLC tiers are unaffected (separate from BLD authoring)
