# MGA Dev Report — April 2026

*Ordinary Girl Meghan / Magical Girl Ava*

---

## The Month of the River

The last report ended with a first-pass landscape in the engine and a list of manual sculpting work ahead. April turned into something I didn't expect: a full month on the river and its infrastructure. What started as "place the water bodies and sculpt the banks" became a deep engineering exercise in dam generation, lock design, pipeline debugging, and terrain tuning. The river is now the most specified piece of infrastructure in the entire project.

---

## The Dam Mesh Generator

The Water Body River actors from UE's water plugin didn't cooperate. The water mesh bled through the dam walls regardless of collision settings — the water system generates its mesh from the spline, not from surrounding geometry. Rather than fight the system, I built a **Blender-based dam mesh generator** that reads the river spline data and produces dam wall meshes with correct profiles.

The generator produces:

- **Trapezoidal dam walls** — 2m thick at the top, 6m at the base, with a concave inner bevel at the channel floor. The bevel took two iterations to get right (it was initially bulging outward instead of curving inward)
- **Edge posts** every 10m along both outer top edges — visual guides for placing the dams in UE
- **Width-reference posts** at a fixed 80m span, showing the minimum riverbed width target
- **FBX export** per spline segment, positioned in absolute UE coordinates so they drop in at (0,0,0)

The bevel profile, post spacing, and dam dimensions are all configurable at the top of the script. The whole thing runs in about 30 seconds and produces 37 dam segments.

---

## Pipeline Fixes

The river processing pipeline got several significant corrections this month.

**Depth mismatches at spline boundaries.** The original `river_process.py` computed hydraulics (depth, velocity) per spline segment. Each segment had its own average width, its own flow rate, its own depth calculation. The same physical river point at a spline boundary got two different depths depending on which segment's average it was computed from. The fix: compute hydraulics once per **pool** on the full unsplit point list, then split into splines. Overlap points now carry identical depth values because they're the same dict object.

**World Partition cell size.** Was hardcoded at 50,000 cm (500m). The actual landscape is 8,129 pixels × 198.45 cm/pixel = 1,613,200 cm, divided by 32 grid cells = **50,412.5 cm** (504.125m). A 0.8% error that accumulated across 37 splines into visible misalignment at the far end of the river. Fixed.

**Lock 6 removed.** The terrain between the old Pool 10 and Pool 12 only drops ~1.3m — not enough to justify a lock structure. The two pools were merged into a single continuous pool. The pipeline now runs with 5 locks producing 6 pools instead of 6 locks producing 7 pools.

---

## Lock Design

This is where the bulk of April's design work went. The lock system evolved from the placeholder "24m cubes" in the March report into a fully specified piece of industrial infrastructure.

### The Managed Section

The biggest design change: **Pool 6 was raised** — significantly — during terrain adjustment. The river through the civic heart of the city now sits in an elevated canal, roughly 14m above Pool 4 upstream and 35m above Pool 8 downstream. This wasn't the original plan. It emerged from adjusting dam heights to match the actual sculpted terrain, and discovering that the central stretch of the city looked better with the river higher — more imposing dam walls, more visible infrastructure, a better backdrop for the Central Park and government buildings.

The consequence is that Locks 3 and 4 had to be completely redesigned:

- **Lock 3** became a **pumped lock** — a rising lock where the downstream pool (Pool 6) is 13.81m higher than the upstream pool (Pool 4). Pumps draw from a reservoir fed by the upstream river flow and push water uphill through wall holes into the lock chamber. A diversion tunnel (~3 km, six 6m-diameter bores) carries the natural river flow underground from Lock 3 to Lock 4, bypassing the managed pool entirely. Pump houses along the tunnel maintain Pool 6's level.

- **Lock 4** became a **two-step staircase lock** — 34.61m of total drop split across two chambers (~17.3m each). Three sets of miter gates: upstream, middle (in a 20m separator wall), and downstream. Entry and exit channels are on opposite sides, creating a zigzag pattern where barges cross the chamber width in each stage. At 280m long and 170m wide, it's the largest structure on the river.

The design history got woven into the lore: Locks 3 and 4 were originally conventional gravity locks, rebuilt during the Boom when TMC raised Pool 6 to create the elevated canal through the civic district. The official reason was urban planning — dramatic backdrop for the Central Park and founding monuments. The real reason was power projection. Lock 4's brutalist monolith, visible from much of the southern city, is an architectural reminder of who controls the infrastructure.

### Lock Internals

Every lock now has a full internal specification:

| Component | Spec |
|-----------|------|
| **Total width** | 170m (45m per side + 80m chamber) |
| **Per side** | 6m outer wall + 5m spillway + 10m bypass (tainter gates) + 8m side channel + 6m pipe channel + 10m lock wall |
| **Front/back walls** | 30m thick reinforced concrete |
| **Side walls** | 10m uniform |
| **Gates** | Miter gates, 20m entry channel, 11.6m leaf length at 18.4° miter angle, 2m thick concrete-core with steel facing |
| **Fill/drain pipes** | 3m × 3m square, 16 total (8 per side), bottom-filling to minimise turbulence |
| **Fill time** | Lock 1 in ~2 min (gameplay target); Lock 4 full transit ~13 min |
| **Bypass** | Tainter gates (2 sets per side, sequential), spillway as passive backup |
| **Barge capacity** | 4 × 15m barges per chamber, FIFO entry/exit on opposite sides |
| **Winches** | 4 per barge slot (16 per chamber), automated positioning |

The bypass system uses tainter gates instead of simple weirs — two sets per side (outer for river level management, inner for lock chamber fill/drain control), with a passive spillway as backup if the gates fail.

---

## Infrastructure Standards

A question about lock interior design — how big should the doors be? — led to a broader design document. Terridyn's three human subspecies (ROH at ~150cm, Normal at ~170cm, Heavy Worlder at ~210cm) need different built environments. The result is a **three-tier infrastructure standard**:

- **Tier 1 (HW-Rated):** 240cm doors, 300cm corridors — locks, public buildings, government facilities
- **Tier 2 (Normal-Rated):** 210cm doors, 270cm ceilings — most residential and commercial
- **Tier 3 (ROH-Optimized):** 180cm doors, 220cm ceilings — ROH housing, station-derived spaces

All locks are Tier 1. A Heavy Worlder crew member moves through the lock at full comfort — no ducking, no turning sideways. This matters for gameplay: HW companions can't follow into ROH-optimized spaces, and HW NPCs in Normal-rated buildings visibly struggle with the architecture. The built environment communicates social stratification without dialogue.

All 22 corporation files now have an HQ infrastructure tier field. TMC is HW-Rated (mining corporation, HW workforce). The rest are TBD — decisions to make as each corporate HQ gets designed.

---

## The Parametric Rule

A lock design discussion surfaced a fundamental engine architecture requirement. Lock water levels change over time (fill/drain cycles). If the player time-skips 6 hours, every lock on the river needs to be in the correct state — mid-fill, idle, draining, whatever the barge schedule says. You can't step-simulate 6 hours of lock cycles in the background.

This generalised into **Manifesto Principle 1a: The Parametric Rule**. Every time-dependent system must express its state as a function of world time: `state = f(t)`. No system may depend on tick-by-tick simulation to produce correct results. Time skips compute world state directly.

The exception is clear: systems that depend on player input at every step — combat, stealth encounters, active chase sequences — can't be parametric because they're reactive. When those systems are active, time skip is disabled. The rule is self-consistent: if a system needs tick simulation, the player is actively engaged and wouldn't be time-skipping anyway.

This is now a manifesto-level commitment, not just a technical guideline. Any future system that can't express itself parametrically gets redesigned until it can.

---

## Controls Update

The controls creative brief got an overdue split between first-person and third-person input behaviour:

- **First person:** mouse directly controls view, WASD is view-relative (standard FPS)
- **Third person:** RMB+mouse orbits the CAMERA drone, WASD is screen-relative (push W = character moves toward top of screen), camera tracks but doesn't auto-rotate with movement
- Controller right stick is always-orbit in third person (no modifier needed) — matches standard third-person convention

Scope zoom in third person moves from RMB to Middle Mouse Button since RMB is now camera orbit. A small change, but it needed to be documented before it became an assumption baked into other systems.

---

## Water Body Custom Actors

Late in the month I started transitioning from UE's Water Body River actors to **Water Body Custom** actors for the actual water surfaces. The river actors' procedural mesh generation doesn't respect dam geometry — water bleeds through walls because the mesh is generated from the spline, not from collision. Custom water body actors use imported static meshes ("slugs") that can be shaped to fit precisely between the dam walls.

The transition required building an **Editor Utility Widget** — a Blueprint tool that copies spline data from the existing Water Body River actors to the new custom actors. Positions and tangents copy cleanly; the metadata properties (Depth, RiverWidth, WaterVelocityScalar, AudioIntensity) require a small C++ wrapper to set because the setter function isn't exposed to Blueprint (the getter is — an oversight in Epic's API).

This work is ongoing. The slug meshes are generated in Blender and the copy tool handles the bulk of the data transfer. The C++ metadata setter is the last piece needed to complete the transition.

---

## By the Numbers

- **5** locks fully designed (was 6 placeholder cubes)
- **37** dam segments generated and placed
- **170m** lock total width, fully specified cross-section
- **280m** Lock 4 staircase length
- **3** pipeline bugs fixed (depth mismatch, WP cell size, Lock 6 removal)
- **1** manifesto principle added (Parametric Rule)
- **1** infrastructure standards document (3 tiers, 22 corp files updated)
- **1** Blender dam mesh generator
- **1** Editor Utility Widget for water body data transfer
- **~750** spline points across 37 river segments

---

## What's Next

- **Water body transition.** Complete the custom water body actor setup — C++ metadata setter, validate buoyancy, replace all river actors with slug-based custom actors
- **Lock modelling.** The blockout geometry is placed; detailed modelling of Lock 4's staircase is underway. Gate leaves (11.6m miter gates), winch systems, control stations, and HW-rated access infrastructure need to be built
- **Landscape sculpting.** Still the big manual pass — banks, beaches, dock areas, the Central Park overlook above Lock 4
- **City blockout.** The grid exists on paper; it needs to exist in geometry. Roads, block volumes, height zoning
- **Enemy AI prototyping.** Carried forward from last month — still waiting for city geometry to test against

The river is done as a system. The infrastructure is specified. Now it needs to look like a place where people work, where barges queue, where the spray from the weirs catches neon light at night.

---

*MGA is a solo-developed adult action RPG currently in pre-production. Follow development at the [Project Tangent SubscribeStar](https://subscribestar.adult/aridecan).*
