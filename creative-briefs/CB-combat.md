# Creative Brief — Combat System

---

## Overview

Combat in MGA is not a mode — it is the same unified action system that governs every interaction in the game. A punch in a bar fight, a bounty takedown, and a magical girl battle all use the same resolution, the same resource pools, and the same action bar. There is no transition into or out of "combat." The world is always running.

This brief covers the player experience of combat: how attacks are initiated, how damage flows from collision to CP, how the player defends, and what data every attack carries. The resolution math (two-phase contest, crits, defensive tiers) is documented in [Combat Mechanics](../../mgaPriv/mechanics/combat.md). The action bar is documented in [Action Bar System](../../mgaPriv/mechanics/skill-bar.md). The clothing damage cascade is documented in [Clothing Creative Brief](CB-clothing.md).

**Related documents:**

- [Combat Mechanics](../../mgaPriv/mechanics/combat.md) — two-phase resolution, crits, DoT timing, defensive tiers
- [Combat Resources](../../mgaPriv/mechanics/combat-resources.md) — CP, SP, MP pools, cap degradation, revival
- [Action Bar System](../../mgaPriv/mechanics/skill-bar.md) — 9–10 slot bar, folders, input scheme
- [Clothing Creative Brief](CB-clothing.md) — clothing damage cascade (UC5, UC5b), body zone segmentation
- [Ground Combat — Weapons](../../mgaPriv/world/ground-combat.md) — weapon technology, personal shields
- [Magical Girl Weapons](../../mgaPriv/mechanics/magical-girl-weapons.md) — soul extension weapons, elemental abilities
- [Capture and Restraint](../../mgaPriv/mechanics/capture-restraint.md) — grapple chain, restraint mechanics
- [Intimate Techniques](../../mgaPriv/mechanics/skills/intimate-techniques.md) — CP drain through intimate acts (super spicy)
- [Mana Drain](../../mgaPriv/mechanics/skills/mana-drain.md) — CP + mana drain through intimate contact (super spicy)
- [Inhibition](../../mgaPriv/mechanics/inhibition.md) — content gating stat, tracks Meghan's openness
- [Skills Overview](../../mgaPriv/mechanics/skills.md) — skill progression, attribute pairing

---

## Attack Data Model

Every attack in the game — melee strike, ranged shot, spell, environmental hazard, trap, AoE blast — carries a standardised set of properties. This is the contract between the combat system and every other system that needs to know what an attack does (clothing cascade, status effects, animation, audio).

| Property | Type | Description |
|----------|------|-------------|
| **Damage amount** | Number | Raw damage before any contest modifiers |
| **Damage type** | Enum | Blunt, slashing, piercing, chemical, fire, ice, lightning, etc. Determines interaction with clothing hardness bypass matrix (see [CB-clothing UC5](CB-clothing.md)) |
| **Clothing modifier** | Float (default 1.0) | Multiplier on damage dealt to clothing durability. Values below 1.0 are clothing-gentle; above 1.0 are clothing-aggressive |
| **Attack attribute** | Enum | Which attribute pair drives Phase 2 resolution: STR (physical), POW (magical), INT (mental), CHA (social) |
| **Primary resource cost** | Pool + amount | Which pool the action draws from and how much (e.g. SP 15, MP 30) |
| **Secondary resource cost** | Pool + amount (optional) | Some actions draw from a second pool (e.g. a mana-infused punch costs SP + MP) |
| **Animation** | Asset reference | Which animation plays on the attacker. Determines the action's time commitment and the collision window |
| **Collision type** | Enum | Melee (attacker's collision mesh contacts target), ranged (projectile spawned), AoE (overlap radius) |
| **Status effects** | List (optional) | Status effects applied on hit, with thresholds scaled by hit quality (see [Combat Mechanics — Status Effects](../../mgaPriv/mechanics/combat.md)) |
| **AoE radius** | Number (optional) | For AoE attacks only — the blast/overlap radius |
| **Directional** | Boolean (optional) | For AoE — whether the blast is omnidirectional or front/back biased |

**Where this data lives:** Each action in the game is a data asset. When an action is placed on the action bar, its full property set is known to the system. Weapons modify the base action properties — the same "slash" action with a knife has different damage, clothing modifier, and animation than with a longsword.

**Weapon contribution:** Weapons add to or modify the base action's properties:

| Weapon property | Effect |
|-----------------|--------|
| **Base damage bonus** | Added to the action's damage amount |
| **Damage type override** | A weapon may force or change the damage type (a mace forces blunt regardless of the action's default) |
| **Clothing modifier bonus** | Added to the action's clothing modifier |
| **Reach** | Determines effective melee range via collision mesh size |
| **Weight** | Affects animation speed — heavier weapons swing slower |
| **Element** | For magical girl weapons — adds elemental damage type |

---

## Use Cases

### UC1 — Initiating a Melee Attack

**Actor:** The player
**Goal:** Strike a target with a melee attack (fist, weapon, improvised object)
**Flow:** Press action bar slot → animation plays → collision mesh contacts target → resolution runs → damage applied

**Step by step:**

1. **Player presses an action bar slot** bound to a melee action (e.g. "Right hook", "Overhead slash", "Shield bash")
2. **Resource cost is deducted** — SP (and optionally MP for magic-infused strikes) is subtracted at the start of the animation. If the player does not have enough resources, the action fails to initiate and brief feedback is given (stagger, failed swing)
3. **Animation plays on Meghan** — the attack animation commits Meghan for its duration. She cannot initiate another action until the animation completes or is cancelled
4. **Collision mesh activates** — the weapon or fist has an attached collision volume. During the animation's active frames (not the windup, not the recovery — only the active strike window), the collision mesh is live and checking for contact
5. **Contact check (Unreal collision):**
   - If the collision mesh contacts the target's skeletal mesh → `FHitResult.BoneName` provides the struck bone
   - If it contacts the target's capsule collider → `FHitResult.ImpactPoint` is used to find the nearest bone via distance loop
   - If no contact occurs → the attack **misses at the physical level**. No resolution runs. The animation completes, the SP is spent, and Meghan is in recovery frames
6. **Bone-to-region lookup** — the struck bone maps to a clothing region via the lookup table (see [CB-clothing — Body Region Segmentation](CB-clothing.md))
7. **Front/back determination** — `dot(hit_direction, target_forward)` determines whether the hit struck front or back
8. **Phase 1 — Targeting Contest** runs: attacker ACC + skill vs defender AGI + defensive skill (if active). Determines miss / glancing / full hit
9. **Phase 2 — Effect Contest** runs: attacker attack_attribute + skill vs defender defense_attribute + blocking. Determines absorbed / partial / full effect
10. **Damage cascade:**
    - Resolved damage enters the clothing cascade for the struck region and side (front/back)
    - Clothing layers absorb per CB-clothing UC5 (absorption, hardness, durability, damage type bypass)
    - Passthrough reaches CP
11. **Damage applied as DoT** — damage resolves over the animation duration, not instantaneously. If the attacker is KO'd during this window, remaining DoT is cancelled
12. **Status effects** applied based on hit quality (full hit > glancing > miss)

**Action cancellation:** The player can cancel a melee attack mid-animation. SP cost is proportional to time already invested (~80% completion = ~80% SP cost). Cancellation enters recovery frames immediately — there is no free exit from a committed action.

**Counterattack window:** If Phase 1 produces a miss with a large defensive margin, the defender gets a guaranteed counterattack window (defensive crit). This is the reward for active defensive skill use.

---

### UC2 — Initiating a Ranged Attack

**Actor:** The player
**Goal:** Strike a distant target with a ranged weapon (firearm, bow, wand, thrown object)
**Flow:** Aim → fire → accuracy determines trajectory → projectile path collision → resolution runs → damage applied

**Step by step:**

1. **Player enters aiming mode** (if applicable) or fires from the hip. Aiming mode provides accuracy bonuses; hip-fire applies accuracy penalties
2. **Player presses the action bar slot** bound to the ranged action
3. **Resource cost is deducted** — SP for physical ranged weapons, MP for magical ranged, SP+MP for hybrid
4. **Fire animation plays** — recoil, muzzle flash, sound. Commits Meghan briefly (shorter commitment than melee — the projectile does the work)
5. **Accuracy calculation** — the projectile's trajectory is determined by:
   - Base accuracy of the weapon
   - Ranged Weaponry skill level
   - Steady Aim skill (reduces penalties from movement, pressure)
   - Aiming mode vs hip-fire
   - Movement state (standing still > walking > running)
   - Posture (crouching improves accuracy)
   - Called Shot (if targeting a specific body zone — accuracy penalty in exchange for zone selection)
6. **Projectile spawned** — travels along the calculated trajectory with spread applied from the accuracy calculation
7. **Path collision** — the projectile checks for collision along its path. First mesh it contacts is the target (or an intervening object — cover, wall, other character)
8. **If projectile hits a character:**
   - Same bone detection as melee: `FHitResult.BoneName` from skeletal mesh, or nearest-bone loop from capsule collider
   - Bone-to-region lookup → front/back determination
   - Phase 1 → Phase 2 → clothing cascade → CP
9. **If projectile hits cover/environment:** No character damage. Projectile behaviour depends on type (bullet embeds, energy bolt dissipates, arrow sticks)
10. **Projectile has its own attack data** — damage type, clothing modifier, and status effects are on the projectile, not the firing action. A fire arrow carries fire damage type regardless of the bow action that launched it

**Called Shot:** The player can deliberately target a specific body zone (see [Called Shot skill](../../mgaPriv/mechanics/skills/called-shot.md)). This overrides the natural bone detection — the accuracy calculation aims for the selected zone, with a penalty proportional to zone precision. A called shot to the head is harder than a called shot to the torso. If the shot lands, it hits the targeted zone; if accuracy degrades the shot, it may hit an adjacent zone or miss entirely.

**Ranged vs melee key difference:** Melee hit detection is physical — the collision mesh must contact the target. Ranged hit detection is trajectory-based — accuracy determines where the projectile goes, then collision determines what it hits. A melee attack can miss by not reaching the target; a ranged attack can miss by going to the wrong place.

---

### UC3 — Receiving Damage

**Actor:** The player (as the target)
**Goal:** Understand the full pipeline from being hit to CP loss

This use case describes what happens to the *defender* — the complete chain from "something contacted Meghan" to "CP changes."

**The full damage pipeline:**

```
1. Collision detected on Meghan's mesh
   → Bone detection (BoneName or nearest-bone loop)
   → Region lookup + front/back

2. Phase 1 — Targeting Contest
   → Attacker ACC + skill vs Meghan's AGI + defensive skill
   → Result: miss (no damage) / glancing (reduced) / full hit

3. Phase 2 — Effect Contest
   → Attacker attribute + skill vs Meghan's defense attribute + blocking
   → Result: absorbed (no damage) / partial / full effect
   → If physical block active: effectiveness % splits damage between
     block item and bleedthrough

4. Resolved damage amount determined
   → Modified by hit quality (glancing = reduced, crit = 2×)

5. Clothing cascade (struck region, front or back)
   → Outermost layer first
   → Per layer: intercept (damage × absorption%) →
     apply clothing modifier → effective hardness
     (hardness × (1 - bypass%)) → durability damage
   → Passthrough to next layer
   → Overflow on destruction carries to next layer

6. Remaining damage → CP
   (Narratively: personal shield nanites absorb the impact,
    draining consciousness/resilience)

7. Status effects applied based on hit quality

8. CP cap degradation check
   → If CP has been below threshold, cap degrades
   → Only sleep fully resets the cap
```

**What the player sees and feels:**

- A hit that clothing fully absorbs: visual clothing damage (dissolve shader progresses), no CP bar movement, brief impact feedback (sound, camera nudge)
- A hit that partially passes through: clothing damage + CP bar drops + stronger impact feedback (screen flash, stagger if applicable)
- A hit to a bare zone: full damage to CP, maximum impact feedback
- A crit: exaggerated feedback — larger screen effect, louder impact, damage number emphasised
- Clothing pocket failure: items visibly drop from the garment mid-fight (staged warning)
- Clothing drops: garment falls off at wearability threshold, items scatter if pockets fail

**Damage type matters to the player:** The same raw damage feels different depending on type. A 100-point blunt hit against a leather jacket barely scratches the clothing (0% hardness bypass) but drains CP. A 100-point slashing hit against the same jacket shreds the clothing (high bypass) but the same CP passes through. The player learns to fear slash/pierce for clothing destruction and blunt for raw CP pressure.

---

### UC4 — Defending

**Actor:** The player
**Goal:** Reduce or avoid incoming damage through active and passive defensive actions

**Defensive options available to the player:**

**Evasion (AGI/WIS) — dodge the attack entirely:**
- Player-triggered dodge input during the incoming attack's active frames
- On success: Phase 1 result shifts toward miss or glancing. A clean dodge avoids all damage
- Large defensive margin → defensive crit → guaranteed counterattack window
- SP cost per dodge. At SP = 0, active dodges are disabled — the character is fully hittable
- Posture affects evasion: standing allows full dodges, crouching allows directional ducks, crawling has minimal evasion options

**Block (FOR/AGI) — absorb with a shield or barrier:**
- Requires a shield item or active magical barrier in the collision path
- **Active block:** Player triggers block input before the hit lands. Higher effectiveness %, costs SP
- **Passive contact block:** Shield happens to be in the collision path without player input. Lower effectiveness %
- Effectiveness % determines the split: that portion hits the shield/barrier HP, the remainder bleeds through to clothing → CP
- Bleedthrough always carries impact effects (knockback, stagger) regardless of block quality
- A shield is an item with its own HP — it can be damaged and destroyed through use

**Parry (timing-dependent):**
- A subset of active block — triggering the block input in a precise window just before impact
- Successful parry: higher effectiveness than standard block + guaranteed counterattack window
- Failed parry timing: degrades to standard active block or misses the window entirely (no block)

**Defensive Stance (FOR/AGI) — sustained defensive posture:**
- Player activates and maintains a guarded stance
- Reduces incoming damage passively while active (damage mitigation multiplier)
- Limits offensive options — cannot initiate most attacks while in defensive stance
- Higher skill levels allow counterattacks from within the stance

**Passive defenses (always active, no player input):**
- Pain Tolerance, Composure, Resistance Training — these contribute partial skill to defensive contests automatically
- The player cannot rely on these alone — passive defense is structurally weaker than active defense (see [Combat Mechanics — Defensive Tiers](../../mgaPriv/mechanics/combat.md))

**Wardrobe Warding (DEF/WIS) — magical clothing reinforcement:**
- Pre-applied enchantment on civilian clothing (see [Wardrobe Warding](../../mgaPriv/mechanics/skills/wardrobe-warding.md))
- Increases hardness and absorption % on enchanted garments
- Not a player action during combat (at low levels) — applied during rest, maintained passively
- At skill level 51+, can be partially refreshed mid-combat at the cost of a brief vulnerability window

**The defensive choice in the moment:**

The player has a fraction of a second to choose:
- **Dodge** — costs SP, avoids entirely if successful, counterattack opportunity on defensive crit
- **Block** — costs SP, reduces damage, shield takes wear, bleedthrough still carries impact
- **Parry** — highest reward (counterattack window), highest risk (failed timing = exposed)
- **Nothing** — raw attribute only, full damage. This is what happens when the player is caught mid-action, out of SP, or simply not paying attention

Offense is structurally favoured. A player who is not actively defending takes significantly more damage than one who is. The system rewards attention and reaction.

---

### UC5 — Area-of-Effect Attacks

**Actor:** The player (as attacker or defender)
**Goal:** Deliver or survive area damage that strikes multiple targets and body zones

**Delivering AoE:**

1. Player activates an AoE action (spell, grenade, explosive device)
2. Resource cost deducted (MP for spells, SP for physical throws, item consumed for consumables)
3. AoE origin point determined:
   - Spells: targeted location or caster-centred
   - Grenades/thrown: landing point determined by Throwing skill (see [CB-inventory UC3](CB-inventory.md) — throw aimed)
   - Environmental: fixed location (exploding barrel, gas pipe, trap)
4. **Overlap detection** — all characters within the AoE radius are hit. Phase 1 is bypassed (being in the radius = being hit)
5. Each character in the radius resolves damage independently

**Receiving AoE — the player's perspective:**

1. Meghan is in the AoE radius — Phase 1 is bypassed
2. **Evasion check** — if the player inputs a dodge during the AoE window, AGI roll reduces the incoming effectiveness before Phase 2. No dodge input = full effectiveness
3. **Phase 2 runs** against the (possibly reduced) damage value
4. **AoE damage distributes across body zones by surface area percentage** (see [CB-clothing UC5b](CB-clothing.md)):
   - Total damage splits to each zone proportional to zone surface %
   - Resolves per garment: collect zone damage into outermost garment → resolve once → redistribute proportionally → next garment collects → repeat
   - Bare zones pass full share straight to CP
5. Total CP loss = sum of all zone passthroughs after clothing resolution
6. Status effects applied (AoE status effects typically apply at full threshold since Phase 1 is bypassed)

**Directional AoE:**
- An explosion from one direction only affects front-facing or back-facing zones based on Meghan's orientation relative to the blast
- An open coat (front zones removed) provides no protection against a frontal blast
- The player can orient Meghan to shield exposed zones with clothed zones — turning to take a blast on the back (where the coat still covers) rather than the front

**AoE vs single-target tactical difference:**
- Single-target attacks hit one zone — clothing on that zone matters, nothing else
- AoE hits every zone — total body coverage matters. Full clothing reduces total CP loss; bare zones are punished
- AoE resolves per garment (one hardness check against the combined collected damage), not per zone (which would over-apply hardness)

**Chemical AoE — DOT fields:**
- Chemical gas or liquid zones apply damage every tick (~0.5s) while the character is in the field
- Each tick distributes and resolves through the same per-garment AoE pipeline
- Chemical damage targets specific cloth types — a cotton dissolvent rapidly degrades cotton garments while barely affecting leather
- Sustained exposure shreds vulnerable clothing layers, progressively exposing bare zones to full damage

---

### UC6 — Grappling and Restraint

**Actor:** The player (as initiator or target)
**Goal:** Physically control an opponent through the grab → grapple → pin → bind chain

**The grapple chain:**

```
Grab → Grappled → Pinned → Binding
```

Each stage escalates control and restricts the target's options. The chain can be broken at any stage if the target escapes.

**Initiating a grab (player as attacker):**

1. Player activates a grab action from the action bar (requires melee range)
2. **Collision check** — Meghan's grab collision must contact the target
3. **Phase 1 — Targeting Contest:** ACC + Martial Arts vs target AGI + Evasion
   - Miss: grab fails, Meghan is briefly in recovery
   - Success: target enters **Grappled** state
4. SP cost is paid. Grappling is physically exhausting — sustained grapples drain SP continuously

**Grappled state:**

- Both characters are locked in close contact. Movement is restricted to struggling
- The grappler can attempt to advance to **Pinned**: STR + Martial Arts vs target STR + Escape Artist
- The grappled character can attempt to escape: STR + Escape Artist vs grappler STR + Martial Arts
- Each contest costs SP for both participants. Grappling is an SP war of attrition
- Other characters can intervene — a third party can attack either participant or attempt to break the grapple

**Pinned state:**

- The target is significantly restricted. Most actions are unavailable
- The pinner can attempt to advance to **Binding**: requires Binding skill + restraint item in hand
- The pinned character can still attempt to escape, but at a penalty compared to the grappled state
- The pinner can also hold the pin without advancing — sustained control at ongoing SP cost

**Binding (restraint application):**

- Requires the Binding skill (AGI/STR) and a restraint item (rope, cuffs, cable ties) in hand
- Application takes real time — the binding animation must complete. The pinned target can still struggle during this window
- Binding contest: AGI + Binding vs target STR + Escape Artist
- On success: the restraint item is applied. The target's action options are severely limited based on what is restrained (hands bound = no hand actions, legs bound = no movement)
- Multiple restraints can be applied sequentially, each further restricting the target

**Being grappled (player as target):**

1. An enemy grab connects — Meghan enters Grappled state
2. The player's options narrow: escape attempts (Escape Artist), call for help (if allies present), or submit
3. **Rending:** While grappling, enemies with the Rending skill can deliberately target clothing for destruction. This is a contested action (Rending vs Wardrobe Warding) that damages specific garments without dealing CP damage
4. **Cursed outfit interaction (Super Spicy):** Cursed items on a magical girl act as restraint anchor points. More curse levels = easier to restrain, harder to escape. See [Capture and Restraint](../../mgaPriv/mechanics/capture-restraint.md)
5. Escape attempts cost SP. If SP reaches 0, active escape attempts are disabled — the character cannot generate enough force to break free

**Bounty hunting application:** The grapple chain is the primary tool for non-lethal target acquisition. A bounty hunter needs to grab, pin, and bind the target to complete the contract. Killing the target may fail the contract. This makes Martial Arts, Binding, and Escape Artist core bounty hunting skills.

**Training:** Grappling skills train through use — both as initiator and as target. Training with Terry (grappling-focused sparring partner) and Celeste (self-defence focus) are documented in [Martial Arts](../../mgaPriv/mechanics/skills/martial-arts.md).

---

### UC7 — Intimate Combat (Super Spicy)

**Actor:** The player (as initiator or target)
**Goal:** Drain an opponent's CP (and optionally MP) through intimate physical acts in a contested, bidirectional exchange
**Content tier:** Super Spicy only — this use case does not exist in base or Spicy variants
**Prerequisite:** Inhibition must be low enough for Super Spicy content to be available (see [Inhibition](../../mgaPriv/mechanics/inhibition.md))

Intimate combat is a **mutual, simultaneous CP drain**. Both participants are attacking and defending at the same time — the exchange is bidirectional by nature. The higher-skilled participant drains CP faster than they lose it; the lower-skilled participant is on a losing clock. Orgasm is the KO — the CP threshold event that ends the contest for the one who reaches it first.

**Initiating intimate combat:**

Intimate combat can begin from several states:

| Entry state | How it starts |
|-------------|---------------|
| **Grapple** | Either participant escalates a grapple into intimate contact. Requires CHA + Intimate Techniques vs target CHA + Composure to initiate. Target can resist the escalation |
| **Restraint** | The initiator begins intimate acts on a restrained target. No initiation contest — the restrained party cannot prevent it from starting, but *can still contest once it begins* |
| **Voluntary** | Both parties consent. No contest to initiate. This is the training and relationship context — sparring with Terry, non-combat intimacy |

**The exchange — step by step:**

1. **Intimate combat begins** — both participants enter the exchange. The world does not pause; other actors can still act, intervene, or attack either participant
2. **Continuous bidirectional CP drain** — each participant's Intimate Techniques skill determines their drain rate against the other:
   - Attacker drain: CHA + Intimate Techniques vs target CHA + Intimate Techniques
   - The skill gap determines the **net flow** — the higher-skilled participant drains faster than they lose
   - Both participants lose CP. The question is who loses it faster
3. **Player input throughout** — the player retains directional control during the exchange. Choices include:
   - **Press** — commit harder; increases both drain inflicted *and* drain received. High reward if winning, accelerates loss if losing
   - **Maintain** — steady state; the skill gap determines the outcome at current rate
   - **Redirect** — attempt to shift the dynamic; costs SP, may open a momentary advantage
   - **Disengage** — attempt to break the exchange; contested by opponent's CHA + skill. Easier when winning, harder when losing. Failed disengagement costs SP and leaves a vulnerability window
4. **Composure as secondary defence** — WIS + Composure provides passive CP loss reduction throughout the exchange. A high-Composure character loses CP more slowly even when outmatched on Intimate Techniques
5. **CP reaches zero → KO** — the participant whose CP hits zero first is out. Narratively this is orgasm overwhelming consciousness. Mechanically identical to any other CP-zero state: unconscious, out of the fight

**Mana Drain — the dual-channel escalation:**

A practitioner with both Intimate Techniques and [Mana Drain](../../mgaPriv/mechanics/skills/mana-drain.md) can apply both simultaneously:

- Intimate Techniques drains CP (CHA-driven)
- Mana Drain drains CP *and* MP (POW-driven, requires maintained physical contact)
- The target must defend against both channels — CHA + Intimate Techniques against the intimate drain, DEF + Mana Stability against the mana drain
- A dual-channel attacker strips CP from two sources and depletes the target's mana pool, removing their ability to cast or maintain magical defences
- Against Tierney or succubus-class opponents: Mana Drain becomes a contested exchange on *both* channels — both parties drain simultaneously on both axes. Skill and attributes on both CHA and POW determine who comes out ahead

**Restraint interaction — the asymmetric exchange:**

A restrained participant is at a severe disadvantage but **is not helpless**:

| Restraint state | Effect on the restrained party |
|-----------------|-------------------------------|
| **Hands bound** | Cannot redirect; press/maintain only. Cannot initiate disengagement |
| **Blindfolded** | Cannot read opponent state; player loses visual feedback cues. Composure defence still functions |
| **Gagged** | Cannot cast (Mana Drain channel unavailable to the gagged party) |
| **Fully bound** | Minimal contest ability; drain rate is heavily one-sided. Composure is the only remaining defence |

The restrained party can still drain CP from the initiator — the exchange remains bidirectional. A low-Composure initiator attacking a highly skilled but restrained opponent may still lose CP faster than expected. Binding amplifies the advantage; it does not eliminate the contest.

**Rending during intimate combat:**

An attacker with the [Rending](../../mgaPriv/mechanics/skills/rending.md) skill can target clothing for destruction during the exchange. This is contested (Rending vs Wardrobe Warding) and damages specific garments without dealing CP damage. Stripping clothing removes armour for future physical attacks and increases psychological vulnerability (potential Composure penalty at GM/system discretion — calibration TBD).

**Cursed outfit interaction:**

Cursed outfit items on a magical girl function as restraint anchor points (see [Capture and Restraint](../../mgaPriv/mechanics/capture-restraint.md)). A heavily cursed magical girl enters intimate combat with existing restraint items already in place, shifting the exchange toward the opponent before it begins. The curse escalation tactic — deepening curse mid-fight to place restraint items, then initiating intimate combat — is the organised-enemy playbook against magical girls.

**What the player sees and feels:**

- CP bars for both participants are visible, draining in real time
- The drain rate visually accelerates or decelerates based on player input choices
- Audio and visual feedback reflect who is winning the exchange — the losing party's feedback intensifies
- A near-threshold warning gives the player a final chance to commit to disengagement or press for the finish
- Third-party intervention (an ally attacking the opponent, an enemy attacking Meghan) interrupts the exchange and returns both participants to standard combat

**Training:**

- Combat application against organic/sentient opponents provides the highest skill progression
- Relationship-based practice (with Terry, with other consenting partners) provides meaningful but slower progression
- Both initiating and being on the receiving end train the skill — failure is not wasted
- Seduction/Charm provides a baseline bonus; high Seduction/Charm makes Intimate Techniques more effective

---

## The Full Damage Pipeline — Summary

For reference, the complete path from action to CP loss:

```
ATTACKER                           DEFENDER
────────                           ────────
1. Action bar input
2. Resource cost deducted
   (SP, MP, or both)
3. Animation plays
4. Collision mesh active
   during strike frames

          ─── collision ───→

                                   5. Bone detection
                                      (BoneName or nearest-bone)
                                   6. Region lookup + front/back

          ─── Phase 1 ───→
          ACC + skill vs           AGI + defensive skill
          Result: miss / glance / full hit

          ─── Phase 2 ───→
          Attack attr + skill vs   Def attr + blocking
          Result: absorbed / partial / full

                                   7. Block split (if blocking)
                                      Effectiveness% → block item HP
                                      Bleedthrough → continues

                                   8. Clothing cascade
                                      (struck region + front/back)
                                      Per layer: absorb → hardness
                                      → durability damage
                                      Passthrough → next layer

                                   9. Remaining → CP
                                      (personal shield = CP narratively)

                                   10. Status effects applied
                                   11. CP cap degradation check
```

For AoE, Phase 1 is bypassed. Evasion provides a mitigation modifier before Phase 2. Damage distributes across body zones and resolves per garment (see [CB-clothing UC5b](CB-clothing.md)).

---

## Open Items

- Status effect catalogue — full list, durations, stacking rules, resolution mechanics
- Exact accuracy formula for ranged attacks — base accuracy, skill modifiers, movement penalties, posture bonuses
- Called Shot accuracy penalties per body zone — how much harder is it to target the head vs the torso?
- Parry timing window — how many frames? Does it vary by weapon type or skill level?
- Block item HP ranges — how durable are shields? How many hits before they break?
- Defensive Stance damage mitigation multiplier — exact values per skill level
- AoE evasion mitigation formula — how much does a successful dodge reduce AoE effectiveness?
- Grapple SP drain rate — how expensive is sustained grappling? Does it vary by relative STR?
- Escape difficulty scaling — how does the escape contest change from Grappled → Pinned → Bound?
- Weapon data table — damage, damage type, clothing modifier, weight, reach for all weapon categories
- Animation frame data — active frames, windup, recovery per action category
- Whether environmental hazards (fire, acid pools, electrical fields) use the same attack data model or a simplified version
- Interaction between magical girl weapon abilities and the attack data model — do the 5 fixed abilities per weapon override or modify the base action properties?
- Whether Meghan can grapple while transformed (magical girl physical stats vs civilian)
- Mana-infused melee attacks — how does the MP cost component interact with the damage type? Does a mana-infused punch become magical damage type or remain physical with added elemental?
- Intimate combat CP drain rate formula — how does the CHA + skill gap translate to drain-per-second?
- Composure CP loss reduction — flat reduction, percentage, or scaling formula?
- Disengagement contest difficulty — how much harder is it to disengage when losing vs winning?
- Rending Composure penalty — does clothing destruction during intimate combat affect the defender's Composure? If so, by how much?
- Mana Drain net-positive threshold — at what skill level does the mana gained exceed the mana spent?
- Dual-channel defence — can a target defend against Intimate Techniques and Mana Drain simultaneously, or must they prioritise one?
- Whether intimate combat can be initiated from standard melee range (without grapple) if both parties are willing or one is caught off-guard
- Visual/audio feedback design for intimate combat — what does the player see and hear during the exchange?
