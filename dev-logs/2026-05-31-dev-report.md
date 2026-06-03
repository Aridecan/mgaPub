# MGA Dev Report — May 2026

*Ordinary Girl Meghan / Magical Girl Ava*

---

## The Month of the Road

The April report ended with "the river is done as a system" and a closing line about needing the city to start existing in geometry. May was the month of that geometry showing up — but only after a two-week detour into the deepest piece of terrain technology the project has needed so far. By the end of May the first four rows of the downtown grid are laid out in the engine, and the tooling that gets them there is doing real work.

---

## The Road Conformity Brush

The river infrastructure of April left a problem: the roads needed to follow the terrain, and the terrain needed to follow the roads. UE's stock landscape brushes don't deform terrain under arbitrary road geometry — they smooth, raise, lower, but they don't say "match the Z of whatever StaticMesh is sitting above this texel." So I built one.

The brush is a Landscape Blueprint Brush that, on user trigger, does the following:

1. **Scene-captures** the road meshes from directly above the landscape, using a 4096² R32F render target.
2. **Encodes** each captured texel's world-space Z into an unsigned 16-bit value via a custom post-process material (`M_RoadHeightEncode`).
3. **Reads** that encoded heightmap back during the landscape's `Render` call, decodes it, and blends it into the landscape height texture.

That description hides about a hundred hours of debugging. The interesting parts:

**Per-actor material override.** The captured roads need to draw with their height-encoding material, not their normal road surface. The brush iterates RoadGeo actors, swaps each one's materials to the encoding material, captures, then swaps them back. This loop wouldn't apply at first because RoadGeo had been refactored from an InstancedStaticMeshComponent root to a plain StaticMeshComponent, breaking the class filter. SMC is the ISMC parent, so filtering on SMC catches both — fix one. Writing to `Override Materials` directly didn't propagate to the render thread either; had to switch to the `SetMaterial(index, mat)` API. And the `SavedMaterials` array used to restore originals was function-local, so writes weren't surviving the loop — promoted to a class variable. Three independent bugs in the same loop, all subtle, all required for the override pipeline to do anything at all.

**Masked dilation for AA recovery.** When the road mesh rasterises at 4096² resolution, the edge texels get partial coverage and produce fractional Z values that decode as terrain ~140 m deep along the road's edges. A literal trenching artefact. The fix is **masked dilation**: a cardinal-cross sampling chain at distances 1/2/3/4 texels (17 samples total), taking the max, but masked so the dilation only fills inside the original road footprint. The dilated value extends the road's correct Z value across the partial-coverage edge texels without growing the footprint outward. Trenching eliminated.

**Calibration to ~0.05% drift.** End-to-end calibration found two small math errors compounding into ~0.5% scale drift across the landscape — BrushSize fudges of 1.00651 in X and 1.0042 in Y killed them. Constant offsets of (1200, 2800) cm aligned the brush's coordinate system to the road geometry exactly. A ZBias of −40 cm pushes terrain just below the road meshes for visual clearance. A MaxLandscapeBelow rejection threshold (= 10 m in 16-bit encoding) screens out single-pixel Z drops from diagonal-segment sawtooth.

**Substrate gotchas.** UE 5.7's Substrate mode broke two things: (1) post-process material output gets clamped to LDR even on HDR capture targets — workaround via per-actor material override, (2) Substrate slab Coverage pin produces fractional alpha even in Opaque blend mode — leave Coverage disconnected for hard edges. Both got documented in the project's UE notes.

The brush is now stable, runtime-tunable (TexelSearchSize parameter 0..4 for dilation reach), and produces terrain that conforms to road geometry within rasterisation quantisation (~4 m per texel at 4096²). The technical brief `TB-road-conformity-brush` was rewritten end-to-end to match the actual implementation.

---

## The Materials Lockdown

In parallel with the brush work, the material system got formalised. Ten master materials (Character, Hair, Eye, Cloth, Architecture, Foliage, Water, Glass, Decal, Ground), a 4-texture sampling stack (Primary / SARE / MOHW / Definition), and a per-master MaxIndex parameter that controls how many palette zones each material can address. Ground uses 2 zones (cel-shaded road / sidewalk colour). Characters use 30 zones (most palette flexibility). The convention is now a technical brief in `mgaPub/technical-briefs/TB-materials.md` and an asset manifest tracks every material instance.

Palette slots got nailed down for the road infrastructure too: asphalt MIs (road, intersection, sidewalk, corner, curb-cut) pull C1 from slot 85 (asphalt-tier grey, 0.25 brightness). Architecture (road abutment) pulls C1 from slot 86 (concrete-tier, 0.12 brightness). These five Ground MIs and one Architecture MI replace the earlier `GreyboxMat` / `GreyboxMat2` placeholders that were sitting on every block in the city.

---

## The Road Export Pipeline

With the brush working and the materials assigned, the question became: where do the roads actually go? The grid spec exists on paper — 30 × 36 superblocks, 4×4-blocks-per-superblock, 3×3-superblocks-per-megablock, 10° sightline offset at every superblock boundary — but laying it out by hand in UE would take weeks of clicking on (X, Y) inputs.

So that became a Python tool.

`reference/organize_roads.py` reads the procedurally generated `junctions.json` and emits two outputs:

1. **`roads_organized.txt`** — a paste-ready text reference organised by superblock. Each superblock lists its 4 N-S roads and 4 E-W roads with every intersection point in paste-ready UE coordinate format `(X=…,Y=…,Z=5000.00)`. 1,080 superblocks, 8,640 road segments, 81,000 lines.
2. **`roads_organized.json`** — a flat lookup table keyed by short street-pair name (`"11Wx5S"`, `"MERIDIANxMAIN"`, etc.) so other tools can find an intersection's coordinates without parsing text. 17,347 named intersections.

Each road segment carries an **overhang endpoint** 100 m past each end on N-S roads and 60 m on E-W roads, where adjacent superblocks join. The overhang exists on straight stretches of road so the importer can stitch separate dynamic road networks together cleanly even though the road kinks at the next junction.

### The Median Shift

The first version of the pipeline emitted coordinates on the *physical centreline* of each road. The UE road importer wants coordinates on the **boundary between the left-hand-lanes and right-hand-lanes arrays** — and the median lives inside the left-hand array, so the centreline is offset by half the median width from where the importer wants the polyline. L2 median half: 1.5 m. L3 median half: 2.0 m.

Two complications:

- Roads can be travelled in either direction, and "right of forward" flips depending on which way. So every L2/L3 road needs two coordinate variants — Northbound (or Eastbound) and Southbound (or Westbound) — and the author picks the one matching each road's lane-array convention.
- The boundary roads kink at every L2/L3 junction (10° alternating offsets). At the kink, the offset polyline does *not* land at "centreline + perpendicular shift" — it lands at the proper polyline-offset intersection point, slightly displaced.

Both got built into the generator. L2/L3 boundary roads now emit Northbound + Southbound variants per superblock; L1 interior roads stay on centreline; junctions on the shifted polylines use intersection-of-shifted-lines for the correct kink position.

### Corner Curves

The grid is mostly orthogonal, but where it isn't — where the river cuts blocks off, where a lock structure interrupts the grid, where steep terrain forces a bend — the road needs a smooth curve, not a hard 90° angle. The `corner_curve.py` CLI tool takes a corner spec and produces the curve geometry:

```bash
corner_curve.py 11Wx5S 12Wx5S 11Wx6S 20
```

The four arguments are: intersection name, point on road A, point on road B, radius in metres. The script reads the JSON, computes the fillet, and prints the curve start, curve end, and (optionally) arc centre in paste-ready UE format.

It handles three cases the easy fillet formula doesn't: (1) non-90° corners — the 10° sb-row offset produces 80° and 100° corners depending on which side of the kink you're on, with setbacks of 23.84 m and 16.78 m respectively for a 20 m radius. (2) Shifted-lane corners — `:N` / `:S` / `:E` / `:W` modifiers on the street names tell the script to compute the corner on the *shifted* polylines for L2/L3 roads. (3) Lines that are parallel offsets of segment-direction-at-intersection rather than chord-direction, which matters when the two endpoints are several sb-rows apart and the road kinks in between.

The two scripts plus the JSON lookup file made the rest of May possible.

---

## Downtown Layout — Locked In

With the pipeline producing reliable coordinates, the downtown's design could move from "described in prose" to "specified to centimetre precision." The L3-bounded downtown core got fully nailed down:

**Bounds.** West: 24th Street West. East: 24th Street East. South: 24th Avenue South. North: 24th Avenue North. 12 × 12 superblocks. 4 × 4 megablocks. 144 superblocks total. Roughly 4.9 km × 6.1 km centred on the world origin.

**Block addressing.** A new `db<col><row>` system gives every block in the downtown a unique internal address. Column 1 = west, column 48 = east. Row A = north, row AV = south (Z then AA–AV). 2,304 blocks total. Internal use only — in-game references continue using the legacy letter-grid M–T / 13–20 system, which now sits as a parallel coordinate system in `terridyn-city-grid.md`.

**River blocks.** A block is **river-class** (non-buildable) if a full L1+-tier street bounding box can't be drawn around all four sides. The classification falls out of road work automatically: any block whose bounding street is interrupted by water is a river block. First 22 river blocks identified — 10 in Pool 8 south of Lock 4, 6 on the Lock 4 footprint itself, 6 in Pool 6 running through Central Park's west edge.

**Central Park.** Bounded by 4S (north), 12S (south), 4W (east), and the river (west). The west edge tapers from ~9.5 W at the north (4S) to 7 W at the south (12 S) as the river drifts. Roughly 33 full blocks + 5 partial blocks. Main entrance at 8S × 4W with `db20AF` and `db20AG` reserved as **two full parking blocks**. Main bus stop on `db20AJ` at the SE corner along 12S. Secondary entrance at 4S × 8W. Secondary bus stop on 4S just east of the secondary entrance.

**Lock 4 placement.** East edge of the 280 m × 170 m staircase sits exactly on **7th Street West**, just north of 12 S. Occupies sb-col −2 across rows AJ + AI + a sliver of AH, taking 60% of col 16 and all of col 17 in those rows. The view from Central Park looks south over the cascade — by design, the lock and the river are visible from the park's south edge, communicating the civic-meets-industry theme without dialogue.

**The 12 S drawbridge.** Major river crossing along 12 S. Approach ramps rise from 5 W on the east and 10 W on the west — about 500 m of elevated structure spanning five streets, with a drawbridge mechanism over the navigable channel at the col-17 river crossing. Cross streets pass under the elevated structure at grade.

---

## Worldbuilding — The Things That Aren't Roads

Three substantial threads got nailed down in late May.

### National Service & Tertiary Ages

The galactic-default 3-year national service (men drafted, women socially-pressured public service) interacts with Terridyn's no-draft variant to produce a distinctive age distribution at MCI and TMS. Alliance-system immigrants arrive at 22–24 because they served. Terridyn locals arrive at 18–20 because they didn't. **Meghan arrives at 19** because she holds an extraordinary-circumstances waiver (the reasons are character-file territory). She's local-typical age and immigrant-typical origin — a structurally unusual combination that anyone clocking both will notice.

Celeste's older brother got corrected from "survived conscription" to "completed a three-year volunteer tour with the Terridyn System Defense Force" — TSDF being the local volunteer option that some Terridyn-natives take despite there being no draft. Pirates occasionally probe the system; TMC, Hitsunami, and Vastelion's combined corporate-defence weight makes it a "specific brand of stupid" to actually invade.

### The Diplomatic Passport

A substantial pass landed on the lore around Meghan's diplomatic passport — flagged as a long-running plot thread in earlier reports. The specifics are spoiler-heavy and stay out of the public dev log, but the work locked down several points of plot and backstory: the credential's exact nature, the prologue customs sequence, who on Terridyn knows what about it, where it sits during the game, and how it interacts with other characters' awareness. It's now coherent enough to be referenced consistently in design work without re-deriving from scratch each time.

### The Alliance's Diplomatic Corps

In support of the passport work, the Alliance's diplomatic structure got formalised. Senior Ambassadors run on-site missions; Junior Ambassadors hold specific portfolios (Trade, Defence, Cultural Exchange, etc.) and operate independently in those portfolios. When no Senior Ambassador is in-region, a Junior Ambassador's portfolio implicitly expands to *everything* — no title bump, but the responsibilities default to them by absence.

The Alliance has no mission on Terridyn. There's a small consulate on the solar-orbiting space colony (visas and lost-passport replacement, no resident ambassador, no surveillance brief) and that's it — distinct from the space-elevator anchor station, which doesn't have an Alliance presence at all. Terridyn sits in ungoverned space, is corporate-run, and isn't strategically prioritised by Alliance foreign service — which is structurally important to a number of downstream things.

---

## City Blockout Begins

The combination of the brush, the export pipeline, and the locked-down downtown layout means that for the first time in the project's history, **rows of the city are actually being placed in the engine**. As of end-of-month, **4 of 13 N-S city rows** are laid down — the southern strip from 24S up through the superblocks containing 12 S.

That's the southernmost megablock-row of the downtown core fully road-networked: from the south L3 boundary at 24 S, up through 23 S / 22 S / 21 S (the three L1 interior rows of the south sb-row), through the 20 S boundary, through the next sb-row's three L1 interiors, and on through the 12 S boundary. The next pass continues north into the rows containing the Pool 8 river path that south of Lock 4.

The pace from here depends on how much manual sculpting the river-adjacent rows need (the road-conformity brush handles flat terrain cleanly, but bends around river blocks and the lock footprint may require per-corner adjustments).

---

## By the Numbers

- **2** Python scripts written (~900 lines combined)
- **17,347** named intersections in the JSON lookup
- **81,022** lines in `roads_organized.txt` (1,080 superblocks × ~75 lines avg)
- **8,640** road segments emitted
- **2,304** addressable downtown blocks under the new `db<col><row>` system
- **22** river/lock blocks identified (Pool 8, Lock 4 footprint, Pool 6 along Central Park's west edge)
- **33** full Central Park blocks + 5 partial (river-shared)
- **4** of 13 city N-S rows laid in-engine — the southern strip from 24 S to 12 S
- **0.05%** end-to-end terrain conformity drift achieved
- **4096²** R32F render target precision floor for the conformity brush
- **3** Substrate-specific gotchas documented (post-process LDR clamp, Coverage pin, per-actor override pattern)
- **3** independent bugs found and fixed in a single per-actor material override loop
- **10** master materials locked down, 4-texture stack
- **2** new technical briefs (`TB-road-conformity-brush`, `TB-materials`)
- **1** corrected sibling backstory (Celeste's brother → TSDF volunteer, not conscript)

---

## What's Next

- **City blockout continues.** Rows 5 through 13 of the N-S extent. Pace is bounded by how much river-adjacent manual work the Pool 8 path demands.
- **Lock 4 model.** Blockout placed; detailed modelling of the two-step staircase chambers, gates, and side channels still ahead. Now that the placement is locked at 7 W (east edge), the surrounding geometry has a stable anchor.
- **Central Park geometry.** With bounds and parking blocks fixed, the actual park-volume sculpting can begin. The west bank treatment along Pool 6 is the first piece — the river's east bank drifts from ~9.5 W at the north of the park to 7 W at the south, and the sculpting needs to follow that gentle curve smoothly.
- **12 S Drawbridge geometry.** ~500 m of elevated structure, drawbridge mechanism centred on the navigable channel. Mostly approach-ramp meshes; the actual movable section is a smaller piece of the visible whole.
- **Enemy AI prototyping.** Carried forward yet again — but with road geometry actually arriving in the engine, the wait is finally getting shorter.

The river is done. The roads are arriving. The downtown is starting to look like a place.

---

*MGA is a solo-developed adult action RPG currently in pre-production. Follow development at the [Project Tangent SubscribeStar](https://subscribestar.adult/aridecan).*
