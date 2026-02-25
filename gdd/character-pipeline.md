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

Hair uses **Chaos Cloth** simulation rather than bone rigging:

| Region | Physics |
|--------|---------|
| **Front hair** | Mostly rigid or minimal secondary motion — bangs moving too much looks wrong in anime style |
| **Back hair** | Fully simulated with Chaos Cloth; each back hairstyle has its own Cloth asset with tuned stiffness springs |

Chaos Cloth pins at the socket root; simulation begins 1–2 loops away from the attachment point. Because body scale and shape key are applied once at spawn (before physics initialisation), Chaos Cloth tethering remains stable and vertex positions never shift mid-simulation.

### Hair Variation Targets

| Category | Count | Notes |
|----------|-------|-------|
| **NPC front styles** | 10 | General-purpose bangs and framing variations |
| **Signature front styles** | 6 | One each for the 5 magical girls + Tierney; only these characters use these styles |
| **NPC back styles** | 10 | Short bobs, medium layered, long straight/wavy, ponytails (high/mid/low), 2 braid types |
| **Launch NPC combinations** | 100 | 10 fronts × 10 backs; mixed with palette colour variation for sufficient crowd diversity |
| **Aspirational target** | 2,500 | 50 fronts × 50 backs (long-term expansion goal) |

NPC hair length range: chin-length to mid-back. No extreme hairstyles at launch (drills, mohawks, shaved sides deferred to future expansion).

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
5. Initialise Chaos Cloth on all simulated meshes

Scale and morph are applied **before** physics initialisation. This order is critical — Chaos Cloth requires stable vertex positions at init time.

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
- Hair style loader system design — DataTable structure for back hairstyle entries (mesh, Cloth asset, display name, unlock state, preview)
- Character creation / salon UI for hair selection
- Whether magical girl transformation switches back hairstyle (different back hair in transformed vs civilian form)
- LOD strategy for hair meshes at distance
