# Creative Brief — Trap Disarming

---

## Overview

Trap disarming is a **visual puzzle**. The player sees a representation of the trap mechanism and chooses actions to neutralise it — cutting wires, rerouting circuits, releasing tension, removing components. The challenge is reading the mechanism correctly and choosing the right intervention in the right order.

This is driven by the **Engineering skill** (INT/AGI). Engineering determines how much of the mechanism is visible and understood: at low skill, the player sees the trap but doesn't know which wire is safe or which node to disable; at high skill, the optimal disarm path is highlighted and hidden secondary triggers are revealed. The same skill-as-information pattern used by Cooking (recipe clarity), Escape Artist (assessment detail), and Lock Picking (bounce preview).

Trap disarming is distinct from **Trap Avoidance** (AGI/WIS), which is about physically navigating past traps without triggering them. Avoidance is a skill roll; disarming is a puzzle. Perception and Intuition detect the trap; Trap Avoidance bypasses it; Engineering neutralises it. The player chooses which approach to take.

Failure is not mission-ending. A failed disarm triggers the trap's payload — clothing damage, restraint activation, or other consequences. The player is worse off but still in the mission.

**Related documents:**

- [Engineering Skill](../../mgaPriv/mechanics/skills/engineering.md) — INT/AGI, skill progression, Signal Routing minigame
- [Trap Avoidance Skill](../../mgaPriv/mechanics/skills/trap-avoidance.md) — AGI/WIS, physical bypass (not disarming)
- [Trap Disarming Minigame Notes](../../mgaPriv/mechanics/skills/trap-disarming-minigame.md) — detailed mechanics, skill integration, trap component tables
- [Perception Skill](../../mgaPriv/mechanics/skills/perception.md) — passive trap detection
- [Intuition Skill](../../mgaPriv/mechanics/skills/intuition.md) — pattern-based trap sensing
- [Prologue](prologue.md) — Beat 6 (Air Vents) teaches trap disarming as tutorial
- [Clothing Creative Brief](CB-clothing.md) — clothing damage system (primary disarm failure consequence)
- [Restraint Escape Creative Brief](CB-restraint-escape.md) — restraint activation (secondary disarm failure consequence)
- [Repair Creative Brief](CB-repair.md) — Signal Routing minigame (shared Engineering skill, different puzzle)

---

## Use Cases

### UC1 — Disarming a Tripwire Trap

**Actor:** The player
**Goal:** Neutralise a physical tripwire trap by reading its mechanism and choosing the correct disarm actions
**Trigger:** Meghan detects a tripwire trap (via Perception/Intuition) and chooses to disarm rather than avoid

**Step by step:**

1. **Approach** — Meghan moves to the trap. The disarm puzzle opens, showing a visual cross-section of the tripwire mechanism: wires, tension springs, trigger assembly, and payload connection

2. **Assessment** — Engineering skill determines how much of the mechanism the player understands:

   | Skill Range | Information Revealed |
   |------------|---------------------|
   | 1–20 | Basic layout: wire paths visible, but no indication of which are safe to cut or what order matters |
   | 21–50 | Tension indicators: shows which wires are under load; highlights the trigger connection |
   | 51–80 | Full mechanism: secondary triggers revealed; optimal disarm sequence highlighted; tension direction shown |
   | 81–100 | Complete understanding: all components labelled; hidden backups visible; one-step disarm shortcut available for simple traps |

3. **Available actions** — the player selects from context-appropriate actions based on what the mechanism presents:
   - **Cut wire** — sever a specific wire. Correct wire = disables part of the mechanism. Wrong wire = may release tension or trigger a secondary
   - **Release tension** — stabilise a spring or tension element before cutting. Required on some traps to prevent snap-trigger on cut
   - **Remove component** — physically extract a piece (detonator, trigger pin). Requires the mechanism to be safe enough to touch
   - **Bypass** — reroute the trigger path so cutting the main wire doesn't activate the payload. More complex but safer on multi-trigger traps

4. **Sequence matters** — traps with multiple components must be disarmed in a viable order. Cutting the main wire before releasing tension on a spring-loaded trigger fires the trap. Engineering skill reveals the correct order; without it, the player must reason from visual cues

5. **Resolution** — all critical components neutralised = trap disarmed. The mechanism goes inert. The path is permanently safe (for return trips, allies, etc.)

6. **Failure** — wrong action or wrong sequence triggers the trap's payload:
   - Clothing damage (the prologue establishes this as the primary trap consequence)
   - Restraint activation (automated restraint devices — enters the restraint escape flow)
   - CP damage (combat traps in high-security areas)
   - The trap fires but the mission continues. The player deals with the consequence and moves on

7. **Salvage** — a successfully disarmed trap may yield components (wire, springs, sensor parts) that feed into the repair/crafting economy. Engineering skill affects salvage quality

**What the player experiences:** The tripwire puzzle is about reading a physical mechanism. At low Engineering, the player sees wires and springs but doesn't know the safe order — they must reason from visual tension cues and risk a guess. At high Engineering, the puzzle is still present but the information advantage makes it faster and safer. The player who invests in Engineering gets reliable disarms; the player who doesn't can still attempt them but faces real risk. Either way, failure hurts but doesn't end the run.

---

### UC2 — Disarming an Electronic Sensor Trap

**Actor:** The player
**Goal:** Neutralise an electronic sensor trap by reading its circuit layout and choosing the correct disarm actions
**Trigger:** Meghan detects an electronic sensor trap and chooses to disarm rather than avoid

**Step by step:**

1. **Approach** — Meghan moves to the trap's access panel or sensor housing. The disarm puzzle opens, showing a visual circuit layout: power lines, sensor nodes, control module, and payload connection

2. **Assessment** — Engineering skill determines information depth (same tier table as UC1, applied to electronic components):
   - Low skill: power lines visible, but which nodes are active and what disabling them does is unclear
   - Mid skill: power flow direction shown, vulnerable nodes highlighted
   - High skill: cascade effects revealed (disabling node A also kills node C), bypass paths highlighted, redundant circuits identified

3. **Available actions:**
   - **Disable node** — shut down a specific sensor node. Correct node = reduces coverage. Wrong node = may trigger cascade alert
   - **Reroute power** — redirect current away from the trigger circuit. Requires identifying the correct input/output pair
   - **Short circuit** — force a specific circuit to fail. Fast but may cascade unpredictably if the player doesn't understand the circuit topology
   - **Bypass sensor** — create an alternate path that routes around the sensor's detection zone without disabling it. Leaves the trap active but creates a safe corridor

4. **Hacking crossover** — if the sensor has a software component (networked sensor, programmable trigger), the player may choose to hack it instead of physically disarming it. This transitions to the Cylinder hacking minigame (see [CB-hacking](CB-hacking.md)). Engineering handles the hardware; Hacking handles the software. Both are valid approaches

5. **Cascade risk** — electronic traps may have redundant circuits. Disabling one node can shift load to another, changing the puzzle state. Engineering skill reveals cascade connections before the player acts; without it, cascades are surprises

6. **Resolution** — all sensor nodes disabled or bypassed = trap neutralised. Same permanent clearing as UC1

7. **Failure** — same consequence model as UC1: clothing damage, restraint activation, or CP damage depending on the trap's payload. Electronic traps may also trigger facility alerts in networked locations (connecting to [CB-enemy-ai UC11](CB-enemy-ai.md) — alert escalation)

**What the player experiences:** Electronic sensors are a circuit-reading puzzle. The visual language overlaps with Signal Routing (the repair minigame) — the player who has done repair work recognises power flow patterns. The Hacking crossover gives technically-invested characters two approaches to the same problem. At low Engineering, the circuit is opaque and every action carries cascade risk. At high Engineering, the player reads the circuit like a schematic and disarms efficiently. The shared visual language rewards system mastery across Engineering tasks.

---

### UC3 — Choosing to Disarm vs Avoid

**Actor:** The player
**Goal:** Decide whether to disarm a detected trap or bypass it using Trap Avoidance
**Trigger:** Meghan detects a trap and must choose an approach

**Step by step:**

1. **Detection** — Perception (passive) or Intuition (pattern alert) identifies the trap. The player sees the trap highlighted in the environment

2. **Information available** — what the player knows before choosing:
   - Trap type (tripwire, electronic sensor) — always visible once detected
   - Estimated difficulty — shown if Engineering is high enough (skill 21+)
   - Payload type (damage, restraint, alert) — shown if Engineering is high enough (skill 51+)
   - Bypass difficulty — based on Trap Avoidance skill vs trap complexity

3. **Three options:**
   - **Avoid** — use Trap Avoidance (AGI/WIS) to physically bypass. Skill roll. Faster, no tools needed, but the trap remains active (blocks allies, blocks return path, remains a threat). Risk of triggering on a failed roll
   - **Disarm** — use Engineering (INT/AGI) to neutralise via the visual puzzle (UC1/UC2). Takes time, requires proximity, but permanently clears the trap. Risk of triggering on wrong puzzle action
   - **Trigger deliberately** — accept the consequence intentionally. Useful if the trap is minor (clothing damage is acceptable) and the player doesn't want to spend time on either approach. Also useful for clearing traps when no better option exists

4. **Context shapes the choice:**
   - Under time pressure (chase, combat, timed objective): avoidance is faster
   - Clearing a path for allies or return trips: disarming is better
   - Solo infiltration with high AGI: avoidance is natural
   - Technical character with high INT: disarming plays to strength
   - Trap protecting valuable loot or access: disarming is required (can't avoid and still reach what's behind it)

5. **Trap Avoidance does not disarm.** Bypassing a trap leaves it active. An ally following behind, or the player returning through the same area later, still faces the trap. This gives disarming a purpose beyond the immediate moment

**What the player experiences:** The choice is not obvious. Avoidance is faster and simpler but temporary. Disarming takes time and carries its own risk but solves the problem permanently. A high-AGI character leans toward avoidance; a high-INT character leans toward disarming. In practice, the player learns to read the situation: "Am I coming back this way? Are allies following? Is there something behind this trap I need?" The game never forces one approach — both are always available if the player has the skills.

---

### UC4 — Trap Disarm Failure Consequences

**Actor:** The system
**Goal:** Deliver meaningful but non-mission-ending consequences when a disarm attempt fails
**Trigger:** The player selects a wrong action during the disarm puzzle, triggering the trap

**Step by step:**

1. **The trap fires its payload.** What happens depends on the trap type:

   | Payload Type | Consequence | Recovery |
   |-------------|-------------|----------|
   | **Blade / cutting** | Clothing damage to exposed zones; durability reduction follows the clothing cascade (CB-clothing). If clothing is already damaged, may destroy garments | Repair (Tailoring) or replace |
   | **Restraint** | Automated restraint device activates — enters the restraint escape flow (CB-restraint-escape). Player must escape before continuing | Escape Artist skill + restraint escape minigame |
   | **Shock / electrical** | CP damage + brief stagger; clothing damage to contact zone | CP recovery (natural or Meditation) |
   | **Chemical** | DoT to specific cloth types (CB-clothing chemical damage); may degrade clothing over time if not removed | Remove affected garment or wait for dissipation |
   | **Alert** (electronic traps in networked facilities only) | Facility alert level increases (CB-enemy-ai UC11); does NOT directly summon enemies but raises readiness | Proceed with higher alert level; no reset |

2. **No instant mission failure.** The trap consequence is a setback, not a game-over. The player absorbs the damage/restraint/alert and continues. This matches the broader design philosophy — failure creates gameplay (restraint escape, clothing replacement, alert management), not a reload screen

3. **Partial disarm credit** — if the player correctly disabled some components before triggering the trap, the payload may be reduced:
   - A trap with two trigger wires where one was correctly cut fires at reduced effectiveness
   - A sensor array with 3 of 4 nodes disabled triggers a weaker alert
   - Engineering skill determines whether the player can recognise and exploit partial disarm opportunities

4. **Learning from failure** — a triggered trap reveals its full mechanism (the player sees what they got wrong). This information helps with future traps of the same type. No formal "trap knowledge" system — the player learns by observation, same as real puzzle-solving

**What the player experiences:** Failure stings but doesn't end anything. Clothing damage means economic cost. Restraint activation means a detour through the escape flow. Alert escalation means harder stealth going forward. The player learns the mechanism from the failure and does better next time. Partial credit rewards players who got most of the puzzle right — one mistake on a complex trap doesn't deliver full consequences.

---

### UC5 — Learning Trap Patterns

**Actor:** The player
**Goal:** Improve trap disarming proficiency through training and field experience
**Trigger:** Meghan encounters traps during missions or practises in safe environments

**Step by step:**

1. **Prologue introduction** — the Air Vent tutorial (Beat 6, Spicy DLC) teaches trap identification and disarming with badly-maintained duct joints. Clothing damage is the consequence. Milo guides the player through the first disarm. This is the player's first exposure to the visual puzzle

2. **Training environments** — CB-training lists safe disarm practice under Trap Avoidance training. Practice traps have no real payload — failure produces a visual/audio indication but no clothing damage or restraint. The puzzle is identical; only the stakes differ. Engineering XP is gained from practice

3. **Field experience** — real traps in missions provide higher Engineering XP on successful disarm. Failed disarms also provide XP (reduced). The pressure of real consequences accelerates learning

4. **Pattern recognition** — trap mechanisms within a category share visual elements. A player who has disarmed several tripwire traps recognises common tension patterns, spring placements, and trigger assemblies. This is player knowledge, not character knowledge — but Engineering skill ensures the character can act on what the player recognises (wider assessment, more forgiving action windows)

5. **Escalating complexity** — early-game traps are simple (single trigger, one correct action). Later traps add:
   - Secondary triggers (hidden backup that fires if the primary is disarmed incorrectly)
   - Multi-step sequences (must release tension before cutting, must reroute before disabling)
   - Combined types (tripwire connected to electronic sensor — must handle both)
   - Higher-quality components (less obvious visual cues, tighter action windows)

6. **Engineering progression effect on disarming:**

   | Skill Range | Disarm Capability |
   |------------|------------------|
   | 1–20 | Simple traps only; limited information; high failure risk on anything complex |
   | 21–50 | Standard traps manageable; tension indicators and trigger connections visible; moderate failure risk |
   | 51–80 | Complex traps practical; secondary triggers revealed; optimal sequence highlighted; low failure risk |
   | 81–100 | All traps disarmable efficiently; one-step shortcuts on simple traps; hidden components always visible; salvage maximised |

**What the player experiences:** The learning curve mirrors real puzzle-solving. Early traps are simple enough to figure out without much Engineering investment. As traps get more complex, the player either invests in Engineering for more information or accepts higher risk. The prologue tutorial ensures every player understands the system; training environments let cautious players practise safely; field experience rewards the bold. A player who has disarmed dozens of tripwires reads new ones faster — both because their Engineering skill is higher and because they personally recognise the patterns.

---

## Open Items

- Exact visual representation of the disarm puzzle — abstract schematic (clean, readable) or in-world close-up of the mechanism (immersive but potentially cluttered)
- Whether Hacking alone can fully disarm electronic traps, or if it only handles the software layer and Engineering is still needed for the physical circuit
- Whether disarmed traps can be re-armed or repurposed by the player (set traps for enemies)
- Tool requirements — does the player need wire cutters, multitool, or specific items to disarm certain traps? Or is it always bare-hands + Engineering skill?
- Whether NPCs can disarm traps (allies clearing paths, enemies re-arming traps the player disarmed)
- Salvage value table — what components come from disarmed traps and what they're worth in the repair/crafting economy
- Combined trap types (tripwire + electronic) — designed as one complex puzzle or two sequential puzzles?
- Sound design — is disarming silent? Can nearby NPCs hear the player working on a trap? (Stealth interaction)
- Whether trap complexity scales with location type (gang hideout = simple, corporate facility = complex, military = extreme)
- Trap placement by NPCs — can bounty targets or enemies set traps during gameplay, or are traps only pre-placed?
