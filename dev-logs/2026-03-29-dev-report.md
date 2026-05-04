# MGA Dev Report — Late March 2026

*Ordinary Girl Meghan / Magical Girl Ava*

---

## From Paper to Dirt

The last report ended with "the shift from 'what are we building' to 'how do we build it' starts now." Three weeks later, I can say: we're building it. The documentation phase is behind me. This report is about what happened when the designs met the engine — and what happened when the engine pushed back.

---

## The Remaining Briefs

Before leaving the documentation phase, the last loose ends got tied off.

**UC consistency pass.** All 30 creative briefs were restructured to a standard Actor/Goal/Trigger/Step-by-step use case format. No new content — just reorganising existing material so every brief reads the same way. Reference data (training hub catalogues, settings tables, JSON schemas) stays as reference sections rather than being forced into UC structure. Final count: **30 briefs, 194 use cases.**

**Six new creative briefs.** The remaining minigame systems got their briefs written:

- **Cooking** (5 UCs) — recipe puzzle design, ingredient properties, skill integration
- **Restraint Escape** (5 UCs) — escape mechanics when captured
- **Trap Disarming** (5 UCs) — field puzzle for traversal hazards
- **Crystal Purification** (5 UCs) — processing raw crystals
- **Grapple** (6 UCs) — lane-based card contest, 5 lanes, 8 cards per side, real-time bidirectional. This supersedes the earlier 8-pointed star concept entirely

That brings the total to **36 creative briefs, ~236 use cases.** The creative brief library — phase 2 of documentation — is done.

**Phases 3 and 4 are paused.** Feature briefs (player-facing design) and technical briefs (implementation specs) both need a basic city in-engine to write against. You can't describe how systems behave *in the world* without having the world to reference — that just leads to rewrites once the geometry is real. So the documentation work is on hold until the city blockout exists. That's the main reason for the shift into engine work described below.

**Enemy AI creative brief** also got written — 16 use cases covering perception, engagement, fleeing, group tactics, morale, and signal parity. The architecture decisions (nav mesh vs nav graph, utility AI + GOAP + BT hybrid) are still blocked on engine prototyping, but the player-facing design is documented.

---

## City Infrastructure

The city got substantially more detailed before I moved to the engine.

### Road Reclassification

The old 2-tier road system (standard streets vs stacked arteries) was replaced with a proper **4-tier hierarchy**:

| Class | Name | Width | Role |
|-------|------|-------|------|
| L1 | Local | 20 m | Residential, low-traffic — the vast majority of the grid |
| L2 | Secondary | 25 m | 4-lane divided, collector routes |
| L3 | Arterial | ~35 m | 6-lane divided, major through-routes |
| H1 | Highway | ~40 m+ | Stacked/elevated, up to 4 decks |

The stacked highway levels got renamed from L0–L3 to **Deck 0–3** to avoid collision with the road classification. Blocks changed from square to **rectangular** (80 m × 105 m, long side N-S), giving different rhythm in each axis.

### City Grid Expansion

The downtown-only 8×8 grid was replaced with a **full 32×32 grid** covering the entire 16 km × 16 km terrain. Columns A–AF, rows 1–32, row 1 = north. Downtown occupies columns M–T, rows 13–20 — the same physical 4×4 km area, now properly located within the full map. Every feature placement was migrated to the new coordinate system, and UE coordinate mapping was defined (50,000 units per cell, ±800,000 full grid).

The river course got corrected during this work. The old document described it as a diagonal from NE to SW across downtown. The actual terrain data shows it running more **north-south through the O column** — entering downtown at O13, shifting slightly east around the centre, and exiting through N19–N20.

### The Lock System

Here's one that came from the engine pushing back on the design.

The river drops ~76.5 m across its ~18.8 km course through the city. At that gradient, the water velocity averaged around 2 m/s — which sounds fine until you remember the design says the river should be swimmable. 2 m/s is not swimmable. That's "you are now downstream whether you wanted to be or not" territory.

The solution: **6 lock/dam/weir complexes** evenly spaced along the river, each absorbing ~12.76 m of elevation change. The locks step the river down in controlled increments, keeping flow velocity in the **0.5–0.8 m/s range** between structures — actually swimmable, with effort.

A correction here: the original lock sizing was based on 200 m barges. That was my mistake — I looked up the length of 15 m wide barges and got back "200," which turned out to be 200 *feet*, not metres. Actual barge length: **65–76 m**. That let the lock chambers shrink significantly.

Each lock complex is now approximately **220 m long × 120 m wide × 24 m tall**. Still substantial, but much more manageable to place on the map and far more believable as infrastructure at Terridyn's 8.5% population level.

The design details are worth highlighting:

- **Lock chamber** is 100 m long × 65 m wide, flanked by a 60 m dam on each side (totalling 220 m end to end). The chamber fits 4 barges side by side with barely room to spare — 76 m barges in a 100 m chamber, 15 m wide each in a 65 m slot. Entry and exit gates are 20 m wide — single-file entry, then barges stage inside
- **Weirs** run alongside each lock, operating 24/7 for flow regulation. Water cascades the full 24 m of the downstream face — permanent white water visible and audible from blocks away. At night, the spray catches neon light from the surrounding city
- **Bridge decks** sit on top of each dam, 24 m above the lower pool. Loaded ore barges pass at approximately bridge deck level — standing on the bridge looking downstream, ore piles are at your feet
- **Fish ladders** are present at each complex — minimal, bare-compliance installations. They exist not because anyone in corporate governance cares about river ecology, but because the C-suite executives need their recreational fishing. The fish populations are a perk, not a priority
- **Corporate control**: each lock is owned and operated by a corporation. Controlling a lock means controlling ore and slag flow through that section of the river. Lock scheduling becomes a corporate corruption opportunity — whose barges get priority?

Six guaranteed river crossings (the lock bridges) plus surface-road drawbridges between them. The locks are major landmarks, potential quest locations, sabotage targets, and corporate chokepoints. Each one fits comfortably within a 500 m streaming cell — which also makes them much easier to design and optimise as individual assets.

### Atmospheric Pressure Model

The planet's atmospheric pressure got a proper physics pass. The old version said "at 350 m elevation = Earth sea level" — but Terridyn City sits at 1,220 m. That didn't add up.

The revised model: sea level pressure is **~14% higher than Earth** (~115,700 Pa vs 101,325 Pa), dropping with a scale height of ~8,095 m. At **~1,100 m elevation**, pressure equals Earth at sea level. Terridyn City at 1,220 m sits comfortably at Earth's ~150 m equivalent — which was already established in the city document. Now the planet document actually explains why.

This also feeds back into the megafauna justification — the higher atmospheric pressure combined with 23% oxygen means the effective oxygen partial pressure at sea level is significantly above Earth's, which is the real driver of the 10–20% size increase in flora and fauna. Coastal zones feel noticeably heavy and humid; the mountain city is the most comfortable habitation zone on the planet.

---

## Into the Engine

This is the part I've been waiting to write about.

### Terrain Pipeline

Getting a 16×16 km terrain from concept to engine turned out to be a multi-step pipeline that I had to develop through trial and error. The workflow is now documented and repeatable, which matters because I'll almost certainly need to redo parts of it.

**Gaea 2** generates the base terrain. Two variants: one with a river canyon baked in, one without. Output size is tuned to map cleanly against UE's 8K landscape node resolution — currently ~16,232 m, though the exact number may need adjustment.

**Import into UE** as an Empty Open World Map. This eats about **48 GB of RAM** and — critically — does not create landscape volumes automatically. That's a manual step: an **8×8 grid of landscape volumes** overlaying the 32×32 grid of landscape proxies that the import creates. With volumes in place, I can selectively load only the regions I need.

### River Construction

This is where the scripts come in. Getting a river placed correctly in a 16 km terrain is not something you do by hand — not if you want it to match the design specifications.

**Step 1: Channel preparation.** Load only the river regions from the canyon variant. Flatten the river bottom using the sculpting tools set to 8,000 UE units, ensuring a minimum 80 m width. Clear out side channels.

**Step 2: Survey.** A Python script running inside UE acts as a sensor drone. It flies down the river channel, measuring bank height (where slope exceeds 10° from horizontal), river width, and depth — then moves 1 metre downstream, re-centres, and repeats. This runs for **several hours** and produces a complete river profile at 1 m resolution.

**Step 3: Normalize.** A standalone Python script reads the raw survey data, smooths it, and outputs spline control points at **30 m intervals**.

**Step 4: Water bodies.** A third Python script, running back inside UE, reads the normalized spline data and creates **Water Body River actors** with the correct spline points.

The result is a rough but correctly positioned river. From there, it's manual sculpting — conforming the landscape to the water bodies, carving out beaches, dock areas, and the six lock complex sites.

### Current State

The first pass of the landscape is done. Lock complexes and river elements are positioned. The scripts handle most of the heavy lifting now — if I need to regenerate the river (different gradient, adjusted channel), the pipeline is repeatable without starting from scratch.

**Next up:** sculpting the landscape into a playable area. The terrain is correct in broad strokes; now it needs to feel like a place people live in. Embankments, beaches, dock frontage, the Memorial Park's sculpted river beach, lock complex surroundings — all of that is manual work that the scripts can't do.

---

## Technical Brief

The full landscape and river pipeline is now documented in **TB-landscape-river-pipeline** — the second technical brief after the Content Directory structure. If I get hit by a bus (or more likely, if I take a month off and forget everything), the entire Gaea → UE → survey → spline → water body workflow is written down step by step.

---

## By the Numbers

- **36** creative briefs (was 30)
- **~236** use cases documented (was ~130)
- **6** lock/dam/weir complexes designed
- **3** Python scripts in the river pipeline
- **2** technical briefs
- **1** landscape imported and river placed in UE5

---

## What's Next

- **Landscape sculpting.** The big manual pass — turning raw terrain into a believable city riverfront
- **City layout.** Placing the grid, the corporate blocks, the height zoning. The numbers are all in the documents; now they need to exist in the engine
- **Enemy AI prototyping.** The creative brief is written; the architecture needs engine time. Behaviour trees, navigation, perception — finding out what UE5 gives us for free and what we need to build
- **Animation architecture.** Still dependent on engine experimentation. The hit reaction, combo, and transition systems need UE5's animation pipeline understood before they can be designed properly

The project is in the engine now. Everything from here forward leaves marks on the terrain.

---

*MGA is a solo-developed adult action RPG currently in pre-production. Follow development at the [Project Tangent SubscribeStar](https://subscribestar.adult/aridecan).*
