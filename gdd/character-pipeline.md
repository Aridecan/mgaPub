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

---

### Proportions

The base body is built to a **head-count ratio of 7.29**, measured off the flattened mesh
2026-10-02. **Canonical height is Meghan at 170 cm**; every other character is this mesh under
uniform scale.

Head count = total standing height / head height, where the head *includes* the skull and the
total *includes* the head. It is the primary proportion control for a stylised anime figure.

| | Value |
|---|---|
| Crown (`Face` max Z) | +3.7001 |
| Chin | -1.9884 |
| Sole | -37.7836 |
| Head height | 5.68851 |
| Stature | 41.4837 |
| **Head count** | **7.293** |
| Crotch fraction | 0.518 |

Because every female character shares one base mesh differentiated only by uniform scale, **the
head count and every foot ratio below are cast-wide constants, not per-character values.**

| Character | Height | Scale |
|-----------|--------|-------|
| Tierney | 147 cm | x3.54356 |
| Daisy | 168 cm | x4.04978 |
| **Meghan** | **170 cm** | **x4.09800** |
| Celeste | 177 cm | x4.26674 |
| Ava | 178 cm | x4.29084 |
| Jennifer | ~192 cm | x4.62832 |

**Regenerate these factors from the final mesh.** They are computed as `height / stature`, and
stature shifts slightly with every sole-level edit (41.4837 on 2026-10-02, 41.4456 after the
lateral-arch pass). Current values are good to ~0.1%, which is under 2 mm on a 170 cm character,
but the table should be recomputed once the mesh is joined, cleaned and final.

#### Measurement Protocol

Reproduce exactly or the number is meaningless:

1. **Orthographic viewports only.** Perspective foreshortening invalidates the measurement.
2. **Measurement lines must be axis-aligned vertical.** An angled line reports the hypotenuse and
   always reads long — this inflated an early reading to 7.27 before the lines were corrected.
3. **Head** = crown of skull to chin point (the lowest silhouette point of the jaw), front ortho.
4. **Total** = sole to crown, side ortho.
5. **Hair and clothing meshes hidden.** Anime hair silhouettes add 10-20% and inflate the count.
6. **The head is a separate object** until joined. `Face` carries the crown; `Body` stops at the
   neck. A total taken from `Body` alone excludes the head entirely. This also means scaling
   `Body` alone *does* change the head count while they remain separate objects.
7. **Check the foot pose before trusting any total.** The body was originally authored with the
   foot in a 25-degree high-heel pose, where the mesh's lowest vertex is the ball, not the heel —
   worth 0.30 of head count. **Front ortho cannot reveal plantar flexion.** The foot has since been
   flattened (2026-10-02) so the datum is now unambiguous, but take totals in side ortho regardless.

#### Target Band

**7.0-7.5 is the target range for adult female characters.** Reference points: measured real adult
female 7.0-7.5, fashion-illustration ideal 8.0-8.5, shoujo/josei 7.5-8.0, seinen/action 7.0-7.5,
moe 5.5-6.5. Below 6.5 a figure reads as a teenager regardless of stated age — a hard constraint
given MGA's cast is explicitly 18+.

7.29 sits mid-band: unambiguously adult, stylised but not fashion-plate. The crotch fraction of
0.518 is likewise inside the stylised-anime range of 0.50-0.53 (measured real adult female is
0.47-0.48), so the legs are long by design without being out of register.

#### Foot Sizing

Measured 2026-10-02 and **accepted as-is.** The foot's internal proportions sit inside the real
adult-female bands; it is uniformly ~8% small against stature, which is normal stylisation.

| | Units | cm @170 | / stature | Real adult female |
|---|---|---|---|---|
| Foot length | 5.7944 | 23.75 | 0.1397 | 0.152 |
| Ball breadth | 2.1260 | 8.71 | 0.0512 | 0.056 |
| Heel breadth | 1.3478 | 5.52 | 0.0325 | 0.038 |

| Scale-free check | Measured | Target | |
|---|---|---|---|
| breadth / length | 0.3669 | 0.36-0.38 | ok |
| heel breadth / length | 0.2326 | 0.23-0.25 | ok |
| foot length / head height | 1.0186 | ~1.0 artist rule, 1.13 real | slightly short |

At 170 cm this is a 23.75 cm foot, roughly a US women's 7 where that height averages nearer a 9.
Stylised anime runs 0.13-0.15 of stature, so the foot sits at the small end of normal rather than
outside it. **A foot that measures ~1.0 head long reliably looks too long in the viewport** — the
artist rule is counter-intuitive, and the eye is the unreliable instrument here, not the mesh.

To re-measure: foot length is the **horizontal** heel-to-toe projection in side ortho, not the
distance along the sloping top surface. Ball breadth is the widest X across the metatarsal heads,
roughly 25-45% back from the toe. Malleolus height is to the ankle *bone*, which sits well below
the narrowest cross-section of the leg.

#### Where the Stylisation Lives

At realistic scale a 7.29 figure carries a ~23 cm head — essentially real-world adult size. The
anime read comes from **leg length, ribcage narrowing and facial features**, not from an enlarged
cranium. Chasing a lower head count by shrinking the skull produces a pinhead, not a stylised
character; take it out of the legs instead.

Proportions were guided by the Reallusion base female mesh (realistic, ~7.5 heads), with legs run
longer than that reference after a 25% reduction from the source tutorial's proportions.

#### Remaining Pipeline Order

The foot is flattened, so the bind pose will be flat by construction. What remains, in order:

1. **Apply object scale on `Body` (1.0377) and `Face` (1.0) before joining.** Joining adopts the
   active object's transform; unapplied scale on a skinned mesh produces bad weights and wrong
   bone lengths on FBX import.
2. **Apply the Mirror modifier before skinning.** Mirrored geometry inherits vertex groups without
   L/R flipping unless the modifier's vertex-group option is set and bones follow the `_l`/`_r`
   convention — the classic failure is a mirrored left leg weighted to right-leg bones.
3. **Join head and body, then rig and skin.** Three traps at the join:
   - `Ctrl+J` keeps only the **active** object's modifier stack. Both `Body` and `Face` carry a
     Mirror, so the non-active one is discarded and half that mesh disappears — hence applying
     both Mirrors in step 2, before the join rather than merely before skinning.
   - **The neck interpenetrates rather than butt-joining.** `Face`'s neck rim sits at z -2.5108,
     1.12 below `Body`'s neck top at -1.3891, so the body's neck runs up inside the head. Delete
     the overlapping band on one side, then bridge. Loop counts look mismatched (~11 half-verts on
     the face rim against ~6 on the body neck) — verify in the editor, since the mirror seam
     contaminates an automated boundary count, and match them before bridging. Poles and triangles
     at the neck are high-deformation geometry that is permanently on camera.
   - **Two skin materials meet at that seam.** `Body` carries `Skin`, `Face` carries `[p]Skin`
     plus seven more. Joined, that is 10 material slots and 10 draw calls per character, with the
     material boundary at the neck. Consolidate the two skin materials unless the split is
     deliberate, and drop `Reference Transparent Eye Shadow`, which reads as reference-only.
   - Whether `Eyes`, `Teeth` and `Tongue` join at all is a separate decision. MetaHuman keeps them
     as separate meshes; eyes in particular are often held out so their shader does not inflate the
     body mesh's slot count.
4. **Author the breast shape key last.** Blender cannot apply a modifier to a mesh carrying shape
   keys, so step 2 becomes impossible once the morph exists.
5. **Move the mesh so the sole sits at Z = 0 before export.** UE skeletal meshes conventionally
   take their origin on the floor between the feet; the mesh currently has its sole at -37.7836.
   Verify facing and scale against the UE mannequin on first import.

**Blender to UE units:** UE is centimetres, 1 uu = 1 cm, and its character capsule, gravity and
step-height defaults assume a ~180 cm mannequin. Blender's FBX exporter re-scales on the way out,
which classically lands the character 100x off. Export with **Apply Scalings: FBX All**.

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

- Whether the 25% leg reduction was applied on the vertical axis only or uniformly. A uniform scale would have thinned the legs by the same 25%, which is a silhouette change rather than a proportion change
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
