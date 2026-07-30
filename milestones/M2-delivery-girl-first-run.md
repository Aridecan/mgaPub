# CoreX-M2 — Delivery Girl: First Run

> **Status: defined 2026-07-27. Not started.** CoreX-M1 is still in progress; M2 begins as M1's
> remaining workstreams close out. This is a living tracking doc — check items off as they land.
>
> **Shape of the work:** two long poles that parallelize by owner — getting Nessa out of Blender and
> into a packaged build (Peter, art + editor) and getting a slice of Terridyn to survive the cook
> (bake + build scripts). Everything else hangs off those two.

## Framing — "CoreX" (EA design flow)

**CoreX = proving out the "X" of the game** — the core technologies a *viable* game requires, de-risked
before content is built. [CoreX-M1](M1-framework-and-ci.md) proved three X's: the menu system, DLC
load + polymorphic override, and the CI cook. All three were infrastructure with no game in them —
deliberately.

**CoreX-M2 puts something in them.** It is the first milestone with a character, a world, and a
player who can move.

## Delivery Girl — what the mode is

**Delivery Girl is a real shipping mode**, reached from the main menu under **Game Modes**. It stars
**Nessa**, a character who appears in this mode and nowhere else in the game.

- **By day** she runs ordinary deliveries — the courier loop from
  [CB — Jobs](../creative-briefs/CB-jobs.md) UC2, which that brief calls *"the city exploration engine."*
- **By night** she runs them naked or in otherwise compromised attire, trading cover for money and
  exercising the stealth systems. Parts of the economy are built into the mode, so the risk/reward
  actually resolves into currency.

The mode is also **the incubator**: features roll out here first, get tested by real play, and are
then pulled into the campaign. That gives it a second job beyond being fun — anything built for
Delivery Girl has to be built to be *reusable*, in shared modules, not in a mode-private silo.

### Why it is the right vehicle for the content tiers

The day/night attire swap is the **first real consumer of the M1 override mechanism, carrying meshes
and materials instead of an `FText`**. M1 proved later-DLC-overrides-earlier on the smallest possible
surface — a line of warning text. Delivery Girl proves it on the payload the whole game depends on,
in a place where it earns its keep rather than existing as a tech demo.

**Tier boundary (decided 2026-07-27):** the night loop **ships in Core**; only the body, the attire,
and the animation set are overridden.

- **Base / Core** ships a **featureless body with the Twitch-safe areas covered** — the same
  stream-safe minimum-cover standard as every Core outfit. Night runs exist in the base game at that
  standard, with the stealth and exposure mechanics fully present.
- **Spicy** ships the **full body, without the Twitch-safe coverage**, plus **clothing destruction**,
  and *overrides the base body*. Reference level: **Baldur's Gate 3 / Conan Exiles** — nudity, not
  explicit content.
- **SuperSpicy** is **mainly animations layered on top of Spicy**. It does not replace the body
  again; Spicy already carries it.
- The **mechanics never change** across tiers. Only what the override chain resolves the body,
  outfit, and animation set to.

This keeps the base mode a whole game rather than half a game, keeps gameplay logic on the safe side
of the Steam compliance boundary, and reduces the tier-delivered payload to an asset swap — the
cheapest thing to keep out of a container and the easiest thing for the leak-audit gate to police.
See [Steam adult-content boundary](../gdd/content-and-dlc.md).

> **The two boundaries are not the same line.** The **Steam boundary** sits between **Core and
> Spicy** — nothing above Core is present in the Steam container. The **explicit-content boundary**
> sits between **Spicy and SuperSpicy** — Spicy is BG3-level nudity, SuperSpicy is where explicit
> material lives. Keeping the Steam cut at the lower of the two is a deliberate product decision
> (Core stays Twitch-safe and stream-legal by construction; see the outfit standard), not a legal
> minimum.

> **SuperSpicy overrides a different kind of thing, and that is a third mechanism.** M1 proved
> override on **C++ behaviour** (a polymorphic provider). M2's stretch proves it on **assets** (a
> skeletal mesh). SuperSpicy needs it on **animation** — linked anim layers, montage sets, or an
> anim-BP override registered at activation. None of the three are the same code path. This is an
> **M3 problem**; it is recorded here so it is not mistaken for free once the mesh swap works.

> **Compliance note, and the reason the base body must be featureless.** The character pipeline
> gives every female character **one shared base body mesh**, so that mesh ships in the Steam
> container by construction. If it carried explicit detail, adult content would be present in the
> Steam build **with zero Spicy assets installed** — and the leak-audit gate would not catch it,
> because the gate asserts that no *DLC-plugin* content appears in a base container, and this would
> be base content. Featureless base + tier-delivered full body is what keeps that from happening.

---

## One-line goal

**Nessa stands up in a baked slice of Terridyn, in a packaged build, and you can walk her around with
sound underfoot.**

M2 does not build the mode. **M2 builds the ground the mode stands on**, and proves it survives the
cook.

## The X's this proves

1. **The character pipeline** — Blender body → UE skeletal mesh → hair sockets → spawn-time
   scale/morph order → visible in a packaged build. The pipeline in
   [Character Pipeline](../gdd/character-pipeline.md) is fully designed and entirely unbuilt: the
   project currently holds `M_Master_Character`, `M_Master_Cloth`, `M_Master_Hair` and **no character
   assets whatsoever**.
2. **The city survives the cook** — one megablock baked to static meshes and packaged with the BLD
   plugins disabled. Today `cook.ps1` says it plainly: *"Roads/city aren't baked to static meshes
   yet, so a plugins-disabled cook drops those actors and produces a roadless-but-packageable game."*
   `TerridynCity2Map` holds ~49,700 World Partition actors and **none of them reach the player**.
3. **A third-party runtime plugin in CI** — GMC builds, cooks, stages and ships. Every third-party
   plugin so far (CityBLD, RoadBLD) has been editor-only and simply switched off at cook. A movement
   plugin cannot be switched off.
4. **M1's controls and audio close the loop** — the `IA_`/`IMC_` assets from M1 WS4 drive a real
   pawn for the first time, and footsteps land in the M1 submix chain.

**What M2 does not commit to prove: the mesh override.** Base-body-vs-Spicy-body is the highest-value
X in this line of work, and it is a **stretch goal** here, otherwise M3. You cannot prove a body
*override* until you have a body, a spawn pipeline, and a packaged build that can display one.
**M2 builds the thing M3 overrides.** That sequencing is intentional, not a gap.

---

## Definition of Done

### Pillar A — Nessa exists

- **Base body imported** — featureless, Twitch-safe areas covered, single skeleton.
- **The base export must NOT carry the explicit morph targets** (found 2026-07-28). The Blender base
  body `MainBody` currently has shape keys `BreastSize`, `Anus`, `Vagina`, `Nipples`. `BreastSize` is
  wanted; the other three must be stripped from the base export and live only on the Spicy body.

  > **This is the compliance rule biting for the first time on a real asset, and it has an
  > architectural consequence.** The tier boundary **cannot** be implemented as morph values on a
  > shared mesh. "Base ships the mesh with the morphs at zero, Spicy drives them to one" fails,
  > because the morph deltas are present in the base container either way — and the leak-audit gate
  > cannot catch it, since the gate proves no *DLC-plugin* content sits in a base container and this
  > would be *base* content. Base and Spicy must be **two meshes**. See
  > [Content Architecture & DLC](../gdd/content-and-dlc.md).

  **Zeroing the morphs is not sufficient — the base geometry itself is not featureless**
  (Peter, 2026-07-28). The morphs are additive, but the Basis mesh already carries the anatomy, so
  the fix is **geometry work**: replace the groin region with featureless geometry and clean up the
  nipple area. Peter's assessment is that neither is difficult.

- **Fork the mesh; do not edit it in place.** The current `MainBody` — geometry plus the three
  morphs — is already the **Spicy** body. Duplicating before the edit yields both halves of the tier
  split for the cost of the one edit, and turns the stretch goal from "author a Spicy body" into
  "the Spicy body already exists." Editing in place destroys it and pushes that work into M3.

- **The shape keys are the risk in that surgery.** Blender shape keys are stored per vertex index, so
  removing and re-patching the groin region can corrupt or misalign key data across the whole mesh.
  `MainBody` carries 56 keys, 52 of which are the Faceit ARKit set that is now in M2 scope. The
  facial keys touch different vertices but share the same key stack, so **verify the ARKit set still
  deforms correctly after the edit**; the fallback is re-running Faceit.

- **Zero the morphs before export, and confirm the exporter does not fold current shape-key values
  into the basis.** All three currently sit at value ~1.0 in the Blender file, and `BreastSize` at
  0.781. An export that applies current values bakes that state into the mesh — for `BreastSize` it
  would fix the bust for every character and defeat the shared-base-plus-morph design.
- **Uniform XYZ scale and the breast-size morph are present and applied at spawn**, even if only one
  value is ever used. The pipeline is the deliverable, not the variation.
- **Hair on both sockets** (`Socket_FrontHair`, `Socket_BackHair`), origins snapped per the GDD so
  future styles drop in with zero per-style offset.
- Renders through `M_Master_Character` / `M_Master_Hair`.
- **Eyes render through `M_Master_Eye`** (added 2026-07-28). The master material already exists in the
  project and was missing from this list; a character with no eye setup looks broken in every
  screenshot the milestone produces, and the cost is small.
- **Nessa's front hair is one of the 10 general NPC front styles, not a signature style**
  (decided 2026-07-28). The [Character Pipeline](../gdd/character-pipeline.md) reserves signature
  fronts for the five magical girls plus Tierney; Nessa is a non-canon mode lead, so a shared style
  fits. This also means M2's hairstyle does double duty as the first of the reusable crowd set rather
  than being spent on one character.
- **Character Blueprint spawns in the documented order:** scale → morph → hair → *(clothing step,
  empty)* → cloth init. With no clothing and rigid hair, the cloth-init step has nothing to
  initialise in M2 — it exists so that the order is already correct when M3 puts something in it.
  That order matters: scale and morph must be applied **before** physics init, because Chaos Cloth
  requires stable vertex positions at init time.
- **No clothing.** The empty clothing step is deliberate — it is M3's insertion point.
- **Hair is RIGID in M2** (decided 2026-07-27). Simulation — Chaos Cloth or bone-based — is M3. This
  keeps the project's first character import boring, and keeps the first Chaos Cloth integration off
  the asset with the least forgiving failure mode.
- **But the hair still ships as a skeletal mesh with its strand/tail bone chain present, weighted,
  and simply not simulated.** Both M3 options need a bone chain: bone-based hair obviously, and
  Chaos Cloth needs the mesh skinned to something too. If M2's hair ships as a plain static mesh on
  the socket, M3 means re-authoring, re-exporting and re-fitting **every hairstyle built by then**.
  Unsimulated bones render identically to rigid — they just never move — so M3 becomes "turn on a
  physics asset" instead of "redo the hair." The 2AM skull-cap method already produces strand
  topology that chains cleanly, and the first hairstyle is being built now, so the cost is near zero.
- `Socket_FrontHair` / `Socket_BackHair` must be **real sockets on the body skeleton**, not baked
  transforms, so hair can be driven by head motion later.

### Pillar B — The city survives the cook

- **One megablock** (3×3 superblocks) of the NE Delivery Girl area baked to static meshes and
  submitted to P4. The area was earmarked for test game modes on 2026-06-11 for its low spoiler risk.
- **The megablock lives inside `TerridynCity2Map` as its own runtime data layer**, not in a separate
  map (decided 2026-07-28). This keeps one source of truth for city geometry and avoids drift between
  a mode map and the real city. The project already uses data layers (`DL_LegacyLandscape`), so the
  pattern is established. The cook cost is not what the actor count suggests: the ~49,700 World
  Partition actors are BLD-generated and are dropped when the plugins are disabled at cook, so the
  cook carries the baked static meshes, not the generator output.
- **The megablock is `db37A`–`db48L`** (decided 2026-07-28) — megablock-col 4 × megablock-row 1, the
  **NE corner** of the downtown core. 144 blocks, 9 superblocks. Its only named landmark is an
  InfoDyne satellite data centre at `db38C`. The follow-on megablock is `db25A`–`db36L` to the west,
  which carries SMU, the Neon Strip and VirtuLife — so the empty district is baked first and the
  content-dense one second, which is the right order for a bake test.
- **One superblock baked and verified first** — collision, physical materials, streaming-cell
  behaviour, and how it cooks — **before** the other eight are touched. This step exists to stop a
  nine-superblock mistake. Recommended first target: block-columns **45–48 × rows A–D**, the NE
  corner superblock, which is bounded by L3 on two sides and L2 on the other two and so exercises all
  three road classes in one bake.
- **Roads confirmed present (2026-07-28).** The entire downtown core is laid down — 24th St W /
  24th Ave N through 24th St E / 24th Ave S, the full 48 × 48 block grid — and ready to bake. **WS4
  is a bake job, not an authoring job.** Bridges over the water are not built, but every river/lock
  block sits in columns 16–20 and both target megablocks are columns 25–48, so no water crosses them.
  The project holds one baked road mesh today (a single L2 segment), so the workflow has been
  exercised once at segment scale.
- A **packaged build with CityBLD/RoadBLD disabled contains the baked city**, and you can walk on it.
- **Frame time on the megablock measured and written down.** A number, not a pass/fail. M2's job is
  to learn what the real city costs; a bad number is a finding, not a failure, and may legitimately
  shrink the baked area.

### Pillar C — Movement and feel

- **GMC integrated**; Nessa walks and runs; third-person camera per
  [CB — Camera](../creative-briefs/CB-camera.md) at its simplest useful setting.
- **Driven by the existing M1 WS4 Enhanced Input assets** — no new input path. This closes M1's
  controls work end-to-end for the first time.
- **Ambient audio bed** on the map.
- **Surface-driven footsteps** via physical materials on the baked surfaces, routed through the M1
  submixes.
- **M1's audio settings verified against real sound (inherited from M1 #16, deferred 2026-07-27).**
  M1 shipped the six submix volume sliders and could never confirm they *do* anything: with no sound
  in the build, a slider that silently fails and one that works look identical. M2 is the first
  milestone with audio to check against, so the check lands here — move each slider (master / music /
  SFX / voice / ambient / UI) against the ambient bed and footsteps and confirm the level responds,
  **in a packaged build**, since that is the configuration the settings path has never been proven in.

### Pillar D — Getting in, and CI

- **Main menu → Game Modes → Delivery Girl → map loads → Nessa possessed.** One entry, no mode
  content behind it. This breaks open the disabled-buttons state M1 left behind, and makes the whole
  chain from splash to player runnable unattended.
- **`mga-weekly` cooks** the map, the character, and GMC.
- **Leak-audit gate stays green**; the M1 four-config acceptance matrix still passes unchanged.

### Stretch — the Spicy body override

Pulled forward from M3 if the pillars land early. Small, self-contained, and the highest-value thing
available to add:

- The **Spicy** GameFeature plugin carries a **full body mesh** (no Twitch-safe coverage).
- On Spicy activation, the override chain **resolves Nessa's body to the Spicy mesh**; base-only
  resolves to the covered body.
- **The audit gate stays green** — the Spicy body must not appear in any base container.
- The **acceptance matrix grows a body-identity assertion**: config 1 shows the covered body,
  configs 2 and 3 show the full body, config 4 (SuperSpicy without Spicy) falls back to the covered
  body with the usual skip message. **Configs 2 and 3 assert the same body** — SuperSpicy contributes
  animation, not a second body — so config 3 remains distinguishable only by the existing
  content-advisory rank assertion until M3 adds an animation-set assertion.

---

## Explicit non-goals (out of M2)

Clothing and the clothing pipeline · body/attire tier overrides *(except the stretch above)* ·
deliveries, missions, objectives · the phone delivery app and GPS widget · economy and money ·
day/night cycle · stealth and detection · vehicles of any kind (bicycle → hoverbike) · NPCs, crowds,
traffic · save system · procedural job generation · package integrity · courier reputation ·
weather · **Linux** (all three build agents are Windows; there is no Linux agent) · any district
beyond the one megablock.

---

## Workstreams

| # | Workstream | Owner | Effort | Status |
|---|-----------|-------|--------|--------|
| **1** | Nessa body + hair out of Blender into UE — export, skeleton, skinning, master-material hookup | Peter drives | L | Not started. Body mostly modelled; hairstyle outstanding |
| **2** | Character spawn pipeline — scale → morph → sockets → empty clothing step → Chaos Cloth init | split | M | Not started |
| **3** | GMC integration — locomotion, third-person camera, on the M1 WS4 `IA_`/`IMC_` assets | split | M | Not started |
| **4** | Megablock bake — one superblock verified, then the other eight, submitted to P4 | Peter drives (modal tool) | L | Not started |
| **5** | Audio — ambient bed, physical materials on baked surfaces, surface-driven footsteps through the M1 submixes | split | S | Not started |
| **6** | Main menu → Game Modes → Delivery Girl → level load → possess | Peter drives UMG, I guide | S | Not started |
| **7** | CI — GMC cooks/stages as a runtime plugin, map + character in the cook, gates stay green, frame time captured | I drive | M | Not started |

### Sequencing

WS1 and WS4 are the two long poles and they are independent, so they parallelize by owner exactly as
M1's pillars did — Peter in Blender and the editor, build scripts elsewhere.

**Two things happen first, in week one, before anything is built on top of them:**

1. **The GMC cook spike (WS7).** Empty map, GMC pawn, packaged build, does it boot and does it stage.
   The plugin itself is low-risk — actively maintained, last updated 2026-06-20, and the project is on
   the latest version — but it is the **first third-party runtime plugin the pipeline has had to
   carry**, and that path has never been exercised. Prove the path, not the plugin.
2. **The single-superblock verify (WS4).** Bake one, cook it, walk on it, confirm collision and
   physical materials survived. Everything about the remaining eight is cheaper after that answer.

Then WS1 → WS2 → WS3 on the character side, WS5 behind WS4 on the world side, WS6 last.

### Risks

- ~~**The bake may discard physical materials.**~~ **RETIRED 2026-07-28 — checked, and the answer is
  better than feared.** The first megablock is baked (16 static meshes under `/Game/MGA/RoadBld`,
  dynamic roads retained for regeneration). **Collision survived correctly**
  (`CTF_UseComplexAsSimple`, empty `AggGeom` — render geometry is the collision, which is right for
  roads). **Physical materials are absent — but the bake did not drop them; they were never
  authored.** The shared parent `M_Master_Ground` is a hand-authored asset predating the bake and
  carries none either.
  **Consequence: WS5 gets cheaper, not more expensive.** Neither a bake-settings fix nor
  nine-superblock re-authoring is needed — physical materials attach at the **material** level, so
  assigning them to the six material instances propagates to all 16 baked meshes and every future
  bake. And the slots are already the right granularity: `MI_Sidewalk` / `MI_Curb` / `MI_Road` are
  **separate**, so sidewalk-vs-road footsteps are distinguishable for free.
- **Nanite enabled on all 16 baked meshes (2026-07-28) — and collision must be re-verified when the
  character walks.** Every `CollisionTraceFlag` still reads `CTF_UseComplexAsSimple`, but the
  classic-LOD triangle count collapsed from 444,999 to **1,025** on one superblock, and from ~7.1 M to
  **11,515** across all 16. With Nanite on, classic LOD0 becomes the **fallback mesh** and
  complex-as-simple collision reads the fallback — so **the flag survived but the geometry it points
  at may not have**. Settings are `fallbackTarget: Auto`, `fallbackRelativeError: 1`, which on a 559 m
  mesh permits metres of deviation.
  **Deferred test (Peter's call):** when Nessa is walking, stand her on the **road** and the
  **sidewalk** and watch her feet — correct standing height on each, and whether the **kerb** still
  stops her. The kerb is the sensitive probe; a decimated fallback erases it first. The same walk also
  validates surface-driven footsteps once physical materials are assigned.
  **Remedies if it fails:** tune the fallback (lower `fallbackRelativeError`, or an explicit triangle
  percentage instead of `Auto`), or set **`ComplexCollisionMesh`** — currently `None` — which is UE's
  sanctioned way to give a Nanite mesh full-fidelity collision from a separate mesh.
- **Physical materials must be assigned across 8 material slots, not 6:** `GreyboxMat`,
  `MI_BrokenWhiteLine_Graybox`, `MI_Curb`, `MI_Road`, `MI_Sidewalk`, `MI_Sidewalk_Corner`,
  `M_Greybox_SolidWhiteLine`, `M_Greybox_SolidYellowLine`.
- **Bake coverage: 16 meshes = 9 full superblocks (the whole megablock) + 7 edge superblocks**, each
  containing its **L1 interior streets**, so block interiors are walkable. Verified by bounds —
  `12th_E__12th_N` is 559 × 471 m, matching the superblock dimension; the `L2`/`L3` suffix in an asset
  name denotes the boundary road at that corner, not the mesh contents. Corner superblocks use an
  `MI_Sidewalk_Corner` variant; the curb/road split is unchanged.

> **This bake is expected to be re-authored later** (Peter, 2026-07-28): *"this PCG is only the first
> quick and dirty"* — the likely path is pulling each baked superblock into Blender to remesh or tidy
> it. Not now, and not an M2 concern, but three constraints make that round-trip cheap if they are
> held from the start:
>
> 1. **Assign physical materials at the MATERIAL level, never as per-mesh overrides.** Material
>    assignments live on the material instances, so they survive a mesh being replaced. The only thing
>    that breaks them is **material slot names changing on re-import** — keep `MI_Road`, `MI_Curb`,
>    `MI_Sidewalk`, `MI_Sidewalk_Corner` stable through any re-author.
> 2. **Keep the baked asset names stable.** They encode location and the level actors reference them
>    by path. Replacing a mesh in place under the same name needs no level edits; renaming means
>    re-linking every actor.
> 3. **Separate the two motives for remeshing.** If it is triangle count, **Nanite likely makes it
>    moot** and is currently disabled — settle that first, and take the C5 frame-time number with it
>    on. If it is topology quality, UVs, or authoring control, Nanite does not help and remeshing is
>    the real answer.
- **Bake output volume and frame cost are genuinely unknown.** This is the milestone's purpose rather
  than a risk to eliminate, but a bad enough number could shrink the baked area.
- **The road bake is modal-only.** There is no headless API — the commandlet path crashes under
  `-nullrhi`, and each `DynamicRoadNetwork` is one superblock. M2 accepts a **one-time manual bake**
  with the output committed to P4: CI never re-bakes, so the missing automation is a future
  optimisation rather than a blocker.
- **Hair authored without a bone chain would be an expensive mistake.** Retired as a *simulation*
  risk — hair is rigid in M2 — but the export decision is live and one-way: static-mesh hair means
  re-authoring and re-exporting every hairstyle in M3. See Pillar A.
- **Nessa's mesh is in Blender, not UE.** "Mostly ready" plus a hairstyle is the *art*; export,
  skeleton, and skinning are engineering that has not started.
- **GMC is a runtime dependency forever.** Unlike CityBLD/RoadBLD it cannot be disabled at cook, so
  it joins the shipping surface permanently. Fallback is `CharacterMovementComponent`, which costs
  feel but not schedule.

---

## Related

- [CoreX-M1 — Front-End Framework + DLC Load/Override + CI Pak Pipeline](M1-framework-and-ci.md)
- [CB — Jobs](../creative-briefs/CB-jobs.md) — UC2 is the courier loop Delivery Girl is built from
- [Character Pipeline](../gdd/character-pipeline.md) — body mesh, hair sockets, clothing, spawn order
- [Art Direction](../gdd/art-direction.md) — master materials and palette the character feeds into
- [CB — Camera](../creative-briefs/CB-camera.md) · [CB — Controls](../creative-briefs/CB-controls.md)
- [TB — Controls Rebind](../technical-briefs/TB-controls-rebind.md) — the `IA_`/`IMC_` assets WS3 drives from
- [TB — CI Cook](../technical-briefs/TB-ci-cook.md) — cook stages, pak routing, audit gate
- [Content Architecture & DLC](../gdd/content-and-dlc.md) — tiers as GameFeature plugins, Steam boundary
