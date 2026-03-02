# Creative Brief — Training System

---

## Overview

Training is how the player deliberately improves Meghan's skills outside of natural gameplay use. Every skill in MGA trains through use — a Parkour skill gained during a courier run counts the same as one gained during a combat chase. But some players want to focus on a specific skill, and some skills benefit from structured instruction before the player encounters them under pressure.

Training areas are small dedicated spaces scattered across Terridyn, each with an instructor NPC who guides the player through exercises. The first visit to a training area is a **lesson** — the instructor explains the skill, demonstrates the basics, and walks the player through a guided exercise. After the lesson, the training area offers **repeatable drills** the player can return to at any time for continued practice.

Training areas serve a dual purpose: they are both a **skill progression mechanic** (Meghan improves) and an **in-game tutorial system** (the player learns how the skill works). A player who visits the firing range before their first bounty hunt understands aiming, called shots, and steady aim before they need them. A player who skips training and jumps straight into the field learns through failure — which is also valid, but more expensive.

**Related documents:**

- [Skills Overview](../../mgaPriv/mechanics/skills.md) — full skill list, attribute pairing, progression model
- [Tutorial / Encyclopedia System](../../mgaPriv/mechanics/tutorial-encyclopedia.md) — how the game teaches systems
- [Core Loop](../gdd/core-loop.md) — time system, daily rhythm
- [CB-combat](CB-combat.md) — combat mechanics, action bar
- [CB-jobs](CB-jobs.md) — job system (jobs are also skill training)
- [CB-studying](CB-studying.md) — academic skill training (Studying, INT/WIS)
- [Difficulty Framework](../gdd/difficulty.md) — difficulty does not affect training; it is combat-only

---

## Training Area Structure

Every training area follows the same pattern:

### First Visit — Lesson

1. **Instructor introduces the skill.** Brief dialogue explaining what the skill does, why it matters, and where it's useful. This is the in-game tutorial for that skill
2. **Instructor demonstrates.** The NPC shows the player what the skill looks like in practice — a punch combo, a climbing technique, a hacking sequence. The player watches
3. **Guided exercise.** The instructor talks the player through performing the skill themselves. Prompts on screen, instructor voice-over, forgiving failure conditions. The player learns the inputs and mechanics in a low-stakes environment
4. **Lesson complete.** The skill is formally introduced. The player has a baseline understanding. Repeatable drills unlock

The lesson is **skippable** per Manifesto Principle 5. A player who already understands the skill (from a previous playthrough, from reading the encyclopedia, or from natural gameplay) can skip the lesson and go straight to drills. The lesson remains available to replay from the training area menu.

### Return Visits — Repeatable Drills

After the lesson, the training area offers drills:

1. **Player selects a drill** from the training area menu. Each area offers 2–4 drills of varying intensity and focus
2. **Drill runs.** The player performs the skill under controlled conditions — targets to hit, obstacles to navigate, puzzles to solve, timers to beat. The instructor provides coaching feedback during the drill ("Good — now try leading the target", "You're gripping too high on the wall")
3. **Skill XP awarded** based on performance. Better execution = more XP. Drills are not as efficient as real-world application (UC6 in CB-studying establishes this pattern) but are reliable and repeatable
4. **Drill complete.** Player can run another drill, switch to a different one, or leave

**Drill difficulty scales with skill level.** A Parkour 10 player gets basic vault drills. A Parkour 50 player gets chained wall-run sequences. The training area grows with the player, remaining useful across the full skill range rather than becoming irrelevant after early levels.

**Time cost.** Drills advance the clock. A quick drill might cost 15–30 minutes of game time. A full training session of multiple drills might cost 1–2 hours. The time cost creates the same trade-off as studying — time spent training is time not spent working or socialising.

---

## Training Hubs

Training areas are grouped into hubs — physical locations in Terridyn that house multiple related training areas.

### Burton's Tavern

Celeste's workplace and a natural social environment. Training here is informal — learning through doing the job and interacting with customers.

| Skill | Instructor | Drill examples |
|-------|-----------|----------------|
| **Cooking** | Celeste | Prep a dish to spec under time pressure; improvise from limited ingredients; handle multiple orders |
| **Perception** | Self-guided (bar environment) | Spot the planted detail in the crowd; identify which customer is about to cause trouble; notice the concealed item |
| **Situational Awareness** | Self-guided (bar environment) | Monitor three tables simultaneously; flag agitation before it escalates; track a suspicious patron across the room |
| **Conflict Resolution** | Celeste / Burton | De-escalate a drunk customer; mediate a dispute between patrons; redirect an aggressive conversation |
| **Small Talk** | Celeste / regulars | Maintain conversation with a difficult customer; navigate an awkward topic; keep a table entertained while waiting |

### MCI Campus

Institutional training in academic, magical, and structured environments.

| Skill | Instructor | Drill examples |
|-------|-----------|----------------|
| **Studying** | Professor NPCs | See [CB-studying](CB-studying.md) — classes, textbooks, study groups |
| **Meditation** | Guided program / instructor | Timed focus exercises; maintain concentration under distraction; extend meditation duration |
| **Counter Spell** | MCI course instructor | Identify incoming spell type; time the counter; handle rapid spell sequences |
| **Elemental Mastery** | Magical training instructor | Controlled elemental output drills; precision targeting; elemental combo practice |
| **Conflict Resolution** | Diplomacy/public speaking club | Formal debate exercises; structured mediation scenarios |

### Gym / Dojo

Physical combat training. The primary venue for melee, grappling, and physical conditioning.

| Skill | Instructor | Drill examples |
|-------|-----------|----------------|
| **Martial Arts** | Gym instructor / Terry / Celeste | Strike combos on training dummies; parry timing drills; grapple-and-escape sequences |
| **Athletics** | Gym instructor / Terry | Climbing wall; endurance circuit; strength exercises under load |
| **Evasion** | Sparring partner NPC | Dodge incoming attacks from a training partner; navigate an obstacle course under fire; directional evasion drills |
| **Resistance Training (Physical)** | Gym instructor | Endure hits from a training partner; progressive impact drills; recovery exercises |
| **Pain Tolerance** | Sparring partner NPC | Sustain activity after taking hits; maintain composure under sustained pressure |
| **Shield Use** | Gym instructor | Block melee strikes; block ranged projectiles; block magical attacks; timed parry drills |
| **Dual Wielding** | Gym instructor | Coordinated strike patterns; alternating offence/defence; weapon-switch drills |

**Terry as training partner:** Terry favours grappling-based training. Sparring sessions with Terry double as relationship-building — playful rewards for good performance, mild teasing for failure. The training dynamic deepens over time, revealing character. See [Martial Arts — Training](../../mgaPriv/mechanics/skills/martial-arts.md).

**Celeste as training partner:** Celeste focuses on self-defence instruction. Meghan teaches Celeste, which reinforces Meghan's own retention (teaching trains the skill). See [Martial Arts — Training](../../mgaPriv/mechanics/skills/martial-arts.md).

### Parkour Gym

A controlled indoor environment for movement skill training without the risks of the urban environment.

| Skill | Instructor | Drill examples |
|-------|-----------|----------------|
| **Parkour** | Gym instructor | Vault sequences; wall-run chains; rooftop-to-rooftop simulations; timed obstacle courses |
| **Running** | Gym instructor / self-guided | Sprint intervals; endurance laps; terrain-change transitions |
| **Stealth** | Gym instructor | Silent movement through a monitored course; noise-awareness drills; crouch-speed training |

### Firing Range

Ranged combat training. Target practice, precision drills, and ranged skill development.

| Skill | Instructor | Drill examples |
|-------|-----------|----------------|
| **Ranged Weaponry** | Range instructor | Stationary targets at increasing distance; moving targets; timed accuracy drills |
| **Called Shot** | Range instructor | Hit marked zones on target dummies; precision under time pressure; moving zone targeting |
| **Steady Aim** | Range instructor | Maintain accuracy while moving; accuracy under simulated stress; breathing and timing exercises |
| **Throwing** | Range instructor | Accuracy throws at targets; arc estimation; moving target throws |

### Hacking Lab

Digital skill training in a simulated environment.

| Skill | Instructor | Drill examples |
|-------|-----------|----------------|
| **Hacking** | Mentor NPC | Progressively difficult terminal puzzles; timed intrusion simulations; detection-avoidance exercises |
| **Research** | Mentor NPC / self-guided | Information retrieval from databases; cross-referencing data sources; efficient search techniques |
| **Problem Solving** | Self-guided (puzzle terminals) | Logic puzzles; pattern recognition; sequence deduction |

### Driving Range

Vehicle handling practice away from traffic and pedestrians.

| Skill | Instructor | Drill examples |
|-------|-----------|----------------|
| **Driving** | Courier mentor NPC | Cornering drills; speed control; obstacle avoidance; vehicle-specific handling (bicycle → motorbike → hoverbike) |

### Shrine Network

Meditation and elemental training at the shrines scattered across Terridyn.

| Skill | Instructor | Drill examples |
|-------|-----------|----------------|
| **Meditation** | Shrine keeper / self-guided | Extended focus sessions (shrine amplification bonus); elemental attunement; CP recovery practice |
| **Elemental Mastery** | Self-guided (elemental hotspots) | Element-specific channeling at shrines matching Meghan's element; environmental manipulation exercises |
| **Mana Stability** | Self-guided | Sustained channeling under environmental mana interference; mana pool management drills |

### Field Training (No Fixed Location)

Some skills train best in the field rather than a dedicated facility. These do not have a fixed training area — the instructor travels with the player or the training is self-directed.

| Skill | Instructor / Method | Drill examples |
|-------|-------------------|----------------|
| **Search** | Bounty hunting mentor / self-guided | Sweep a room for hidden items; investigate a scene; target search after a takedown |
| **Trap Avoidance** | Field instructor | Navigate a trapped environment; identify trigger mechanisms; safe disarm practice |
| **Area Control** | Tactical instructor | Establish defensive positions; manage engagement zones; control chokepoints |
| **Tactical Adaptability** | Sparring scenarios | Adapt mid-fight when conditions change; switch strategy on instructor's command |

---

## Cooperative Training

Some training areas support training with party members or named NPCs. Cooperative drills train both participants and build relationship XP.

| Pairing | Skills trained | Location | Notes |
|---------|--------------|----------|-------|
| **Meghan + Terry** | Martial Arts (grappling), Athletics | Gym / Dojo | Relationship progression; Terry's playful training style |
| **Meghan + Celeste** | Martial Arts (self-defence), Cooking | Gym / Burton's | Meghan teaches Celeste; teaching reinforces Meghan's skill |
| **Meghan + Nagi** | Elemental Mastery, Spell Deflection | MCI / Shrines | Competitive elemental sparring; combo practice |
| **Meghan + Nisa** | Ranged coordination, Area Control | Firing Range / Field | Earth + Ice combo training |
| **Meghan + Arri** | Shield coordination, Counter Spell | MCI / Gym | Lightning + Ice defensive combos |
| **Full team** | Tactical Adaptability, Area Control | Training grounds | Group tactics; formation practice; role-switching |

---

## Development Priority

Every skill has a training path (lesson + drills). Development priority determines which training areas are built first. Higher priority = earlier in development, more polish, more drill variety.

### Priority 1 — Ship First

Skills the player encounters earliest and benefits most from guided instruction.

| Skill | Hub | Rationale |
|-------|-----|-----------|
| **Martial Arts** | Gym / Dojo | First combat skill; core to the game; Terry/Celeste training is story-integrated |
| **Parkour** | Parkour Gym | Used from Ch.1 (courier job, traversal); core mobility |
| **Evasion** | Gym / Dojo | Defensive combat fundamental; players need this before first real fight |
| **Ranged Weaponry** | Firing Range | Ranged combat introduction; bounty hunting depends on this |
| **Hacking** | Hacking Lab | Hacking job requires guided introduction; puzzle mechanics need teaching |
| **Driving** | Driving Range | Courier job vehicle progression; players need to learn handling |
| **Cooking** | Burton's Tavern | Celeste relationship building; waitress job integration |
| **Studying** | MCI | Covered by CB-studying; entrance exam is load-bearing |
| **Meditation** | Shrines / MCI | CP recovery is a core mechanic; needs early introduction |

### Priority 2 — Core Combat and Magic

Skills used heavily in combat and magical girl sequences.

| Skill | Hub | Rationale |
|-------|-----|-----------|
| **Melee Combat** | Gym / Dojo | Weapon-based combat extension of Martial Arts |
| **Shield Use** | Gym / Dojo | Defensive combat; parry/block mechanics |
| **Elemental Mastery** | MCI / Shrines | Magical girl combat; post-Ch.2 wand rebuild |
| **Wand Use** | MCI | Magical girl ranged combat; wand mechanics |
| **Counter Spell** | MCI | Magical defence; structured course already established in lore |
| **Called Shot** | Firing Range | Precision targeting; bounty hunting advanced technique |
| **Stealth** | Parkour Gym | Stealth approach to bounty hunting and infiltration |
| **Athletics** | Gym / Dojo | Climbing, swimming; traversal expansion |

### Priority 3 — Supporting Skills

Skills that enhance gameplay but are not gated by early progression.

| Skill | Hub | Rationale |
|-------|-----|-----------|
| **Binding** | Gym / Dojo | Bounty hunting chain completion |
| **Escape Artist** | Gym / Dojo | Failure pipeline escape; restrained traversal |
| **Throwing** | Firing Range | Grenade/item throwing; ranged utility |
| **Dual Wielding** | Gym / Dojo | Advanced melee option |
| **Steady Aim** | Firing Range | Ranged accuracy under pressure |
| **Running** | Parkour Gym | Sprint endurance; chase sequences |
| **Perception** | Burton's Tavern | Passive skill; training reinforces awareness |
| **Search** | Field | Active investigation; bounty hunting tool |
| **Conflict Resolution** | Burton's / MCI | Social de-escalation |
| **Negotiation** | Field / MCI | Social skill; job and story interactions |
| **Small Talk** | Burton's Tavern | Relationship building tool |
| **Research** | Hacking Lab | Information gathering; complements Studying |
| **Problem Solving** | Hacking Lab | Logic and deduction |
| **Engineering** | Mechanic shop | Mechanic job skill |
| **Repair** | Mechanic shop | Mechanic job skill |
| **Situational Awareness** | Burton's Tavern | Environmental reading |

### Priority 4 — Advanced and Specialised

Skills that matter in later chapters or require specific conditions.

| Skill | Hub | Rationale |
|-------|-----|-----------|
| **Spell Amplification** | MCI / Training grounds | Late magical girl combat |
| **Spell Deflection** | MCI | Magical defence; cooperative training |
| **Channeling** | MCI / Shrines | Sustained magical output |
| **Energy Absorption** | MCI / Shrines | Advanced magical defence |
| **Ward Creation** | MCI | Defensive magic |
| **Barrier Crafting** | MCI | Defensive magic |
| **Mana Stability** | Shrines | Mana management |
| **Mana Drain** | Cooperative training | Super Spicy; requires partner |
| **Wardrobe Warding** | Self-guided | Clothing enchantment |
| **Resistance Training (Physical)** | Gym | Damage resistance |
| **Resistance Training (Elemental)** | MCI / Shrines | Elemental resistance |
| **Pain Tolerance** | Gym | Damage endurance |
| **Composure** | Self-guided / social situations | CP resistance under pressure |
| **Tactical Adaptability** | Training grounds | Mid-fight adaptation |
| **Target Analysis** | Field / Firing Range | Enemy assessment |
| **Planning** | Self-guided | Pre-mission strategy |
| **Area Control** | Training grounds | Zone defence |
| **Leadership** | Field / team missions | Team command |
| **Taunt** | Gym / Field | Aggro management |
| **Social Adaptability** | Field / social situations | Social flexibility |
| **Seduction/Charm** | Relationship contexts | Content-tier dependent |
| **Intimate Techniques** | Relationship contexts | Super Spicy only |
| **Knowledge Recall** | MCI / self-guided | Academic recall under pressure |
| **Intuition** | Self-guided | Passive insight |
| **Trap Avoidance** | Field | Environmental hazard reading |
| **Rending** | Gym / Sparring | Clothing-targeted combat (Spicy) |
| **Breaking/Forcing** | Gym / Field | Obstacle destruction |
| **Physical Recovery** | MCI medical / self-guided | Post-combat recovery speed |

---

## Open Items

- Exact number of drills per training area — 2–4 suggested, but per-hub drill lists need authoring
- Drill XP rates vs natural gameplay XP rates — how much less efficient is training vs real-world use?
- Whether training areas have operating hours (gym closes at night) or are always available
- Instructor NPC roster — which are named characters with dialogue depth vs generic instructors?
- Whether drills have completion rewards beyond XP (e.g. a training milestone unlocks a technique or action)
- Drill scaling formula — how does drill difficulty and XP scale with skill level?
- Whether the player can train with party members at any training area, or only at specific cooperative training locations
- Training area discovery — are all hubs visible from the start, or do some unlock through story progression or exploration?
- Whether training areas exist in the test modes (job test modes, adventure sandbox) for isolated testing
- Voice-over scope for instructors — how much coaching dialogue per drill? Repeating lines vs variety pool?
- Whether modders can add new training areas, drills, and instructors (similar to the studying DLC/mod architecture)
- Mechanic shop as training hub — needs formalisation; Engineering and Repair training implied by the mechanic job but no dedicated training area documented
- Cost of training — do some training areas charge money? (Gym membership, range fees, MCI course fees) Or is training always free once discovered?
