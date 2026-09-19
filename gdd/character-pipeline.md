# MGA — Character Pipeline

---

## Overview

MGA uses a unified character pipeline designed for a stylised anime aesthetic. All female characters share a single body mesh; variation comes from uniform scaling, a single shape key, and modular attachments (hair, clothing, accessories). This keeps animation, physics, and rigging costs constant across the entire cast.

*See [Art Direction](art-direction.md) for the rendering pipeline, colour palette, and material system that all assets feed into.*

---

## Body Mesh

All female characters use **one base body mesh**. Character differentiation is achieved with two parameters, both set at actor instantiation and never changed at runtime:

| Parameter | Purpose |
|-----------|---------|
| **Uniform XYZ scale** | Determines character height; uniform scaling preserves animations, skin weights, collision volumes, and Chaos Cloth stability |
| **Breast size shape key** | Single morph target for bust variation |

Because scaling is uniform (X = Y = Z), all animations play identically on all characters with no retargeting required. Clothing and hair inherit the same scale and morph values at spawn.

**Why this works:** Stylised anime proportions are nearly identical across characters — exaggerated silhouettes tolerate uniform scaling, and clothing renders as rigid stylised shells rather than clingy fabric. This approach matches the architecture used by Genshin Impact, Honkai Star Rail, and Blue Protocol.

### Skeleton

The body mesh rigs to a **MetaHuman-compatible skeleton**, not Manny/Quinn (decided 2026-08-22).
MetaHuman's body skeleton is a measured superset of the UE5 mannequin's deforming skeleton —
all 150 of Manny's real joints appear in it under identical names — so the choice costs nothing
in animation compatibility and adds per-toe articulation plus compatibility with MetaHuman-targeted
animation, Control Rig and Fab assets.

Two consequences worth knowing before rigging: **MetaHuman carries no IK bones**
(`ik_foot_root`, `ik_hand_gun`, `weapon_l/r` and friends must be added back), and the toe joints
hang off `ball_l`/`ball_r` at identity, so standard locomotion drives them rigidly and deforms
exactly as a single toe bone would.

The reasoning parallels the hair strand-bone chain below: author the bones now, defer the decision
about what drives them. The skeleton is shared by every body variant and every garment, so adding
joints later means re-skinning everything authored in between.

**Full bone checklist and rig-time setup steps: [TB — Character Skeleton](../technical-briefs/TB-character-skeleton.md).**

---

## Hair System

### Modelling Technique (2AM Method)

Hair meshes are built in Blender using a skull-cap extrusion technique (derived from the 2AM YouTube workflow):

1. Start with a cube; subdivide ×3 to create a sphere
2. Cut down to a quarter from the side view
3. Split every second vertex on the lower side
4. Select all; extrude by normals inward (establishes scalp shell thickness)
5. Extrude downward — the split vertices produce separate hair strands
6. Separate strands from the base mesh and reparent
7. Modify strands for the target hairstyle
8. Mark sharp edges and seam edges per strand
9. Reattach strands to base mesh

The result is a **single manifold mesh** with clean topology, unified UVs, and approximately **2/3 fewer triangles** than the traditional method of pulling nurbs curves from the skull surface.

Additional strands are added by deleting a face, extruding the top edge, joining sides, and repeating the strand steps for that smaller section.

### Two-Socket Head System

The character head has two sockets for modular hair attachment:

| Socket | Role | Behaviour |
|--------|------|-----------|
| **Socket_FrontHair** | Signature bangs and framing strands | Fixed per character; defines facial silhouette and identity; not player-customisable |
| **Socket_BackHair** | Back hairstyle | Modular; player- or character-selectable; swappable at runtime |

All front hair pieces share the same Blender object origin (snapped to a `HairOrigin_Front` empty). All back hair pieces share a separate origin (snapped to `HairOrigin_Back`). This guarantees every piece aligns perfectly to its socket with zero per-style offset adjustments.

**Workflow:** A base hair block is built through step 4 of the 2AM method (up to the inward extrusion), then the origin is set. All hairstyle variants are duplicated from this base object, inheriting the correct origin.

### Hair Physics — Chaos Cloth

**Hair ships as a skeletal mesh with a strand/tail bone chain present, weighted, and not necessarily simulated** (decided 2026-07-28). Chaos Cloth is the intended simulation method and remains the default, but the bone chain is authored regardless.

The reasoning is optionality, not necessity. Chaos Cloth requires a skeletal mesh, but only needs **a** bone — the solver drives the painted vertices, not the skeleton. The chain is therefore insurance: it costs almost nothing at authoring time because the 2AM skull-cap method already produces strand topology that chains cleanly, and it keeps bone-based hair available without re-authoring. Hair authored as a plain static mesh on a socket would mean re-authoring, re-exporting and re-fitting **every hairstyle built by then** if the simulation method ever changes. Unsimulated bones render identically to rigid — they simply never move.

Simulation method per region:

| Region | Physics |
|--------|---------|
| **Front hair** | Mostly rigid or minimal secondary motion — bangs moving too much looks wrong in anime style |
| **Back hair** | Fully simulated with Chaos Cloth; each back hairstyle has its own Cloth asset with tuned stiffness springs |

Chaos Cloth pins at the socket root; simulation begins 1–2 loops away from the attachment point. Because body scale and shape key are applied once at spawn (before physics initialisation), Chaos Cloth tethering remains stable and vertex positions never shift mid-simulation.

### Hair Variation Targets

| Category | Count | Notes |
|----------|-------|-------|
| **NPC front styles** | 10 | General-purpose bangs and framing variations |
| **Signature front styles** | 6 | One each for the 5 magical girls + one additional key character; only these characters use these styles |
| **NPC back styles** | 10 | Short bobs, medium layered, long straight/wavy, ponytails (high/mid/low), 2 braid types |
<details>
<summary>⚠️ Story Spoiler — Signature hair assignment</summary>

The additional key character with a signature front hairstyle is Tierney.

</details>

| **Launch NPC combinations** | 100 | 10 fronts × 10 backs; mixed with palette colour variation for sufficient crowd diversity |
| **Aspirational target** | 2,500 | 50 fronts × 50 backs (long-term expansion goal) |

NPC hair length range: chin-length to mid-back. No extreme hairstyles at launch (drills, mohawks, shaved sides deferred to future expansion).

### Hair Under Headwear

**Headwear hides only the hair it covers; the rest stays visible and is shaped to sit under it** (decided 2026-09-19). The legacy approach, hiding all hair whenever a hat is equipped, is not used.

This section is an **authoring contract**, and it is fixed now because retrofitting it means re-exporting every hair piece and hat built by then. The same reasoning applies as for the strand bone chain.

Three mechanisms combine:

| Mechanism | What it solves | UE implementation |
|-----------|----------------|-------------------|
| **Region hiding** | Hair the headwear physically covers | Each hair region is its own material section; toggled with `USkeletalMeshComponent::ShowMaterialSection` (per LOD) |
| **Class compression morph** | Visible hair flaring out as if nothing were on the head, e.g. strands below a beanie | One morph target per hair piece per headwear **class**, set with `SetMorphTarget` at equip time |
| **Headwear collision** | Simulated back hair clipping up through a brim or helmet | The headwear's physics asset is added as a Chaos Cloth collision source on the hair (`AddClothCollisionSource`, to be verified on 5.8) |

#### Headwear Classes

A class is defined by **how the headwear meets the head** (its junction geometry), not by what it looks like. Every hat in a class shares one compression morph, so the authoring cost scales with the class count, not the hat count.

| Class | Junction | Examples | Default hidden regions | Morph |
|-------|----------|----------|------------------------|-------|
| **None** | — | Bare head | — | — |
| **Band** | A horizontal ring round the head at brow height | Baseball cap and anything else that meets the head at a horizontal band: beanie, fedora, bucket hat, beret, flat cap | Cap, Crown | `Hat_Band` |
| **Arc** | A narrow line over the top of the head, ear to ear | Over-ear headphones, headsets, comms rigs, Alice band | — | `Hat_Arc` |
| **Strap** | A thin band round the back of the head, below the Band line | Goggles, respirators and face masks, visors, AR glasses with a rear band | — | `Hat_Strap` |
| **Shell** | A rigid dome over the whole skull | Bike and scooter helmets (open-face), motorcycle helmets (full-face), riot and tactical helmets | Cap, Crown (+ Fringe for full-face) | `Hat_Shell` |
| **Hood** | Soft fabric draped over the skull and down the back | Hoodie hood up, cloak hood, rain poncho | Cap, Crown, Fall | `Hat_Hood` |

**Arc and Strap are the cyberpunk additions.** Headphones, headsets, goggles, respirators and AR visors are everyday street wear in the setting, and they are the most common headgear that does **not** hide any hair. Only the compression morph applies to them: a line of hair pressed in where the band sits.

**Hidden regions are set per hat, not per class.** The class column above gives the default. A hat can override it. For example, a full-face helmet adds Fringe, and a fabric headscarf or wrap is a Shell hat that hides everything. This keeps rare cases from becoming new classes.

#### Hair Regions

Every hair piece, front and back, is split into material sections at two cut lines on the shared skull:

| Region | Piece | Covers |
|--------|-------|--------|
| **Cap** | Back | The scalp shell from 2AM steps 1–4 |
| **Crown** | Front + back | Strand lengths above the Band line |
| **Fall** | Back | Back-hair strand lengths below the Band line |
| **Fringe** | Front | Bangs and framing strands below the Band line |

The **Band line** is a single ring defined once on the base head, from the brow line, over the ears, to the occipital. It lives in the Blender hair template beside `HairOrigin_Front` and `HairOrigin_Back`, and every piece is cut against it. For the same reason as the shared origins, no per-style adjustment is needed.

A strand crossing the Band line is split at the nearest edge loop into two sections. It stays one continuous mesh and one bone chain.

#### Class Envelopes — The Contract on Both Sides

Each class (except None) has a **class envelope**: a generic proxy surface for the inside of that class's headwear, authored once on the base head.

- **Hair side:** each piece's `Hat_<Class>` morph is authored by shrink-wrapping its visible regions onto the envelope, then saving the result as a shape key. This is largely mechanical, so it can be scripted like the braid construction.
- **Headwear side:** every hat's interior must sit **on or outside** its class envelope. A hat that cuts inside the envelope will clip, so it has to be refitted or moved to a different class.

The envelope is what lets any hat in a class work with any hair piece without the two being tested against each other.

#### Hair Ornaments — Out of Scope

Magical girl hair ornaments are **not** part of the generic system. They belong to that character's signature hairstyle, either modelled into it or attached to character-specific sockets on the signature hair piece. They are one-off pieces fitted by hand and need no class, envelope or morph.

---

## Clothing Pipeline

Clothing uses the **Marvelous Designer → Unreal Engine** pipeline:

1. **Design** — Garments authored in Marvelous Designer
2. **Export** — Clothing meshes exported for Unreal Engine import
3. **Chaos Cloth** — Physics simulation applied per the Marvelous Designer → UE tutorials
4. **Shape key** — Clothing meshes carry a corresponding breast size morph target copied from the body mesh; the same morph value is applied at spawn to prevent clipping

Hair is **not** made in Marvelous Designer — it is hand-modelled in Blender using the 2AM skull-cap method described above.

### Spawn-Time Pipeline

The character spawn sequence ensures consistent physics:

1. Apply uniform XYZ scale to body mesh
2. Apply breast size shape key to body mesh
3. Attach hair (front + back) to head sockets — inherits body scale
4. Attach clothing — inherits body scale; apply matching breast morph
5. Attach headwear (if any): set the hair's `Hat_<Class>` morph, hide the hat's covered regions, and register the hat's physics asset as a cloth collision source
6. Initialise Chaos Cloth on all simulated meshes

Scale and morph are applied **before** physics initialisation. This order is critical — Chaos Cloth requires stable vertex positions at init time.

**Equipping or removing headwear at runtime breaks that guarantee.** An example is taking a cap off indoors: the compression morph moves the simulated back hair's vertices mid-simulation. A headwear change therefore has to reset the hair's cloth simulation (for example `ForceClothNextUpdateTeleportAndReset`), ideally hidden behind an animation or a cut.

---

## Appendix — Blender Braid Construction

Anime-style braids are built from cylinder primitives and integrated into the skull-cap hair mesh.

### Braid Segment Construction

1. Create two cylinders (12 vertices around, 10 cm long, 0.5 cm radius) aligned along the Y axis, offset on X (centres at ~0.5 and ~1.5)
2. Add loop cuts (~7–8 evenly spaced)
3. Push loop cuts into crossing positions to create the stylised "leaf" crossover shape — inner strand arcs under, outer strand arcs over
4. Mirror across the X axis to produce a full 4-strand segment (2 right + 2 left)
5. Clean up geometry: apply mirror, add small bevel, mark sharp edges, set auto-smooth

### Integration into Hair Mesh

1. On the skull-cap base mesh, identify 4 adjacent faces where the braid originates
2. Delete those faces to create an opening
3. At each of the 4 resulting edges, extrude to create strand roots
4. Use the 12→4 vertex collapse (3→1 quads) to bring each cylinder strand down to 4 vertices at the root, matching the skull-cap face topology
5. Bridge or merge the strand roots into the base mesh
6. Result: manifold mesh with braid geometry integrated into the hair cap

### Array and Curve Modifiers

The single braid segment is extended into a full braid using Blender's modifier stack:

| Modifier | Purpose |
|----------|---------|
| **Array** (Fit Curve) | Repeats the segment along the braid path |
| **Curve** | Deforms the arrayed segments to follow a Bezier/Nurbs curve |
| **Simple Deform → Taper** (optional) | Thins the braid toward the tip |

The segment is designed to tile seamlessly — top and bottom edge loops match perfectly for array repetition. A Python script can automate the base cylinder creation and loop-cut positioning for consistent results across multiple braid styles.

---

## Open Items

- Exact Chaos Cloth stiffness and spring parameters per hair style category (braid, ponytail, bob, long straight)
- Whether front hair gets any Chaos Cloth at all, or is entirely rigid
- Whether back hair ultimately simulates via Chaos Cloth or via the authored bone chain. Chaos Cloth is the default and the chain is insurance; the decision lands in CoreX-M3, when the first hair simulation is actually built
- Hair style loader system design — DataTable structure for back hairstyle entries (mesh, Cloth asset, display name, unlock state, preview)
- Character creation / salon UI for hair selection
- Whether magical girl transformation switches back hairstyle (different back hair in transformed vs civilian form)
- LOD strategy for hair meshes at distance
- **Mutable evaluation for headwear.** UE's built-in Customizable Object system has Clip Mesh With Mesh, Clip Morph and tag-removal nodes aimed at hair under headwear. Per the reuse-UE-systems rule, check this before building the region and morph system above. The likely conflict is that Mutable merges parts into one generated mesh, while back hair is a socketed skeletal mesh with its own Cloth asset
- **Layered headwear**: a Band cap worn under a Hood is a signature cyberpunk look. The system currently allows one class at a time; decide whether Hood + Band is a supported combination (union of hidden regions, Hood morph wins) or a single dedicated hat
- **Hood vs. a simulated hoodie**: Chaos Cloth has no cloth-to-cloth collision, so simulated back hair cannot collide with a simulated hood. This is why Hood hides Fall by default; long hair pulled forward out of the hood would need its own hairstyle variant
- Whether scalp-mounted cyberware (neural ports, implant plates) is a headwear class, a hair region, or a no-go for characters with hair
- Whether the skeleton adopts MetaHuman's 172 finger corrective joints (Tier C in [TB — Character Skeleton](../technical-briefs/TB-character-skeleton.md)) or stops at the toe joints. Deferred to rig time; the correctives are driven by a pose-driver solver rather than by animation, and can be added later without re-skinning
