# Creative Brief — Clothing System

---

## Overview

Clothing in MGA is a physically modelled system where every garment has real material properties, takes real damage, and participates in the inventory and combat systems. Clothing is not cosmetic — it is equipment with defensive value, container capacity, and a finite lifespan.

Every piece of clothing has a cloth type (cotton, leather, latex, etc.), a durability pool, a hardness value, a damage absorption percentage, and zero or more pockets that register as containers with the inventory system. Clothing layers on the body in an ordered stack. Damage cascades through layers from outermost to innermost, with each layer intercepting and absorbing a fraction before passing the remainder down.

Clothing degrades through two independent tracks: combat damage (partially repairable) and laundering wear (permanent). Both tracks ratchet the garment toward eventual replacement, creating a recurring economic cost.

**Related documents:**

- [Inventory Creative Brief](CB-inventory.md) — hand costs, container rules, item drop on destruction
- [Inventory System (GDD)](../gdd/inventory.md) — storage resolution, reservations, quick actions
- [Magical Girl Uniform](../../mgaPriv/mechanics/magical-girl-uniform.md) — separate defensive system for transformed state
- [Art Direction](../gdd/art-direction.md) — material system, cel shader, palette
- [Character Pipeline](../gdd/character-pipeline.md) — Marvelous Designer → UE5 clothing workflow

---

## Clothing Item Properties

Every piece of clothing carries the following data:

| Property | Description |
|----------|-------------|
| **Cloth type** | Material category: cotton, wool, leather, latex, kevlar-weave, etc. Determines interaction with damage types and chemical attacks |
| **Absorption %** | What fraction of incoming damage this garment intercepts from the cascade |
| **Hardness** | Flat damage reduction applied to intercepted damage. Effectiveness varies by damage type (see Damage Resolution) |
| **Max durability** | Starting durability ceiling. Reduced permanently by laundering (~1% per wash). Never recoverable |
| **Current durability** | Current HP. Reduced by combat damage. Partially repairable (see Repair) |
| **Permanent combat loss** | Accumulated irreparable damage from repairs. Tracked per repair event in the repair ledger |
| **Wearability threshold** | Durability percentage below which the garment can no longer stay on the body (typically ~10%). Garment drops but still exists as a repairable object |
| **Body regions** | Which body regions this garment covers. Regions are granular enough to support asymmetric clothing and partial-coverage garments (see Body Region Segmentation below) |
| **Layer** | Where in the stack this garment sits: underwear, base, mid, outer |
| **Pockets** | Zero or more declared pockets, each with its own dimensions, volume, weight capacity, and viability threshold |
| **Open/closed state** | Whether the garment is fastened. Affects pocket accessibility of layers underneath |

---

## Body Region Segmentation

Damage is localised — a hit to the torso only cascades through layers covering the torso, not the skirt on the hips. This means the body region model must be granular enough to support:

- **Asymmetric clothing:** A thigh-high stocking on one leg and a sock on the other — left and right must be independent
- **Partial coverage:** A belly shirt covers the upper torso but not the lower torso. A halter top covers less than a full T-shirt
- **Multi-region garments:** Coveralls cover torso + arms + legs. A short-sleeve shirt covers torso + upper arms. A coat covers torso + hips
- **Front/back distinction:** An open coat removes itself from front hit zones while remaining in back hit zones (see UC3)

**Planned approach — skeleton-based regions:**

Regions are derived from the UE5 Manny/Quinn skeleton (89 bones). When a collision occurs on a skeletal mesh, the engine reports the hit bone. A **bone-to-region lookup table** maps that bone to a clothing region. This is data-driven — regions can be split or merged by editing the table, no code changes required.

The spine is kept at full 5-bone granularity because the torso is where the most granular clothing coverage differences occur (panties vs. shorts, belly shirt vs. full shirt, halter top vs. T-shirt).

**Front/back detection** is handled separately from the bone lookup. A dot product of the hit direction against the character's forward vector determines front vs. back:

```
region = bone_to_region_lookup[hit_bone]
side = dot(hit_direction, character_forward) > 0 ? front : back
```

This gives every region a front/back distinction without doubling the region count.

**Bone-to-region grouping:**

| Skeleton bone(s) | Clothing region | L/R split |
|------------------|-----------------|-----------|
| `head` | head | No |
| `neck_01`, `neck_02` | neck | No |
| `clavicle_l`, `clavicle_r` | clavicle | Yes (L/R) |
| `spine_05` | spine_05 (upper chest / shoulders) | No |
| `spine_04` | spine_04 (chest) | No |
| `spine_03` | spine_03 (mid torso / ribcage) | No |
| `spine_02` | spine_02 (upper abdomen) | No |
| `spine_01` | spine_01 (lower abdomen) | No |
| `pelvis` | pelvis | No |
| `upperarm_l/r`, `upperarm_twist_*_l/r` | upper_arm | Yes (L/R) |
| `lowerarm_l/r`, `lowerarm_twist_*_l/r` | lower_arm | Yes (L/R) |
| `hand_l/r`, all finger bones `_l/r` | hand | Yes (L/R) |
| `thigh_l/r`, `thigh_twist_*_l/r` | upper_leg | Yes (L/R) |
| `calf_l/r`, `calf_twist_*_l/r` | lower_leg | Yes (L/R) |
| `foot_l/r`, `ball_l/r` | foot | Yes (L/R) |
| IK bones, virtual bones | Unmapped (inherit nearest parent region) |

This produces **21 regions** (13 centre-line regions + 8 with L/R splits = 13 + 8 = 21). Every region also has a front/back side from hit direction, giving up to 42 addressable hit zones without increasing the region count.

**Garment coverage examples:**

| Garment | Regions covered |
|---------|-----------------|
| Panties | pelvis |
| Shorts | pelvis, spine_01 |
| Jeans | pelvis, spine_01, upper_leg L/R, lower_leg L/R |
| Halter top | spine_04, spine_05 |
| Belly shirt | spine_03, spine_04, spine_05 |
| Full T-shirt | spine_01–05, upper_arm L/R |
| Long-sleeve shirt | spine_01–05, upper_arm L/R, lower_arm L/R |
| Coveralls | spine_01–05, upper_arm L/R, lower_arm L/R, pelvis, upper_leg L/R, lower_leg L/R |
| Skirt (knee-length) | pelvis, upper_leg L/R |
| Left thigh-high stocking | upper_leg L, lower_leg L |
| Right ankle sock | foot R |
| Bra | spine_04, spine_05 |
| Coat (closed) | spine_01–05, clavicle L/R, upper_arm L/R (front + back) |
| Coat (open) | spine_01–05, clavicle L/R, upper_arm L/R (back only — front zones removed; see UC3) |

**Verification required:** The assumption that UE5 hit detection on a skeletal mesh returns the nearest bone needs to be confirmed in-engine. If hit results return vertex data instead, the vertex-to-bone mapping through skin weights provides the same lookup path.

**Refinement:** The 21-region model is a starting point. If playtesting reveals that certain regions are never addressed independently (e.g. clavicle always groups with spine_05), regions can be merged by editing the lookup table. If finer granularity is needed somewhere, a region can be split by separating its bone entries.

---

## Use Cases

### UC1 — Dressing

**Actor:** The player
**Goal:** Put clothing onto Meghan's body
**Flow:** With clothing in hand or accessible in a world container → equip action → garment goes onto the body in the correct region and layer

**Rules:**

- Dressing requires the garment to be in hand first (follows UC1 of the Inventory Creative Brief — items always go to hands before anywhere else)
- Each garment declares which **body zones** it covers and which **layer** it occupies (see Body Region Segmentation above)
- Regions are derived from the UE5 skeleton bones (see Body Region Segmentation above). Left and right limbs are independent. The spine has 5 separate regions for fine-grained torso coverage. A coat covers spine_01–05 + clavicle + upper arms. A left thigh-high stocking covers upper_leg_L + lower_leg_L only
- Layers per region are ordered: underwear → base → mid → outer. A garment declares its layer
- Multiple garments can occupy the same region if they are on different layers. Panties (underwear, hips) + nylons (base, hips + legs) + skirt (mid, hips) + coat (outer, hips + torso) = four items on the hips region, no conflict
- **Layer conflicts:** Two garments on the same region AND the same layer conflict. The player must remove one before equipping the other. You cannot wear two skirts on the mid layer of the hips region
- Dressing takes real time. The world does not pause. Dressing actions have a time cost proportional to the garment's complexity (pulling on a coat is faster than lacing up boots)
- Dressing requires free hands — the number varies by garment. A simple pull-on garment needs 1 free hand; a garment with buttons, zips, or lacing may need 2
- **Layer order enforcement:** Outer layers must be removed before inner layers can be equipped or removed. You cannot put on underwear over a skirt. You cannot put on a base layer shirt if the mid layer is already occupied — the mid layer must come off first, then the base goes on, then the mid goes back on. This is the physical reality of dressing

**On equip:**

- The garment's pockets register with the inventory system as containers (see UC4)
- The garment enters the damage cascade for its covered regions (see UC5)
- The garment's layer position is recorded for damage cascade ordering (outermost first)

**Success:** Garment is worn, pockets are registered, layer stack is updated
**Failure:** Layer conflict, not enough free hands, garment is unwearable (below wearability threshold)

---

### UC2 — Undressing

**Actor:** The player
**Goal:** Remove a worn garment from Meghan's body
**Flow:** Select garment to remove → undress action → garment goes to hand

**Rules:**

- Undressing puts the garment into a hand slot. The player then decides what to do with it — stow it, drop it, place it in a world container
- Undressing requires free hands (same hand cost as dressing that garment)
- **Layer order enforcement:** Outer layers must be removed before inner layers. To remove a base layer shirt, the player must first remove any mid and outer layer garments covering the same region
- Undressing takes real time with the same complexity-based time cost as dressing
- **Forced undressing (combat):** Enemies with the Rending skill or equivalent can forcibly remove clothing. Forced removal bypasses normal time cost but is contested against Wardrobe Warding. See [Wardrobe Warding](../../mgaPriv/mechanics/skills/wardrobe-warding.md) and [Rending](../../mgaPriv/mechanics/skills/rending.md)
- When a garment is removed, its pockets deregister from the inventory system. Any items in those pockets remain inside the garment — they travel with the garment, not with Meghan. If the garment is dropped, the pocket contents go with it

**Success:** Garment is in hand, pockets deregistered, layer stack updated
**Failure:** Outer layer blocking removal, not enough free hands

---

### UC3 — Open/Closed State

**Actor:** The player
**Goal:** Fasten or unfasten an outer garment to control pocket accessibility of inner layers

**Rules:**

- Garments on the mid and outer layers can have an **open/closed state** if the garment has a fastening mechanism (buttons, zip, belt buckle, clasp)
- Underwear and base layers do not have an open/closed state — they are always effectively closed (form-fitting, no fastening to toggle)
- **Closed outer garment:** Blocks pocket access to all garments on lower layers in the same body region. A zipped coat over a skirt means the skirt's pockets are inaccessible
- **Open outer garment:** Does not block pocket access. An open coat allows reaching the skirt pockets underneath
- Toggling open/closed is a quick action — significantly faster than dressing or undressing. One hand required
- **Multiple blocking layers:** If both a mid layer and an outer layer cover the same region, the skirt pocket is blocked if *either* the mid or outer layer above it is closed. All layers above the target pocket must be open for access
- **Default state on equip:** Garments equip in their closed state by default. The player opens them explicitly

**Front/back hit zone interaction:**

Opening a garment does not simply toggle a boolean — it changes which body zones the garment participates in for the damage cascade:

- **Closed coat:** Covers upper torso front, upper torso back, lower torso front, lower torso back, hips, upper arm L/R — full protection
- **Open coat:** Removes itself from all front zones. Still covers upper torso back, lower torso back, upper arm L/R — back and arms remain protected, front is exposed

A hit to the front torso while the coat is open skips the coat entirely and cascades starting from the next layer. A hit to the back still passes through the coat first. This makes opening a coat a genuine tactical decision: front pocket access at the cost of front protection.

- **Combat implication:** Opening a coat to access an inner pocket mid-fight costs time AND removes front-zone protection. The player chooses between keeping the outer layer's protection sealed or opening it for inventory access. This is a real tactical trade-off

**Inventory integration:** When a pocket is blocked by a closed outer layer, it appears greyed out in the inventory screen — visible but not interactive. The player can see what is in it but cannot retrieve or store items until the blocking layer is opened.

**Success:** Garment state toggled, pocket accessibility updated across all affected layers
**Failure:** Garment has no fastening (no open/closed state), not enough free hands

---

### UC4 — Clothing as Containers

**Actor:** The inventory system
**Goal:** Register clothing pockets as inventory containers when worn, manage their lifecycle

**Rules:**

- When a garment is equipped, each of its declared pockets registers with the inventory system as a container. Each pocket has:
  - **Dimensions** (max X, Y, Z) — the largest item that can physically enter
  - **Volume** — total space available
  - **Weight capacity** — how much weight the pocket can hold
  - **Viability threshold** — the garment durability percentage below which this pocket fails and dumps its contents
  - **Hands required** — how many free hands are needed to access this pocket (typically 1)
- Registered pockets appear in the unified inventory screen alongside all other containers (body containers, hand slots, world containers in reach)
- **Pocket viability:** When the garment's current durability drops below a pocket's viability threshold, that pocket fails:
  - All items in the pocket drop to the ground at Meghan's position
  - The pocket deregisters from the inventory system
  - Item reservations for that pocket are preserved (not cleared) — the pocket is non-functional, not forgotten. After repair above the threshold, the pocket comes back online and reservations are still valid
- **Pocket quality axis:** A reinforced inner pocket with a viability threshold of 5% holds its contents almost until the garment drops. A cheap exterior pocket at 40% dumps early. Pocket quality is a meaningful equipment property — players will care about it
- Different pockets on the same garment can have different viability thresholds. A coat's reinforced breast pocket might survive at 10% durability while its flimsy side pockets fail at 30%
- **Open/closed interaction:** Pockets on inner layers blocked by a closed outer garment are inaccessible (see UC3). They still exist and hold their contents — they just cannot be interacted with until the blocking layer is opened
- **Auto-storage iteration order:** Pockets from clothing follow the same rule as the inventory system — first worn, first checked. If Meghan puts on pants before the jacket, pants pockets are checked before jacket pockets during auto-storage. See [Inventory System (GDD)](../gdd/inventory.md)
- **On garment removal (undressing):** Pockets deregister. Items stay inside the garment. If the garment is dropped or placed in a world container, the pocket contents remain with it — accessible by opening the garment as a world container
- **On garment destruction (0% durability):** Pockets are destroyed. All remaining contents drop. Reservations are cleared. The garment no longer exists

**Success:** Pockets registered and functional; items stored and retrievable; viability thresholds enforced
**Failure:** Pocket below viability threshold (contents drop), pocket blocked by closed outer layer, garment destroyed

---

### UC5 — Damage Resolution

**Actor:** The combat system
**Goal:** Resolve incoming damage against clothing layers before it reaches the personal shield and CP

**Attack properties (required on every damage source):**

| Property | Description |
|----------|-------------|
| **Damage amount** | Raw damage value |
| **Damage type** | Blunt, slashing, piercing, chemical, fire, etc. |
| **Clothing modifier** | Multiplier on damage dealt to clothing specifically (default 1.0) |

**Damage is localised by body zone.** A hit to the torso only cascades through layers covering that torso zone. A hit to the left leg only cascades through layers covering the left leg. A garment that covers the hips but not the legs is not involved when the left shin is struck. The combat system must determine which body zone is hit for every damage event.

**The cascade — one hit, all layers on the struck zone, same tick:**

Damage enters at the outermost layer covering the struck zone and cascades inward. Each layer processes the damage independently.

**Per-layer resolution:**

```
Step 1 — Intercept
  intercepted = incoming_damage × layer_absorption%
  passthrough = incoming_damage - intercepted
  → passthrough goes to next layer (or to shield if this is the innermost)

Step 2 — Clothing modifier
  modified = intercepted × attack_clothing_modifier

Step 3 — Hardness bypass (lookup: damage_type × cloth_type → bypass%)
  effective_hardness = hardness × (1 - bypass%)

Step 4 — Durability damage
  durability_damage = max(0, modified - effective_hardness)
  → applied to the garment's current durability
```

**Hardness bypass matrix — damage type vs. cloth type:**

Each combination of attack damage type and garment cloth type defines how much of the garment's hardness is bypassed. This is a data table lookup.

| | Cotton | Wool | Leather | Latex | Kevlar-weave | ... |
|---|---|---|---|---|---|---|
| **Blunt** | 0% | 0% | 0% | 0% | 0% | |
| **Slash** | 70% | 60% | 30% | 80% | 10% | |
| **Pierce** | 80% | 70% | 40% | 90% | 15% | |

*(Values are illustrative — exact numbers are a balance pass.)*

**Blunt is always 0% bypass** — fabric does not tear from blunt force; it transmits through. Slashing and piercing bypass hardness to varying degrees depending on cloth type. Leather resists cutting better than cotton. Kevlar-weave resists both.

**Overflow on destruction:** If a layer is destroyed mid-hit (durability reaches 0), any durability damage that exceeded the remaining durability is added to the passthrough for the next layer. Destroying a layer is not cheaper for the attacker.

```
Example — coat has 3 durability remaining, takes 7.5 durability damage:
  Coat destroyed. Overflow = 7.5 - 3 = 4.5
  Passthrough to next layer = original passthrough + 4.5
```

**Chemical attacks — DOT against specific cloth types:**

- Chemical damage sources (gas fields, liquid splashes) target specific cloth types
- While in a chemical field, the attack reapplies every tick (~0.5 seconds)
- Each tick runs through the same pipeline with a small damage amount
- A "cotton dissolvent" gas has near-100% hardness bypass against cotton, minimal bypass against leather — the leather coat protects while the cotton shirt underneath dissolves if exposed
- Chemical DOT continues as long as the character remains in the field

**Clothing modifier examples:**

| Modifier | Interpretation |
|----------|---------------|
| 0.5 | Clothing-gentle — rubber baton, restraint tool |
| 1.0 | Default — no special effect on clothing |
| 1.25 | Slightly clothing-aggressive |
| 2.0+ | Clothing-destroying — designed to strip defences; connects to Rending skill and curse weapon systems |

**Cascade example — full walkthrough:**

```
100 blunt damage, clothing modifier 1.25, striking hips region

Layer 4 (coat, outer): cotton, 15% absorption, hardness 10
  Intercept: 100 × 0.15 = 15       Passthrough: 85
  Modifier:  15 × 1.25 = 18.75
  Bypass:    blunt × cotton = 0%    Effective hardness: 10
  Durability damage: 18.75 - 10 = 8.75

Layer 3 (skirt, mid): wool, 10% absorption, hardness 5
  Intercept: 85 × 0.10 = 8.5       Passthrough: 76.5
  Modifier:  8.5 × 1.25 = 10.625
  Bypass:    blunt × wool = 0%      Effective hardness: 5
  Durability damage: 10.625 - 5 = 5.625

Layer 2 (nylons, base): latex, 5% absorption, hardness 2
  Intercept: 76.5 × 0.05 = 3.825   Passthrough: 72.675
  Modifier:  3.825 × 1.25 = 4.78
  Bypass:    blunt × latex = 0%     Effective hardness: 2
  Durability damage: 4.78 - 2 = 2.78

Layer 1 (panties, underwear): cotton, 3% absorption, hardness 1
  Intercept: 72.675 × 0.03 = 2.18  Passthrough: 70.495
  Modifier:  2.18 × 1.25 = 2.725
  Bypass:    blunt × cotton = 0%    Effective hardness: 1
  Durability damage: 2.725 - 1 = 1.725

→ 70.495 reaches personal shield / CP
```

**Same hit but slashing (cotton bypass 70%, wool 60%, latex 80%):**

```
Layer 4 (coat, cotton): effective hardness = 10 × 0.30 = 3
  Durability damage: 18.75 - 3 = 15.75

Layer 3 (skirt, wool): effective hardness = 5 × 0.40 = 2
  Durability damage: 10.625 - 2 = 8.625

Layer 2 (nylons, latex): effective hardness = 2 × 0.20 = 0.4
  Durability damage: 4.78 - 0.4 = 4.38

Layer 1 (panties, cotton): effective hardness = 1 × 0.30 = 0.3
  Durability damage: 2.725 - 0.3 = 2.425
```

Slashing shreds clothing far faster than blunt. The passthrough to shield/CP is the same, but the garments are chewed up.

**Relationship to the magical girl uniform:** The uniform and civilian clothing are completely separate defensive systems. They do not stack or interact.

- **Civilian form:** Damage cascades through clothing layers (this system) → personal shield → CP
- **Transformed form:** Civilian clothes enter the pocket dimension. The magical girl is treated as effectively naked under an opaque magical shield. Damage passes through the uniform's own absorption model (the 75% threshold system in magical-girl-uniform.md) → CP. The uniform covers the entire body — including areas that appear visually uncovered — as a single defensive layer with a durability pool roughly 10× that of civilian clothing (~1000 vs. ~100 for a leather jacket)

The uniform's 75% threshold means it must take 250% of its full durability in damage before any reaches CP — a level of protection civilian clothing cannot approach. This is the mechanical expression of the difference between mundane fabric and a magical construct.

**Wardrobe Warding interaction (civilian clothing only):** The Wardrobe Warding skill magically reinforces civilian clothing, increasing both hardness and absorption % on enchanted garments. This makes mundane clothing meaningfully more protective without approaching the uniform's scale. Wardrobe Warding does not apply to the magical girl uniform — the uniform has its own defensive mechanics.

---

### UC6 — Degradation

**Actor:** The game systems (passive)
**Goal:** Model the natural wear and aging of clothing over time

**Two independent degradation tracks:**

**Track 1 — Laundering wear (permanent, unrepairable):**

- Each time a garment is washed/laundered, its **max durability** drops by approximately 1%
- This is permanent. No repair, no skill, no NPC can restore lost max durability from laundering
- A garment washed 20 times has a max durability of ~80%. Even at full repair, it shows 20% dissolution visually
- Eventually, laundering alone makes clothing unwearable — a garment washed 90+ times is approaching its wearability threshold even without combat damage
- This is the baseline economic driver: clothing wears out through normal life, independent of combat

**Track 2 — Combat damage (partially repairable):**

- Damage from hits reduces current durability (see UC5)
- Combat damage is partially repairable through the Tailoring skill or NPC tailors (see UC10)
- Each repair leaves some permanent loss. Accumulated permanent combat loss is tracked in the repair ledger

**Effective ceiling:** The highest durability a garment can ever reach is:

```
effective_max = max_durability - permanent_combat_loss
```

Both tracks ratchet independently. An old, frequently-repaired shirt has low max durability from washing AND accumulated permanent combat loss from repairs.

**Visual representation — dissolve shader:**

- Each clothing material includes a **Perlin noise texture** (or procedural noise node in the material graph)
- A `DamageState` parameter drives a threshold against the noise
- Pixels where the noise value falls below the threshold have their alpha set to 0 — producing a torn, dissolved appearance
- `DamageState` is derived from the garment's current condition: `DamageState = 1.0 - (current_durability / 100)`
- The noise is per-object instance — different instances of the same shirt tear in different patterns
- The dissolve integrates with the existing material system: the Perlin noise result multiplies against the primary texture's alpha channel. The cel shader, definition texture, and inverse hull outlines all respond naturally to the remaining mesh

**Visual stages:**

| Durability | Visual |
|------------|--------|
| 100–80% | Near pristine; minimal visible wear |
| 80–50% | Visible fraying, small holes, worn edges |
| 50–30% | Significant dissolution; large torn sections |
| 30–10% | Barely holding together; mostly dissolved |
| Below wearability threshold | Drops from body |
| 0% | Destroyed — object ceases to exist |

---

### UC7 — Clothing Destruction

**Actor:** The combat system / damage cascade
**Goal:** Handle the consequences when a garment reaches its durability limits

**Two destruction stages:**

**Stage 1 — Unwearable (durability drops below wearability threshold):**

- The garment can no longer stay on the body
- The garment drops to the ground at Meghan's position
- Any pockets that haven't already failed (their viability threshold hadn't been reached yet) dump their contents at this point
- The garment still exists as a world object — it can be picked up, stored, and repaired
- After repair above the wearability threshold, the garment can be re-equipped
- Pockets that were still viable at the time of drop retain their viability status; they just need the garment repaired and re-equipped to come back online
- Item reservations are preserved

**Stage 2 — Destroyed (durability reaches 0%):**

- The garment ceases to exist
- All remaining contents drop to the ground
- All item reservations for this garment's pockets are cleared
- The garment cannot be repaired, recovered, or reconstructed
- The layer stack updates — the next inner layer is now exposed to direct damage cascade

**Pocket failure sequence (staged warnings):**

Pockets fail independently as durability drops through their viability thresholds. This gives the player staged feedback before the garment itself drops:

```
Coat: 100 durability
  Side pocket:  viable until 30%
  Inner pocket: viable until 15%
  Wearability:  10%

At 28 durability — side pocket fails, contents drop
  → Player sees items fall; knows the coat is in trouble

At 12 durability — inner pocket fails, contents drop
  → Escalating warning

At 8 durability — coat unwearable, drops to ground
  → Coat exists as a world object; repairable

At 0 durability — coat destroyed
  → Gone permanently
```

**Layer cascade on destruction:** When a garment is destroyed or drops mid-combat, the damage cascade for subsequent hits skips that layer. If the coat is gone, the next hit starts at the skirt.

**Overflow on the destroying hit:** Any durability damage that exceeded the garment's remaining durability is added to the passthrough for the next layer on the same hit (see UC5).

---

### UC8 — Unworn Clothing in the World

**Actor:** The rendering system
**Goal:** Represent clothing that is not being worn as a physical object in the world

**Investigation — cloth simulation freeze:**

The proposed approach uses Unreal Engine's cloth simulation to generate the unworn mesh procedurally:

1. The garment's worn mesh is placed in a simulation context (on a surface, in a container)
2. The cloth simulation runs briefly — the fabric settles under gravity
3. The simulation is frozen at a specific frame — the settled state becomes the unworn mesh
4. The engine determines how the mesh lays based on physics rather than hand-modelled folding

**Advantages:**

- No per-garment unworn mesh modelling required
- Different surfaces produce different settled shapes (draped over a chair vs. crumpled on the floor vs. folded on a shelf)
- Consistent with the existing Chaos Cloth pipeline — same simulation tech used for worn and unworn states
- Colour and material come from the material instance (double-index palette system) — no additional texturing

**Open questions for investigation:**

- Performance cost of running a brief cloth sim every time clothing is dropped or placed
- Whether the settled state can be cached per surface type to avoid re-simulation
- Whether a small library of pre-baked settled states per garment category (shirt, pants, coat, etc.) is more practical than live simulation
- How the dissolve shader (damage state) reads on a settled/crumpled mesh — does the Perlin noise still produce convincing tear patterns on irregular geometry?

**Fallback:** If live cloth sim proves too expensive, generic settled shapes by garment category (folded shirt, folded pants, crumpled coat, etc.) with material instance colouring provide visual variety at fixed asset cost.

---

### UC9 — Buying and Selling Clothing

**Actor:** The player
**Goal:** Acquire new clothing and sell worn clothing

**Buying:**

- Clothing is purchased from shops, market stalls, and online retailers (via phone)
- New clothing has max durability at 100%, zero permanent combat loss, and full pocket functionality
- Price is determined by cloth type, absorption %, hardness, pocket count and quality, and brand/style
- Higher-quality clothing (better cloth type, higher hardness, reinforced pockets with low viability thresholds) costs more
- Some clothing is only available from specific retailers or requires reputation access

**Selling:**

- Clothing can be sold to second-hand shops, consignment, or directly to NPCs
- Sale price is determined by the garment's current condition:
  - Max durability (laundering wear reduces resale value)
  - Current durability (combat damage reduces resale value)
  - Permanent combat loss (repair history reduces resale value — visible wear)
  - Pocket viability status
- A pristine garment sells for a significant fraction of its purchase price. A heavily worn, repeatedly repaired garment sells for very little
- Destroyed clothing (0% durability) cannot be sold — it no longer exists

**Economic pressure:** Clothing is a recurring cost (documented in [Core Loop](../gdd/core-loop.md)). Combat damages garments; laundering degrades them over time. The player must budget for wardrobe replacement alongside rent, food, and tuition. Higher-quality clothing lasts longer and absorbs more damage, but costs more upfront — a genuine investment decision.

---

### UC10 — Repairing Clothing

**Actor:** The player or an NPC tailor
**Goal:** Restore combat-damaged durability to a garment

**The Tailoring skill:**

| Field | Value |
|-------|-------|
| **Primary attribute** | Agility (AGI) |
| **Secondary attribute** | Wisdom (WIS) |
| **Overview** | Stitching, cutting, and fabric work is dexterous hand work (AGI). Knowing how to approach a repair and what is salvageable is judgment (WIS) |

**Repair quality:** A tailor (player or NPC) has a **repair quality percentage** derived from their Tailoring skill level. This determines how much of the combat damage can be restored. No tailor can achieve 100% — every repair leaves some permanent loss.

**The repair ledger:**

Every garment tracks a **repair ledger** — a record of each repair event:

```
{
  repair_id: 1,
  damage_repaired: 10,      // damage that was addressed
  tailor_quality: 0.80,      // 80% quality
  amount_restored: 8,        // 10 × 0.80
  permanent_loss: 2           // 10 - 8
}
```

**Repair flow:**

1. Garment has taken combat damage (current durability is below effective max)
2. Tailor examines the garment — the repairable amount is the difference between current durability and effective max
3. Tailor applies their quality percentage: `restored = repairable × tailor_quality`
4. Remainder becomes permanent combat loss: `new_permanent = repairable - restored`
5. Current durability increases by `restored`
6. Repair event is recorded in the ledger

**Retroactive improvement:**

When Meghan finds a better tailor, that tailor can review the repair ledger and improve previous repairs:

```
Original: 80% tailor repaired 10 damage → restored 8, permanent 2
Better:   90% tailor reviews → should have restored 9, permanent 1
Result:   1 point of permanent loss recovered; current durability +1
```

The better tailor reruns every ledger entry at their quality level. Any entry where their quality exceeds the original tailor's quality yields recovered durability. Entries already at or above the new tailor's quality are unchanged.

**Constraints:**

- **Laundering wear is unrepairable.** Tailoring addresses combat damage only. Max durability lost to washing cannot be restored by any means
- **Never 100%.** Even a near-perfect tailor leaves some permanent loss per repair. The minimum permanent loss per repair event is always > 0
- **Garment must be above 0% durability.** Destroyed clothing cannot be repaired — it no longer exists. Clothing below the wearability threshold but above 0% can be repaired
- **Repair takes time.** Complexity and damage severity determine repair duration. The world does not pause during NPC repairs — Meghan drops off the garment and picks it up later, or waits
- **NPC tailors vary.** Tailors around the city have different quality levels. Finding a 90% tailor is a meaningful discovery. The best tailors may require reputation, referrals, or higher prices
- **Self-repair:** Meghan can invest in the Tailoring skill to repair her own clothing. This saves money but costs time and skill investment. Most players will hire NPCs for routine repairs

**Example — full lifecycle:**

```
New shirt: max_durability = 100, current = 100, permanent_combat_loss = 0

Takes 10 combat damage → current = 90
Repaired by 80% tailor → restored 8, permanent 2 → current = 98
  Effective max = 100 - 2 = 98

Washed 5 times → max_durability = 95
  Effective max = 95 - 2 = 93
  Current capped at 93 (if it was at 98, it drops to 93)

Takes 10 more damage → current = 83
Repaired by 80% tailor → restored 8, permanent 2 → current = 91
  Effective max = 95 - 4 = 91

Meghan finds a 90% tailor who reviews the ledger:
  Repair 1: was 80% (8/10), now 90% (9/10) → recovers 1
  Repair 2: was 80% (8/10), now 90% (9/10) → recovers 1
  Permanent combat loss: 4 → 2
  Effective max = 95 - 2 = 93
  Current = 91 + 2 = 93

Washed 10 more times → max_durability = 85
  Effective max = 85 - 2 = 83
```

The shirt is aging through both tracks. Eventually it will be replaced.

---

## Open Items

- Cloth type list — full enumeration of material categories and their properties
- Hardness bypass matrix — exact values per damage type × cloth type combination
- Absorption % ranges — what values are reasonable for different garment types (underwear vs. armoured jacket)
- Hardness ranges — what values are reasonable for different cloth types
- Wearability threshold — is ~10% universal, or does it vary by garment?
- Pocket viability threshold ranges — calibration pass per pocket type
- Dressing/undressing time costs — per garment complexity category
- Chemical attack types — enumeration of chemical damage sources and which cloth types they target
- Tailoring skill — full skill article (progression levels, training methods, NPC tailor quality distribution)
- Clothing modifier values — calibration pass for all weapon and attack types
- Clothing shop inventory and pricing — economic balance pass
- NPC tailor locations and quality levels — world design pass
- Dissolve shader — Perlin noise parameters, visual threshold tuning, interaction with definition texture
- Cloth sim freeze for unworn mesh — technical investigation (performance, caching, fallback)
- ~~Whether clothing damage from a hit is localised to a body region or distributed across all worn regions~~ — **confirmed: localised per body zone**
- ~~Whether the open/closed state of a garment affects its absorption % or hardness~~ — **confirmed: opening removes the garment from front hit zones entirely; back zones remain protected**
- ~~Wardrobe Warding interaction~~ — **confirmed: magical reinforcement increases both hardness and absorption % on the enchanted garment**
- ~~Magical girl uniform interaction~~ — **confirmed: civilian clothes enter the pocket dimension on transformation and stop participating. The uniform is treated as a single opaque shield covering the entire body (even visually uncovered areas) with its own durability pool (~1000 vs. ~100 for a leather jacket) and its own absorption model (the 75% threshold system). The two systems are completely separate**
- **Skeleton hit detection verification** — confirm that UE5 skeletal mesh collision returns the nearest bone (or vertex with bone mapping via skin weights). This is the foundation of the bone-to-region lookup
- Body region grouping refinement — the 21-region model may need merging (if some regions are never independently relevant) or splitting (if finer granularity is needed). Playtest-driven
- Whether the clavicle region is independently useful or should be merged with spine_05 or upper_arm
