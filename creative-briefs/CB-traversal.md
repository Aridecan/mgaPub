# Creative Brief — Traversal System

---

## Overview

Traversal is how the player experiences Terridyn as a physical place. Walking through the Neon Strips at night feels different from sprinting across rooftops during a courier delivery, which feels different from crawling through a maintenance vent on a hacking contract. The traversal system exists to make all of those moments feel distinct, responsive, and governed by the same skills the player has been building through daily life.

Movement is analog. The player controls speed continuously through stick deflection — not by toggling between walk and run. Skill investment opens new routes through the environment: a rooftop shortcut that requires Parkour 30, a vertical climb that demands Athletics 40, a vehicle tier that unlocks at Driving 25. The city is always traversable at any skill level; higher skill makes it traversable in more ways.

**Related documents:**

- [Traversal System — GDD Article](../gdd/traversal.md) — high-level overview and technology summary
- [Skills Overview](../../mgaPriv/mechanics/skills.md) — skill progression, attribute pairing
- [Parkour](../../mgaPriv/mechanics/skills/parkour.md) — urban acrobatics, chained movement
- [Running](../../mgaPriv/mechanics/skills/running.md) — speed, endurance, sprint
- [Athletics](../../mgaPriv/mechanics/skills/athletics.md) — strength traversal, swimming, climbing
- [Driving](../../mgaPriv/mechanics/skills/driving.md) — vehicle operation
- [Stealth](../../mgaPriv/mechanics/skills/stealth.md) — noise reduction, silent movement
- [Evasion](../../mgaPriv/mechanics/skills/evasion.md) — reactive movement, dodge
- [Core Loop](../gdd/core-loop.md) — time system, jobs, daily rhythm
- [CB-combat](CB-combat.md) — combat system, action bar, damage pipeline
- [Settings Menu](CB-settings-menu.md) — controller configuration, dead zones

---

## Analog Input Model

Stick deflection maps to speed on a continuous curve:

| Input level | Result |
|-------------|--------|
| Slight deflection | Slow walk — quiet, observational pace |
| Moderate deflection | Normal walk — default exploration speed |
| Full deflection | Run — fastest non-sprint speed |
| Full deflection + sprint modifier (held) | Sprint — maximum speed, stamina drain |

There is no walk/run toggle. The player modulates speed by how far they push the stick. Releasing the sprint modifier while at full deflection drops smoothly back to run speed.

**Keyboard and mouse:** Walk speed is mapped to a configurable input method — walk toggle, speed key, or analog keyboard support where available. Sprint is a held key. The goal is equivalent control fidelity to gamepad, not a simplified fallback.

**Dead zones and response curves** are configurable in the controller section of the settings menu. Players with stick drift or accessibility needs can adjust the input curve without losing the analog speed model.

**Motion Matching integration:** Speed transitions (walk → run → sprint and back) are driven by the analog input value feeding directly into the Motion Matching system. The animation system selects and blends between gait animations based on the current speed, producing smooth acceleration and deceleration without hard animation boundaries.

---

## Base Locomotion — Walk, Run, Sprint

The foundation of all movement. The player is always in one of these states unless actively climbing, swimming, crawling, or in a vehicle.

**Terrain affects speed.** Paved streets are fastest. Grass and dirt reduce speed slightly. Rubble, mud, and steep gradients reduce it further. The player feels the terrain without needing a HUD indicator — Meghan slows down, her animations change, and the Motion Matching system shifts her gait accordingly.

**Stamina cost scales with intensity.** Walking costs negligible stamina. Running costs moderate stamina. Sprinting costs high stamina and cannot be sustained indefinitely — the duration depends on STA and the Running skill. When stamina depletes, sprint drops to run, then run slows. Recovery begins when the player reduces intensity.

**Encumbrance matters.** Carry weight (STR-dependent) affects top speed and stamina drain. A Meghan carrying a full courier load moves slower and tires faster than an unencumbered one. The effect is proportional, not binary — a light load barely matters, a heavy load is noticeable, and exceeding carry capacity forces a walk.

---

## Climbing

Climbing is surface-dependent. The type of surface determines which skill governs the attempt.

**Athletics climbing (STR/FOR):** Raw strength surfaces — sheer walls, ship hulls, smooth vertical faces, cliffs. These surfaces have minimal handholds; the climber relies on grip strength and endurance. Stamina drain is high. Fall risk at low skill is significant.

**Parkour climbing (AGI/STR):** Technical urban surfaces — building ledges, drainage pipes, scaffolding, fire escapes, vaults over obstacles, mantles onto rooftops. These surfaces have identifiable hand and foot placement points. The skill is about reading the surface and chaining moves efficiently, not brute strength. Stamina drain is moderate. Higher skill enables faster sequences and longer chains.

**Procedural hand/foot placement:** Motion Matching provides context-sensitive hand and foot positioning on climbing surfaces. The player does not manually place hands — they direct Meghan's path and the system handles the animation. The result should feel responsive and grounded, not floaty.

**Stamina drain while climbing.** Climbing is expensive. The player can see stamina depleting and must plan routes that include rest points (ledges, flat surfaces, pipe junctions). Running out of stamina while climbing triggers a fall — severity depends on height and what's below.

**Fall risk at low skill.** At low Athletics or Parkour, certain surfaces are simply too difficult. The player can attempt them, but failure probability is high. The system communicates this through Meghan's animations — hesitation, slipping, laboured movement. This is not a hard gate; it is a risk/reward signal.

---

## Jumping and Swinging

Vertical and horizontal gap crossing. Jumping is universally available; chained sequences and swing traversal require Parkour investment.

**Basic jump:** Available to all. Covers small gaps, low obstacles, and single-height elevation changes. Stamina cost is low. Motion Matching handles takeoff, airborne, and landing animations based on distance and height.

**Parkour chains:** At higher Parkour levels, the player can chain jumps with wall kicks, rail slides, and redirects. The system reads the environment and offers chain opportunities when the geometry supports them — the player commits with a directional input and the sequence plays out. Failed chains (mistiming, insufficient skill) break the sequence and Meghan lands where she is.

**Swing points:** Fixed environmental objects (pipes, bars, cables, clotheslines) that support swing traversal. The player engages a swing point with a contextual input while airborne or running. Parkour skill determines swing distance and the ability to chain swing-to-swing.

**Stamina cost per action.** Each jump, wall kick, and swing costs stamina. A long parkour chain across rooftops costs significant total stamina. The player must gauge whether they can complete a sequence before committing.

---

## Crawling

Two distinct crawl modes with different trade-offs.

**Hands-and-knees crawl:** Faster movement, higher profile. The player moves at a reduced but meaningful speed. Useful for navigating low passages, moving through occupied spaces with some concealment, and traversing areas where standing is not possible.

**Belly crawl (soldier crawl):** Slower movement, lowest possible profile. The player moves slowly but is extremely hard to detect visually. Used for stealth approaches, vent navigation, and moving through spaces with very low clearance.

**Stealth integration.** Crawling speed affects noise output. Hands-and-knees is quieter than walking but louder than belly crawl. Belly crawl at low speed is nearly silent. The Stealth skill governs noise reduction across all crawl states — a high-Stealth player moving on hands-and-knees may be quieter than a low-Stealth player on their belly.

**Navigation through restricted spaces.** Vents, under-vehicle gaps, collapsed structures, and tight crawlspaces require crawling. These spaces are environmental puzzles — the player sees them, identifies them as traversable, and chooses whether the route is worth the time and exposure.

---

## Swimming

Athletics-driven water traversal. Terridyn has rivers, underground waterways, and industrial channels.

**Calm water:** Accessible at low Athletics. The player can cross rivers and navigate canals without significant risk. Stamina drain is moderate. Speed is slower than walking on land.

**Rough water:** Currents, rapids, and turbulent industrial outflows. Higher Athletics required to maintain control. Low skill risks being swept downstream or pulled under. Stamina drain is high.

**Underwater traversal:** At high Athletics, the player can dive and navigate submerged passages. Breath hold duration scales with FOR. Underwater navigation opens hidden routes and access points not available from the surface.

---

## Vehicles

Vehicles are traversal tools with their own skill progression, not a mode change.

**Bicycle:** Available early. Low Driving requirement. Faster than running, no fuel cost, quiet. The courier job's primary tool in the early game. Handles predictably; skill investment improves cornering and speed.

**Motorbike:** Mid-game availability. Moderate Driving requirement. Significantly faster than bicycle; fuel cost; louder. Opens longer delivery routes and faster city traversal. Handling at low skill is squirrely — the player feels the difference between Driving 15 and Driving 40.

**Hoverbike:** Late-game availability. High Driving requirement. Fastest ground traversal; expensive to fuel and maintain; loud. Represents mastery of the Driving skill tree. Handling is responsive and rewarding at high skill; dangerous and difficult at low skill.

**Courier job integration.** Vehicle progression aligns with the courier job's demands. Early deliveries are bikeable. Mid-game contracts require motorbike range. Late-game high-value deliveries reward hoverbike speed. The player who has been couriering has the Driving skill to handle whatever vehicle the job demands.

---

## Content Tier: Modesty Movement (Spicy)

When Meghan's inhibition is high and she is in a state of indecency (clothing destroyed or removed beyond her comfort threshold), locomotion animations change. She attempts to cover herself while moving — one arm across the chest, hunched posture, shorter strides.

**The effect is cosmetic and speed-modifying, not mode-locking.** All traversal modes remain available. Modesty movement reduces speed because the character is dividing her attention between moving and covering. It does not prevent climbing, crawling, or any other mode — it makes them slower and visually different.

**Inhibition gates the intensity.** At high inhibition (early game, unexposed Meghan), the modesty animations are pronounced — significant speed reduction, obvious distress. As inhibition decreases through story progression and experience, the modesty animations relax. At low inhibition, Meghan moves normally regardless of clothing state.

**The system communicates character growth.** Early-game Meghan's modesty movement is a visible expression of who she is — someone who has never been in this situation. Late-game Meghan's comfort in the same situation shows how far she has come. The traversal system reflects character development without a single line of dialogue.

---

## Content Tier: Restrained Movement (Super Spicy)

Restraints restrict traversal based on what they bind. This section establishes principles; specific restraint-to-traversal mappings are deferred to the restraint feature brief.

**Governing principle:** A restraint removes the traversal modes that require the restrained body part.

| Restraint | Traversal locked | Traversal available |
|-----------|-----------------|-------------------|
| Hands bound | Climbing, swinging, hands-and-knees crawl | Walking, running (reduced balance), belly crawl, swimming (limited) |
| Feet bound | Running, sprinting, jumping, swimming | Walking (hobbled), hands-and-knees crawl, belly crawl |
| Hands + feet bound | Most modes | Minimal shuffle, belly crawl (slow) |

**Escape Artist and Binding interaction.** A restrained player can attempt to escape using the Escape Artist skill. Until escape succeeds, traversal is limited. The traversal system does not need to know about the escape attempt — it reads the current restraint state and applies the appropriate lockouts.

**Specifics deferred.** Individual restraint items (rope, cuffs, cable ties, cursed outfit pieces) and their precise traversal effects are defined in the restraint feature brief, not here. The traversal system receives a restraint state and applies the principle above.

---

## Stealth Movement

Stealth traversal is not a separate mode — it is normal traversal performed quietly. The player can crouch, slow down, and choose quieter movement options at any time.

**Crouched movement.** The player can crouch while moving. Crouched speed is slower than walking; noise output is significantly reduced. Stealth skill governs how much noise reduction crouching provides. At high Stealth, crouched movement is nearly silent.

**Speed-noise trade-off.** Moving slower is always quieter. The player chooses their speed based on how close they are to detection. A slow crouch through a guarded corridor versus a fast crouch past a distracted guard — the player reads the situation and modulates input accordingly.

**Advanced stealth movement.** At high skill levels, the Stealth skill allows faster crouched movement without proportional noise increase. The player who has invested heavily in Stealth can move at meaningful speed while remaining quiet — a tangible reward for skill investment.

**Parkour integration at high skill.** At high Parkour + high Stealth, advanced traversal modes become available quietly. Silent climbing, quiet vaulting, ceiling traversal through industrial pipe networks. These are late-game capabilities that combine two significant skill investments — they are not available early and they are not available through either skill alone.

---

## Open Items

- Exact stamina costs per traversal action — calibration TBD, depends on STA attribute and skill level scaling
- Fall damage formula — height thresholds, skill mitigation, surface type modifiers
- Vehicle fuel costs and maintenance economics — integration with the economy system
- Swim breath hold duration formula — FOR scaling, skill modifiers
- Motion Matching configuration specifics — dataset size, transition blend parameters
- GMC plugin configuration — which default UE movement behaviors are overridden vs extended
- Environmental hazard interaction during traversal — climbing through fire, swimming through chemical runoff
- Parkour chain system design — how does the player read and commit to chain opportunities?
- Climbing surface tagging system — how are surfaces marked as climbable in the editor?
- Vehicle handling model — arcade vs simulation spectrum, per-vehicle tuning
- Interaction between traversal modes and the CAMERA system — does the camera behavior change during climbing, swimming, or vehicle use?
- Whether encumbrance affects climbing and swimming differently from ground traversal
- Hoverbike terrain interaction — does it hover over water? Over gaps?
