# TB-landscape-river-pipeline — Landscape and River Construction Pipeline

## Overview

Documents the end-to-end workflow for creating the Terridyn City landscape and river system in Unreal Engine. This pipeline involves external terrain generation (Gaea 2), import into UE, river surveying via Python automation, and water body actor creation. The workflow is memory-intensive and time-consuming — this document exists so it can be repeated without reconstructing the process from scratch.

---

## Pipeline Summary

```
Gaea 2 (terrain generation)
  ├── Canyon variant (river channel baked in)
  └── Canyonless variant (flat terrain for water body overlay)
        │
        ▼
UE Import (Empty Open World Map)
  → Manual landscape volume creation (8×8 grid)
  → Region loading/unloading
        │
        ▼
River Sculpting (canyon variant)
  → Flatten river bottom (8000 UE units)
  → Clear side channels
        │
        ▼
River Survey (Python sensor drone script)
  → Measures bank height, width, depth every 1m
  → Outputs full river profile data
        │
        ▼
Spline Generation (Python normalizer script)
  → Reads survey data
  → Outputs spline points at 30m intervals
        │
        ▼
Water Body Creation (UE Python script)
  → Creates Water Body River actors
  → Sets spline points from normalized data
        │
        ▼
Manual Sculpting Pass
  → Landscape conformed to river
  → Beaches, docks, locks, embankments
```

---

## Step 1 — Gaea 2 Terrain Generation

**Two heightmaps are generated:**

| Variant | Purpose |
|---------|---------|
| **Canyon** | Has the river canyon baked into the terrain; used for river surveying and sculpting reference |
| **Canyonless** | Clean terrain without the canyon; used as the base landscape that water body actors overlay |

**Output size:** ~16,232 m (may need adjustment) — chosen to map as closely as possible to an even scale against the **8K UE landscape node output** (8,129 × 8,129 per node). The goal is a clean 1:1 or near-1:1 texel-to-unit mapping to avoid scaling artifacts.

> **Note:** The exact output dimension may need refinement. The target is an integer relationship with UE's 8K node size. Document the final working value here once confirmed: `___________`

---

## Step 2 — Import into Unreal Engine

1. Create a new **Empty Open World Map**
2. Import the Gaea heightmap

**Critical issue:** Importing the heightmap does **not** automatically create landscape volumes. This must be done manually.

**Memory warning:** The import process consumes approximately **48 GB of RAM**. Ensure sufficient memory before starting.

3. Create an **8×8 grid of landscape volumes** overlaying the **32×32 grid of landscape proxies** that the import creates
4. With volumes in place, **unload regions** that aren't needed for the current work phase

---

## Step 3 — River Channel Preparation (Canyon Variant)

Working with the canyon variant heightmap:

1. Load only the regions/volumes containing the river
2. Use the **Flatten tool** set to **8,000 UE units** to level the river bottom
   - This ensures a minimum river width of **80 m** across the channel
3. Flatten out any **side channels** that would divert water flow

---

## Step 4 — River Survey (Python Sensor Drone)

A Python script simulates a sensor drone that flies down the river channel and takes measurements:

**Measurements at each point:**
- **Bank height:** Where the slope exceeds 10 degrees from horizontal (defines the top of the river bank)
- **River width:** Distance between left and right bank tops
- **River depth:** Depth at the measurement point

**Process:**
1. Start at the river's upstream entry point
2. Take measurements (bank height, width, depth)
3. Move **1 metre** downstream
4. **Re-centre** on the river channel
5. Repeat

**Runtime:** Several hours for the full river course.

**Output:** Complete river profile dataset — position, bank heights, width, and depth at 1m resolution along the entire course.

---

## Step 5 — Spline Normalization (Python Script)

A second Python script processes the raw survey data:

1. Reads the 1m-resolution survey output
2. **Normalizes** the data (smoothing, outlier handling)
3. Outputs **spline control points at 30m intervals** in a format readable by the UE script

---

## Step 6 — Water Body Actor Creation (UE Python Script)

A third script runs inside Unreal Engine:

1. Reads the normalized spline data from Step 5
2. Creates **Water Body River actors**
3. Sets up spline points on each actor from the data

**Result:** A rough but correctly positioned river through the landscape. The water bodies follow the surveyed channel with appropriate width and depth variation.

**Critical post-creation steps (must be done before proceeding):**

1. **Disable "Affects Terrain"** on every Water Body River spline actor
2. **Delete the landscape edit layer/brush associated with water** (the WaterBrush landscape edit layer that UE creates automatically)

> **Why this matters:** If these two steps are skipped, World Partition loading breaks. All landscape proxies and all water splines will remain loaded at all times instead of streaming in/out by cell. This **explodes memory usage in the editor** and defeats the purpose of World Partition. These steps must be completed before any sculpting work begins.

---

## Step 7 — Manual Sculpting Pass

With the water body actors in place on the **canyonless** base terrain:

1. Import the canyonless heightmap into a **new Open World Map**
2. Apply the water body actors from Step 6
3. **Sculpt the landscape** to conform to the river — the water body actors define where the river is; the terrain needs to be shaped to match
4. Add detail features:
   - **Beaches** (Memorial Park sculpted beach, recreational areas)
   - **Docks** (TMC docks, Vastelion docks, general cargo)
   - **Lock complexes** (6 lock/dam/weir structures along the river)
   - **Embankment walls** (stone and concrete through downtown core)
   - **Bridge abutments**

---

## Scripts Reference

| Script | Language | Runs In | Purpose |
|--------|----------|---------|---------|
| River survey (sensor drone) | Python | Unreal Engine | Flies river channel, measures bank/width/depth at 1m intervals |
| Spline normalizer | Python | Standalone | Converts 1m survey data to 30m spline points |
| Water body creator | Python | Unreal Engine | Creates Water Body River actors and sets spline points |

> **TODO:** Record script locations and filenames once finalized.

---

## Known Issues and Gotchas

- Heightmap import does **not** create landscape volumes — must be done manually after import
- ~48 GB memory required for import — close other applications
- The 8×8 volume grid must align with the 32×32 proxy grid (4 proxies per volume)
- Flatten tool value of 8,000 units is critical — determines minimum river width
- Sensor drone script takes several hours — run overnight or during other work
- The canyon and canyonless variants must be generated from the **same Gaea project** with identical parameters except the canyon feature, or the terrain won't align

---

## Open Questions

- Exact Gaea output dimension (currently ~16,232 m — confirm final value)
- Script file locations and version control
- ~~Whether the sensor drone runs as a standalone Python process or as a UE Editor Utility Widget~~ (resolved — runs inside UE; normalizer runs standalone)
- Optimal spline interval (currently 30m — may need adjustment based on river curvature)
- Process for regenerating only portions of the river if changes are needed
