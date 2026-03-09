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

## Use Cases

### UC1 — On-Foot Movement (Walk, Run, Sprint)

**Actor:** The player
**Goal:** Move through Terridyn at variable speed using analog input, with terrain and encumbrance modifying performance
**Trigger:** Any stick deflection or movement key input

**Step by step:**

1. **Player deflects the stick or presses a movement key.** Stick deflection maps to speed on a continuous curve — there is no walk/run toggle. The player modulates speed by how far they push the stick:

   | Input level | Result |
   |-------------|--------|
   | Slight deflection | Slow walk — quiet, observational pace |
   | Moderate deflection | Normal walk — default exploration speed |
   | Full deflection | Run — fastest non-sprint speed |
   | Full deflection + sprint modifier (held) | Sprint — maximum speed, stamina drain |

2. **Speed transitions are smooth.** Releasing the sprint modifier while at full deflection drops smoothly back to run speed. The Motion Matching system selects and blends between gait animations based on the current speed, producing smooth acceleration and deceleration without hard animation boundaries.

3. **Terrain affects speed.** Paved streets are fastest. Grass and dirt reduce speed slightly. Rubble, mud, and steep gradients reduce it further. The player feels the terrain without needing a HUD indicator — Meghan slows down, her animations change, and the Motion Matching system shifts her gait accordingly.

4. **Stamina cost scales with intensity.** Walking costs negligible stamina. Running costs moderate stamina. Sprinting costs high stamina and cannot be sustained indefinitely — the duration depends on STA and the Running skill. When stamina depletes, sprint drops to run, then run slows. Recovery begins when the player reduces intensity.

5. **Encumbrance matters.** Carry weight (STR-dependent) affects top speed and stamina drain. A Meghan carrying a full courier load moves slower and tires faster than an unencumbered one. The effect is proportional, not binary — a light load barely matters, a heavy load is noticeable, and exceeding carry capacity forces a walk.

**Keyboard and mouse:** Walk speed is mapped to a configurable input method — walk toggle, speed key, or analog keyboard support where available. Sprint is a held key. The goal is equivalent control fidelity to gamepad, not a simplified fallback.

**Dead zones and response curves** are configurable in the controller section of the settings menu. Players with stick drift or accessibility needs can adjust the input curve without losing the analog speed model.

---

### UC2 — Climbing a Building

**Actor:** The player
**Goal:** Scale a vertical or near-vertical surface using Athletics or Parkour, managing stamina and fall risk
**Trigger:** Player approaches a climbable surface and initiates a climb

**Step by step:**

1. **Player identifies the surface type.** The type of surface determines which skill governs the attempt. Terridyn's vertical environment spans multiple height zones — low-rise residential, mid-rise commercial, and high-rise industrial/corporate — each presenting different climbing challenges and surface types.

2. **Athletics climbing (STR/FOR) handles raw strength surfaces.** Sheer walls, ship hulls, smooth vertical faces, cliffs — surfaces with minimal handholds where the climber relies on grip strength and endurance. Stamina drain is high. Fall risk at low skill is significant.

3. **Parkour climbing (AGI/STR) handles technical urban surfaces.** Building ledges, drainage pipes, scaffolding, fire escapes, vaults over obstacles, mantles onto rooftops — surfaces with identifiable hand and foot placement points. The skill is about reading the surface and chaining moves efficiently, not brute strength. Stamina drain is moderate. Higher skill enables faster sequences and longer chains.

4. **Motion Matching provides procedural hand/foot placement.** The player does not manually place hands — they direct Meghan's path and the system handles context-sensitive hand and foot positioning on climbing surfaces. The result should feel responsive and grounded, not floaty.

5. **Stamina drains throughout the climb.** Climbing is expensive. The player can see stamina depleting and must plan routes that include rest points (ledges, flat surfaces, pipe junctions). Running out of stamina while climbing triggers a fall — severity depends on height and what's below.

6. **Low skill increases fall risk.** At low Athletics or Parkour, certain surfaces are simply too difficult. The player can attempt them, but failure probability is high. The system communicates this through Meghan's animations — hesitation, slipping, laboured movement. This is not a hard gate; it is a risk/reward signal.

---

### UC3 — Chaining Parkour Across Rooftops

**Actor:** The player
**Goal:** Cross gaps and obstacles using jumps, wall kicks, rail slides, and swing points in fluid chains
**Trigger:** Player approaches a gap, obstacle, or swing point during traversal

**Step by step:**

1. **Basic jump is universally available.** Covers small gaps, low obstacles, and single-height elevation changes. Stamina cost is low. Motion Matching handles takeoff, airborne, and landing animations based on distance and height.

2. **Parkour chains unlock at higher skill.** At higher Parkour levels, the player can chain jumps with wall kicks, rail slides, and redirects. The system reads the environment and offers chain opportunities when the geometry supports them — the player commits with a directional input and the sequence plays out. Failed chains (mistiming, insufficient skill) break the sequence and Meghan lands where she is.

3. **Swing points extend horizontal reach.** Fixed environmental objects (pipes, bars, cables, clotheslines) support swing traversal. The player engages a swing point with a contextual input while airborne or running. Parkour skill determines swing distance and the ability to chain swing-to-swing.

4. **Stamina budgeting governs chain length.** Each jump, wall kick, and swing costs stamina. A long parkour chain across rooftops costs significant total stamina. The player must gauge whether they can complete a sequence before committing.

**Mixed-use transition zones as parkour sweet spots.** The boundaries between Terridyn's height zones — where low-rise residential meets mid-rise commercial, or mid-rise meets high-rise industrial — create the most varied rooftop geometry. These transition areas offer the richest parkour chains: staggered rooflines, exposed pipes, ventilation infrastructure, and variable gaps that reward creative routing.

---

### UC4 — Crawling and Low-Profile Movement

**Actor:** The player
**Goal:** Move through spaces using a low-profile posture — for stealth, confined navigation, or tactical positioning
**Trigger:** Player enters crawl mode at any time, or encounters a space requiring crawl clearance

**Step by step:**

1. **Player drops to a crawl.** Crawling is a general-purpose movement posture, not just for tight spaces — the player can drop to a crawl anywhere. Two modes are available with different trade-offs.

2. **Hands-and-knees crawl offers speed at moderate profile.** Faster movement, higher profile. The player moves at a reduced but meaningful speed. Quieter than walking but louder than belly crawl. Use cases include open-ground stealth approach, moving through occupied spaces with concealment, and navigating confined spaces.

3. **Belly crawl (soldier crawl) offers minimum profile.** Slower movement, lowest possible profile. The player moves slowly but is extremely hard to detect visually. Near-silent at low speed. Used for stealth approaches across open ground, vent navigation, moving under vehicles, and traversing collapsed structures.

4. **Stealth skill governs noise output across all crawl states.** Crawling speed affects noise output, but the Stealth skill provides noise reduction on top of the base crawl noise. A high-Stealth player moving on hands-and-knees may be quieter than a low-Stealth player on their belly.

5. **Restricted spaces require crawling.** Vents, under-vehicle gaps, collapsed structures, and tight crawlspaces require crawling. These spaces are environmental puzzles — the player sees them, identifies them as traversable, and chooses whether the route is worth the time and exposure.

---

### UC5 — Swimming

**Actor:** The player
**Goal:** Cross or navigate bodies of water, from calm canals to submerged passages
**Trigger:** Player enters water

**Step by step:**

1. **Calm water is accessible at low Athletics.** The player can cross rivers and navigate canals without significant risk. Stamina drain is moderate. Speed is slower than walking on land.

2. **Rough water demands higher Athletics.** Currents, rapids, and turbulent industrial outflows require higher Athletics to maintain control. Low skill risks being swept downstream or pulled under. Stamina drain is high.

3. **Underwater traversal opens at high Athletics.** The player can dive and navigate submerged passages. Breath hold duration scales with FOR. Underwater navigation opens hidden routes and access points not available from the surface.

**Terridyn's waterways.** Terridyn has rivers, underground waterways, and industrial channels — water traversal is not a novelty; it is a meaningful route option.

---

### UC6 — Riding Vehicles

**Actor:** The player
**Goal:** Travel faster across Terridyn using the vehicle progression from bicycle to hoverbike
**Trigger:** Player mounts an available vehicle

**Step by step:**

1. **Bicycle is available early.** Low Driving requirement. Faster than running, no fuel cost, quiet. The courier job's primary tool in the early game. Handles predictably; skill investment improves cornering and speed.

2. **Motorbike becomes available mid-game.** Moderate Driving requirement. Significantly faster than bicycle; fuel cost; louder. Opens longer delivery routes and faster city traversal. Handling at low skill is squirrely — the player feels the difference between Driving 15 and Driving 40.

3. **Hoverbike becomes available late-game.** High Driving requirement. Fastest ground traversal; expensive to fuel and maintain; loud. Represents mastery of the Driving skill tree. Handling is responsive and rewarding at high skill; dangerous and difficult at low skill.

**Driving skill gates handling quality, not access.** Vehicles are traversal tools with their own skill progression, not a mode change. The player can mount a vehicle at any Driving level, but control quality reflects investment.

**Courier job integration.** Vehicle progression aligns with the courier job's demands. Early deliveries are bikeable. Mid-game contracts require motorbike range. Late-game high-value deliveries reward hoverbike speed. The player who has been couriering has the Driving skill to handle whatever vehicle the job demands.

---

### UC7 — Moving Quietly

**Actor:** The player
**Goal:** Traverse the environment with minimal noise, using speed modulation and Stealth skill investment
**Trigger:** Player crouches during movement or reduces speed to avoid detection

**Step by step:**

1. **Player crouches while moving.** Crouched speed is slower than walking; noise output is significantly reduced. Stealth skill governs how much noise reduction crouching provides. At high Stealth, crouched movement is nearly silent.

2. **Player modulates speed to control noise.** Moving slower is always quieter. The player chooses their speed based on how close they are to detection. A slow crouch through a guarded corridor versus a fast crouch past a distracted guard — the player reads the situation and modulates input accordingly.

3. **Advanced stealth movement rewards investment.** At high skill levels, the Stealth skill allows faster crouched movement without proportional noise increase. The player who has invested heavily in Stealth can move at meaningful speed while remaining quiet — a tangible reward for skill investment.

4. **Parkour + Stealth combo unlocks silent traversal.** At high Parkour + high Stealth, advanced traversal modes become available quietly. Silent climbing, quiet vaulting, ceiling traversal through industrial pipe networks. These are late-game capabilities that combine two significant skill investments — they are not available early and they are not available through either skill alone.

**Stealth traversal is not a separate mode.** It is normal traversal performed quietly. The player can crouch, slow down, and choose quieter movement options at any time.

---

### UC8 — Traversal While Exposed (Spicy)

**Actor:** The player
**Goal:** Continue traversal when Meghan's clothing state exceeds her indecency threshold
**Trigger:** Meghan's clothing is destroyed or removed beyond her comfort threshold while inhibition is above zero

**Step by step:**

1. **Locomotion animations change.** Meghan attempts to cover herself while moving — one arm across the chest, hunched posture, shorter strides.

2. **Speed is reduced but no modes are locked.** The effect is cosmetic and speed-modifying, not mode-locking. All traversal modes remain available. Modesty movement reduces speed because the character is dividing her attention between moving and covering. It does not prevent climbing, crawling, or any other mode — it makes them slower and visually different.

3. **Inhibition gates the intensity.** At high inhibition (early game, unexposed Meghan), the modesty animations are pronounced — significant speed reduction, obvious distress. As inhibition decreases through story progression and experience, the modesty animations relax. At low inhibition, Meghan moves normally regardless of clothing state.

**Character growth expressed through traversal.** Early-game Meghan's modesty movement is a visible expression of who she is — someone who has never been in this situation. Late-game Meghan's comfort in the same situation shows how far she has come. The traversal system reflects character development without a single line of dialogue.

---

### UC9 — Traversal While Restrained (Super Spicy)

**Actor:** The player
**Goal:** Navigate with limited traversal options based on active restraints
**Trigger:** Meghan is restrained (hands, feet, or both)

**Step by step:**

1. **Restraints lock traversal modes that require the restrained body part.** This is the governing principle — a restraint removes the traversal modes that require the restrained body part:

   | Restraint | Traversal locked | Traversal available |
   |-----------|-----------------|-------------------|
   | Hands bound | Climbing, swinging, hands-and-knees crawl | Walking, running (reduced balance), belly crawl, swimming (limited) |
   | Feet bound | Running, sprinting, jumping, swimming | Walking (hobbled), hands-and-knees crawl, belly crawl |
   | Hands + feet bound | Most modes | Minimal shuffle, belly crawl (slow) |

2. **Escape Artist interacts with restraints.** A restrained player can attempt to escape using the Escape Artist skill. Until escape succeeds, traversal is limited. The traversal system does not need to know about the escape attempt — it reads the current restraint state and applies the appropriate lockouts.

3. **Specifics deferred to restraint feature brief.** Individual restraint items (rope, cuffs, cable ties, cursed outfit pieces) and their precise traversal effects are defined in the restraint feature brief, not here. The traversal system receives a restraint state and applies the principle above.

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
