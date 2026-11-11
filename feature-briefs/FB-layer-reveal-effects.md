# Feature Brief — Layer-Reveal Effects (Transformation Sweep + Holo Scanner)

> **Status: nice-to-have (added 2026-11-10).** Not scheduled, not a CoreX item. Recorded so the
> cheap enabling decision (a shared reveal hook in the character master materials, see *The One
> Thing Worth Doing Early*) is made before the clothing pipeline is built, not retrofitted after.

---

## Overview

Two visual effects that share one technique. In both, **a moving geometric boundary decides, per
pixel, which layer of a character is drawn**:

| Effect | The boundary | Which side shows what |
|---|---|---|
| **A. Transformation Sweep** | A horizontal band travelling up or down the body | Old outfit on one side, new outfit on the other, a glowing seam at the band |
| **B. Holo Scanner** | A transparent window (a world-space rectangle) between the camera and the character | Normal clothing outside the window; a **scan layer** seen *through* the window. Clothing stays visible above and below where the window covers |

**Inspiration (reference only, not a spec):** two videos by Sakura Rabbit, made in Unity:

- *Unity3d Change Clothes*: https://www.youtube.com/watch?v=8xFAHuY9-eE. A field travels up and down the body, changing the outfit while the character dances.
- *Unity Holo Window*: https://www.youtube.com/watch?v=h6aYBD04PJo. A transparent window shows the character's body behind it, while clothing stays visible beyond its edges.

Neither video gives implementation details. The UE approach below is our own. We want the
*effect*, not a copy of either video's look.

**Related documents:**

- [CB — Clothing](../creative-briefs/CB-clothing.md): the pocket-dimension rule for civilian clothes on transformation
- [CB — Cutscenes](../creative-briefs/CB-cutscenes.md): transformation sequences are cinematics; exit state and skip target
- [Character Pipeline](../gdd/character-pipeline.md): body, hair and clothing layers; the open question of whether transformation changes the back hair
- [Content Architecture & DLC](../gdd/content-and-dlc.md): Core/Spicy coverage standard and the Core damage floor
- [Audio Direction](../gdd/audio-direction.md): "the music earns the transformation"
- [CoreX-M3 — Candidates](../milestones/M3-candidates.md): the clothing pipeline, where the reveal hook would land

---

## A. Transformation Sweep

### What the player sees

Meghan transforms into Ava. A band of light, styled as **Ava Blue**, passes along her body while she
moves through the transformation choreography. Behind the band the civilian clothes are gone; ahead
of it they are still there. The uniform, hair and accessories resolve in the band's wake. The reverse
(detransform) runs the band the other way. Each magical girl's band uses her own colour (RGB then
CMY).

The choreography is **authored animation**, not a pose, so the effect has to hold up while the body
is moving. That is the part that makes the Sakura Rabbit reference fit, and it is why the boundary
has to be computed against the moving body rather than baked.

### Diegetic framing

[CB — Clothing](../creative-briefs/CB-clothing.md) already settles that **civilian clothes enter the
pocket dimension on transformation**. The band is that boundary made visible: **the aperture of the
pocket dimension passing over her.** Clothes are not dissolving. They are being *stored*, and the
uniform is being *retrieved*. The same reading covers the reverse direction.

### Variants worth supporting

- **Direction**: up, down, or from the centre outward (two bands). Per-character preference is a
  cheap expressive knob.
- **Multiple passes**: a fast first pass for the clothes and a slower second pass for hair and
  ornaments, for example.
- **Gameplay-speed version**: a short (≈0.5 s) sweep for in-combat transformation, with the full
  cinematic reserved for first transformations and set pieces. A full sequence every time wears thin.

### Content-tier rule

The band must **never show more body than the tier allows**:

- **Core:** the seam has **zero gap**. Old outfit directly above, new outfit directly below, with
  the band's emissive glow covering the boundary. No bare body is drawn at any frame. Core also never
  exceeds the minimum-cover standard *mid-transition*: if both outfits meet it, every frame of the
  sweep does too.
- **Spicy:** may open a gap between the two outfits (the genre-standard transformation silhouette).
  That gap shows the body and so belongs to the Spicy tier. **Its assets and parameters must live only
  in the Spicy pak**, and the leak-audit gate applies.

---

## B. Holo Scanner

### What the player sees

A transparent panel (a holographic screen, a security arch or a transit-gate pane) sits between the
camera and the character. Through the panel, the character's covered layer is replaced by a **scan
layer**. Above and below the panel's edges, and anywhere the panel does not cover, the character looks
normal. When the character walks through, the scan region slides along the body.

The obvious reference is the **subway scanner in *Total Recall* (1990)**: walk behind a screen and
become an X-ray skeleton. It suits the cyberpunk setting: in Terridyn City, being scanned is routine.

### What the scan layer shows (content-tier rule)

**Core shows no body.** In the Steam build the scan layer is the **skeleton plus cyberware**: implants,
concealed weapons, contraband and anything the scanner is built to find. This is both the Total
Recall reference and the more useful layer for gameplay. It also makes the effect safe in Core by
design.

- **Spicy** may add a body-silhouette scan mode (a scanner tuned for "imaging" rather than
  "security"). This mode is Spicy-only, so its assets live in the Spicy pak and fall under the
  leak-audit gate.
- Using the Core **base body** as the scan layer is ruled out. The shared base mesh is featureless,
  but a scanner that "sees through clothes" still reads as a Spicy beat even when there is nothing to
  see.

### Gameplay hooks (ideas, not commitments)

- **Transit and corporate checkpoints**: scanner arches on the subway and at corp-campus gates.
  Carrying a flagged item means rerouting, hiding it, or hacking the scanner.
- **Signal parity:** scanners are part of the world's perception, under the same rules as the
  player. Guards act on what the scanner actually sees, not on omniscient data.
- **Magic vs scanners:** whether pocket-dimension items, the wand or a mana aspect show on a corporate
  scanner is already an open question (`mgaPriv/world/mana-aspects.md`). This effect is where
  the answer would become visible to the player. Decide it there, not here.
- **Delivery Girl:** a parcel-scanner gate at a corp lobby is a natural low-stakes first use.

---

## How It Could Work in UE (sketch)

Both effects are **masked-material tests on the character's layers**, driven by a few parameters.
Neither needs render-target tricks or a second camera.

### Shared mechanism: a "LayerReveal" material function

Every character-layer material (body, clothing, hair, accessories, scan layer) calls one material
function. The function returns an **opacity-mask contribution** and an **emissive seam**, and each
layer tags which side of the boundary it belongs to (`Outside` / `Inside`).

- **Clothing is already Masked.** The clothing damage dissolve ([CB — Clothing](../creative-briefs/CB-clothing.md),
  Perlin noise against `DamageState`) is a per-pixel on/off test, so `M_Master_Cloth` is a **Masked**
  master regardless of this brief. LayerReveal is **one more factor in the same Opacity Mask**:
  `OpacityMask = Primary.A × DamageDissolve × LayerReveal`. Every term is 0 or 1, so multiplying is
  "visible only if all agree". Nothing about this brief changes the blend mode. It adds one term.
- **The inverse-hull outline must use the same mask.** The hull is a separate shell with its own
  material. If it does not evaluate the same damage noise (same seed, same UV space) and the same
  reveal test, a torn or swept-away region leaves its outline floating in the air. Build the mask as one
  function that both the surface and the hull call.
- **Holes expose the inside of the garment.** Through a tear you can see the inner face of the far
  side of the garment, so cloth needs two-sided rendering (or an authored inner shell). This also comes
  from the damage system, not from this brief.
- **Per-actor parameters go through Custom Primitive Data**, not one Dynamic Material Instance per
  slot. A character has many components and material slots. CPD is set per component without
  creating material instances, so the sweep can drive every layer from one place. The damage
  dissolve's per-instance noise seed and `DamageState` belong in CPD as well. **Allocate the CPD
  slots for both systems together**, so they don't collide.
- **Per-world parameters** (scanner window transforms) go in a **Material Parameter Collection**.
  Support a small fixed number of active windows, for example the four nearest to the camera.

### A. The sweep test

- Boundary height `h` in **actor-local space** (`AbsoluteWorldPosition` minus actor location, along
  actor up). Measuring locally keeps the band attached to the character while the root moves through
  the choreography. A height measured on the *rest* pose would also hold through limb motion. Choose
  between them by how it looks with real choreography.
- Old-outfit layers clip where `z < h`, new-outfit layers clip where `z > h`, and an emissive term
  is added where `|z − h| < bandWidth`. In Core the two clips meet exactly (no gap). In Spicy the gap
  is a parameter.
- During the transition, **both outfits are attached at once**, with the new outfit using the
  leader-pose component on the same skeleton. **Chaos Cloth on the incoming outfit must be simulating
  and settled before the band reaches it**, so pre-warm it out of view, or the first revealed frames
  will show cloth snapping.
- **Niagara** draws the ring itself. Sample the skeletal-mesh surface near height `h` to spawn sparks
  that hug the body.
- **Sequencer drives `h`** for cinematic transformations, keyed in the same sequence as the
  choreography and the music cue. Gameplay transformations use a timeline. The cinematic's exit state
  (CB — Cutscenes) is simply "new outfit only, old outfit detached", which also makes it a clean skip
  target.

### B. The window test

- The scanner window is a planar rectangle (transform + extents in the MPC). Per pixel, **cast the
  ray from the camera to the pixel's world position and test whether it crosses the rectangle** before
  reaching the pixel. If it does, the pixel is "behind the window".
- Normal layers clip *inside* that region and the scan layer clips *outside* it. That one test
  produces "clothing visible above and below the window" with no stencil work.
- The **scan layer** is a skeletal mesh (skeleton + cyberware) following the character through the
  leader pose. It is always attached to characters near a scanner and is invisible except where the
  test reveals it. Flagged items carry their own scan-layer mesh.
- The window's own glass (scanlines, a tint, a faint refraction, edge glow) is ordinary translucent
  geometry drawn on top.
- *Alternative considered:* custom-depth stencil plus a post-process composite. It is more flexible
  for full-screen looks, but it cannot reveal geometry that was never drawn and it pushes the effect
  into post. The per-material ray test is simpler and stays per character.

### Cost and risk notes

- **Masked materials on skeletal meshes** cost more than opaque ones, and with Nanite skeletal meshes
  in 5.8 masked materials use the programmable rasteriser path. Clothing pays that cost **anyway**,
  because the damage dissolve already makes it Masked. The extra cost of LayerReveal is a few ALU ops
  in a mask that already exists. **Measure the Masked-clothing cost once, as part of the clothing
  pipeline**, and do not treat it as a cost of this feature. The body and the scan layer are the only
  layers this brief makes Masked that would otherwise be opaque.
- **Core damage floor:** underwear cannot be damaged in Core, but it still takes the reveal term, so
  the transformation sweep works on it. Clamp damage there and leave the reveal term active.
- **TAA/TSR ghosting** on a fast-moving emissive seam: test it early with motion.
- **Hair**: the hair materials need the same hook. If the transformation also swaps the back-hair
  mesh (an open question in the Character Pipeline), the swap happens behind the band.

---

## The One Thing Worth Doing Early

The effects are nice-to-have, and so is the work to build them. **The hook is not.** When the
clothing pipeline's master materials are built (an M3 candidate), add the `LayerReveal` function
call and the CPD slots to the master materials from the start, as a no-op by default. That is the
same work as building the damage dissolve: the same Opacity Mask, the same CPD-driven parameters,
the same hull-mask sharing. Build the dissolve as a combined opacity-mask function with the reveal
term stubbed in. If the hook is
in the masters, both effects become a parameter-driving job later. If it is not, both mean
**retrofitting every outfit, hair and body material** in the game.

---

## Open Decisions

- **Sweep direction and timing per magical girl** (part of each character's transformation design).
- **In-combat transformation**: a short sweep or the full sequence, and whether the player can skip it.
- **Do magic items show on a scanner?** Tracked in the mana-aspects notes. This brief only provides
  the way to show the answer.
- **Spicy scan mode**: include it or not. If included, it ships only in the Spicy pak.
- **Sweep reference frame**: actor-local height or rest-pose height. Decide after testing with real
  choreography.

---

## Cost Notes

- **Shader work** is small and fully reusable: one material function, two tests, a few parameters.
- **Asset cost is the scan layer**: a skeleton mesh compatible with the MetaHuman skeleton, plus a
  scan-layer mesh for each piece of cyberware and each flagged item. The skeleton is built once and
  shared, because all characters use the same skeleton.
- **Choreography** for transformation sequences is animation work the game needs anyway. The sweep
  adds no animation cost.
