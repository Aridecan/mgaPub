# MGA Dev Report — June 2026

*Ordinary Girl Meghan / Magical Girl Ava*

---

## The Month of the Big Bet

May ended with "the river is done, the roads are arriving, the downtown is starting to look like a place." June started by *finishing* that place — the full downtown road network, the demographics, the residential blockout — and then did something risky: tore up the ground it was all sitting on.

Halfway through the month, UE 5.8 shipped with a new terrain system, and the project took the bet to migrate onto it mid-build. That decision cost a re-do of the entire road-to-terrain conformity system, a machine-killing engine crash, and a bug report to Epic. But it ended somewhere the project has been trying to reach for a year: **the city now builds itself from data** — roads, blocks, sidewalks, lots, and buildings, generated procedurally from the grid spec, conforming to the terrain, with no hand-placement. June was a long month.

---

## Finishing the First City

The first half of June closed out everything May had been building toward on the old heightmap landscape.

**144 superblocks, complete.** Eighteen days after May's "12 of 144," the full 24W↔24E / 24S↔24N downtown core was laid out in the engine — all 13 north-south rows. The hardest single superblock was 8W×16N: a river bend, the TMC dock complex, and four corner curves all in one tile.

**The conformity brush, calibrated.** The road-conformity brush from May got tuned end-to-end to sub-texel accuracy across the full 16 km landscape. The diagnostic that cracked it was almost embarrassingly physical: place four 1 m reference cubes at known intersection corners, read them back through the capture pipeline, and fit the math to the error. That cleanly separated "is the capture right?" from "is the apply right?" — without it, the calibration would have been a much longer hunt.

**The GPS map pipeline.** A three-stage Python pipeline now renders the downtown to a single annotated SVG: road network by tier, block addresses, river and locks, district zones, and building classes — each tagged so the engine can later harvest the labels into in-game GPS/map UI. This map became the project's workhorse reference for the rest of the month (it's how block IDs get picked for everything downstream).

**Demographics and the residential blockout.** A demographics overhaul (correcting the child-population share, which halved the school count) fed a full residential build-out: **438 buildings across 365 blocks — about 20 million m², the entire housing budget for the city.** That ran on a new five-class building taxonomy (Class A 60-storey towers down to Class E 6-storey walk-ups, all sharing a standard block footprint), an interactive placement ledger, randomized bell-curve building footprints, twin-tower blocks, and an "L2/L3 commercial podium" zoning rule. A class-coloured map render makes the whole housing layer legible at a glance.

---

## The Empty City

The residential numbers tell a story the worldbuilding leaned into all month. Terridyn's downtown was **built for a million people and holds about eighty-five thousand** — roughly 8%. About 15% of the buildable land carries 91%+ of the housing; the rest is reserved for industry, research, and parks that the population collapse left half-used.

That's the texture the player walks through: **mothballed boom-era infrastructure** — dark, sealed buildings on nearly every street, lights off, doors locked, a city wearing clothes three sizes too big. The tone is dark corporate satire with a signature inversion — *the menace wears a lanyard* — held deliberately on the right side of the line: **dark, but never grimdark**, anchored by Meghan's stubborn altruism and the sincere absurdity of Sir Gallopington. The empty city isn't bleak set-dressing; it's the central mood, and June was when the geometry finally started to sell it.

---

## The Big Bet: UE 5.8 and Mesh Terrain

UE 5.8 arrived mid-month, and an evaluation pass found several features that map directly onto MGA's open problems — but one was load-bearing: **Mesh Terrain (the "Mega Mesh").**

The project's terrain had been a classic heightmap landscape. Heightmaps are 2.5D (one height per grid cell) and, at the resolution a 16 km playfield can afford, the road beds came out *sawtoothed* — the grid was too coarse to follow a clean road centreline. Mesh Terrain replaces the heightmap with an actual adaptive mesh: finer where it needs to be, and genuinely 3D, which opens the door to caves and overhangs the old landscape could never represent.

So the project took the bet and migrated. This is not a small change mid-production — it invalidates the road-conformity brush (which deformed a heightmap), changes how ground materials are painted, and changes how anything queries terrain height. But the sawtooth problem wasn't going away on its own, and the longer the city grew on the old terrain, the more expensive the move would get. June was the cheapest this migration was ever going to be.

---

## Re-Conforming the Roads

On Mesh Terrain, the road-conformity brush retired. In its place: a PCG (procedural) graph that conforms the mesh terrain to the roads directly, plus a step that didn't exist before — **intersection leveling.**

Where roads cross, their beds have to meet at one height or the pavement buckles and clips on a hill. A new tool walks every junction, levels the crossing road splines to a common height, and lays a flat shoulder out to the curb so the intersection sits as one coplanar plane. It smooths the seams where the per-superblock road chunks meet, and it does it region-by-region so the work stays bounded. Getting this right was a multi-session grind — terrain traces that silently miss the new mesh, z-fighting between pavement and conformed ground (fixed by dropping the ground a slab-thickness below the road), and a per-tier corridor width so 40 m arterials conform as cleanly as 20 m lanes.

The payoff is that the roads now sit *on* the new terrain correctly — which became the foundation everything else this month stood on.

---

## The Crash, and the Bug

Then the new terrain tried to kill the machine.

Baking the whole-world mesh terrain — the step that turns the live preview into final geometry — ran the system out of memory so hard it took down two of three monitors and required a remote reboot. The error was "out of page file," and the obvious fix (a bigger page file) didn't help: a 128 GB page file crashed, an unbounded one crashed, and the commit charge just climbed without limit.

Root-causing it turned up a genuine engine bug. The World Partition builder *has* a memory governor that's supposed to throttle the bake before it runs out — but it only watches **physical RAM**, which Windows keeps healthy by paging committed memory out to disk. So on any machine with a page file, the governor never fires; the build keeps committing memory until it hits the system commit limit and dies. The fix is a few lines — also check available *commit* (which the engine already computes and even logs, but never uses in the decision). It lives in core World Partition code, so it affects every WP builder, not just the new terrain.

That got written up properly and **reported to Epic** (forums + a proposed patch). It also clarified the build pipeline going forward: the heavy whole-world bake belongs on **CI** (where it can be distributed via Unreal's Zen storage and produce one authoritative, consistent result), while local work stays on the live interactive preview — which, crucially, needs no whole-world bake at all. The governor fix is a prerequisite for that CI bake, since build-farm machines have *less* RAM than the dev box: if it crashes here, it crashes there.

**A favour, if you have an Epic account.** Two engine bugs surfaced during this month's terrain work, and both are now filed on Epic's forum. The tracker surfaces issues by community upvotes — a report needs a handful before Epic triages it — and these hit *anyone* doing large World Partition / Mesh Terrain work, not just MGA. If you can spare a click, an upvote on either genuinely helps:

- **World Partition builders crash with "Out of Page File" (memory governor ignores commit charge):** https://forums.unrealengine.com/t/world-partition-builders-crash-with-out-of-page-file-hasexceededmaxmemory-only-checks-physical-ram-never-commit-charge-affects-all-wp-builders/2731619
- **[5.8] USplineRemeshModifier selects no triangles (coordinate-space mismatch):** https://forums.unrealengine.com/t/5-8-usplineremeshmodifier-selects-no-triangles-coordinate-space-mismatch/2730339

---

## The City Builds Itself

And then the month paid off.

The city tools — RoadBLD and CityBLD, the WorldBLD suite — turned out to be **fully scriptable.** CityBLD's whole pipeline (place a block, subdivide it into lots, assign land uses, spawn buildings) is exposed to scripting. Which means the city no longer has to be clicked together by hand: it can be **generated from the grid spec.**

The pipeline now goes, entirely from data:

1. A block ID (`db47B`) → its four corner coordinates, computed from the grid math (superblock indexing and the 10° sightline rotation handled automatically).
2. Inset to the curb line, with chamfered corners matching the road radii.
3. Each corner's **height read from the leveled road splines** — so the block sits on the sidewalks, not floating on raw terrain. (The terrain, it turns out, is the *wrong* height reference; the roads are leveled, and the sidewalks conform to the roads.)
4. The edges **densified with the road's leveling points**, so a block on a hill follows the road's slope instead of cutting a straight line through it.
5. The whole thing placed, named, foldered, and generated into sidewalks, lots, and buildings — idempotently (re-running a block cleanly replaces it without disturbing its neighbours).

Getting there meant solving a stack of subtle problems — generation that silently doubled, corners that pinched when the sidewalk inset was wider than the corner chamfer, a parcel-subdivision algorithm that choked on the dense edges until it was switched to a different method. All of it is now understood and documented. The result: the first blocks generated **purely from the grid data, conforming to the roads in height, slope, and corner shape, with clean lots and buildings** — and a clear path to running it across the whole downtown.

The building *styles* are still placeholders (the sample kits ship American and European looks; Terridyn's sci-fi districts get authored later). But the machine that places them is real, and it's driven by the same `db<col><row>` grid and demographic ledger the rest of the city already uses.

What this buys is **time.** Hand-placing the first city's road network and residential blockout took the better part of two months of clicking through coordinates. The procedural pipeline collapses that mechanical work into a script run — and it's the whole toolchain that came together this month, not just this one piece: the road export, the GPS-map renderer, the conformity leveling, and now the block generator. Every hour not spent placing identical buildings by hand is an hour back for design, story, and the parts of the game only a person can make. For a solo project, that's the difference between "someday" and "this year."

A line worth drawing clearly, because the question always comes up: **this is not generative AI, and not one game asset has been made by AI.** The meshes, materials, textures, characters, and world are authored the normal way, by hand. What AI has done on MGA is help **build the tools** — the Python and in-editor scripts that automate the mechanical parts of construction. That's the same distinction Epic and Valve have both drawn: using AI to accelerate your own workflow is *force-multiplication of human effort*, a different thing from the generative-AI asset creation the asset debate is actually about. MGA's content is, and will stay, human-made; the tooling just lets one person move like a bigger team.

---

## Tooling: Driving the Editors

A quieter thread underneath all of the above: the editors got wired for direct automation. Both Blender (via the official Blender Lab MCP server) and Unreal can now be driven programmatically for inspection and scripted edits — reading scene state, running geometry queries, executing editor scripts, taking screenshots. Most of the CityBLD pipeline above was built and tested through that channel. It's plumbing, not a feature, but it's a meaningful speed-up for exactly this kind of procedural, iterate-in-the-engine work.

*(The dev environment also moved off the old Linux VM onto Windows this month, and an evaluation of Epic's new open-source version-control system, Lore, kicked off — both worth their own deeper write-up, and both feeding the revision-control tooling videos.)*

---

## By the Numbers

- **144 of 144** downtown superblocks road-networked (full core complete)
- **438** residential buildings placed across **365** blocks — ~**20 M m²**, the full housing budget
- **5** building classes (A 60-storey → E 6-storey), one shared block footprint
- **~85,000** residents in a city built for **~1,000,000** (~8% occupancy)
- **1** terrain system migrated mid-project — heightmap landscape → UE 5.8 Mesh Terrain
- **1** road-conformity system rebuilt — brush → PCG conformity + intersection leveling
- **1** genuine engine bug root-caused and reported to Epic (WP build memory governor ignores commit charge)
- **1** full procedural block pipeline: grid ID → road-conforming, slope-following, lot-and-building block, from data
- **<1 cm** accuracy of the data-derived block geometry vs the hand-built reference
- **2** editors (Blender, Unreal) wired for scripted automation
- **3** three-stage GPS-map pipeline producing the annotated downtown SVG

---

## What's Next

- **Bulk the loaded region.** The single-block pipeline is proven; next is running it across the loaded downtown rows so the city fills in. Needs per-edge road-tier handling (arterial-bordered blocks) and a spatial speed-up before it scales cleanly.
- **MGA districts.** The block machine works; now it needs *Terridyn's* look — custom sci-fi/cyberpunk building styles and zoning, driven by the existing demographic ledger, replacing the placeholder sample kits.
- **The CI bake.** Stand up the whole-world mesh-terrain bake on CI with Zen distribution, gated on the governor fix landing (or a source patch). Local work stays on the live preview.
- **Enemy AI prototyping.** Still carried forward — but the ground it needs to stand on is, finally, almost entirely in place.

The first city was built by hand. The second one is learning to build itself.

---

*MGA is a solo-developed adult action RPG currently in pre-production. Follow development at the [Project Tangent SubscribeStar](https://subscribestar.adult/aridecan).*
