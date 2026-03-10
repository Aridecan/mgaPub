# Creative Brief — Enemy AI

---

## Overview

Enemy NPCs in MGA fight using the same skills, perception, and information systems the player uses. An NPC with Martial Arts 45 fights differently from one with Hacking 60 — not because of scripted behaviour, but because the AI evaluates the same unified action system the player accesses. Actions cost SP, commit to animations, and resolve through the same two-phase contest. There are no secret enemy-only abilities.

The core design principle is **signal parity**: the AI uses only information it could realistically perceive. No god-mode data access. An NPC can only see a target's CP bar if they have the right HUD widget active. An NPC can only track a target through walls if they tagged them in LOS. An NPC's detection radius is based on their Perception skill level. The same rules apply to NPCs and the player.

Every NPC in combat operates in one of three states: **Normal** (ambient behaviour — patrolling, idling), **Engaged** (actively fighting), and **Fleeing** (morale broken — retreating, surrendering, escaping). Stealth is not a separate state; it is a tactic within Engaged.

Architecture decisions — navigation approach, decision system implementation, perception specifics — are pending prototyping. This brief defines *what the player experiences*, not *how the engine implements it*. The [enemy AI research document](../../reference/enemy-ai-research.md) contains the architecture analysis and recommendations; the [enemy AI working notes](../../mgaPriv/mechanics/enemy-ai-notes.md) contain the design rationale.

**Related documents:**

- [Combat Creative Brief](CB-combat.md) — unified action system, two-phase resolution, attack data model, grapple chain
- [Combat Feedback Creative Brief](CB-combat-feedback.md) — hit reactions, stagger, damage display
- [Weather & Environment Creative Brief](CB-weather-environment.md) — weather conditions, perception modifiers
- [HUD Modification Creative Brief](CB-hud-modification.md) — widget point budget, tagging system, tactical overlays
- [Clothing Creative Brief](CB-clothing.md) — clothing damage cascade, body zone segmentation, Rending
- [Knockout & Recovery Creative Brief](CB-knockout-recovery.md) — CP zero, KO state, revival
- [Skills Menu Creative Brief](CB-skills-menu.md) — STALKER profiles, NPC skill data
- [Exploration Creative Brief](CB-exploration.md) — stealth, environmental modifiers
- [Enemy AI Working Notes](../../mgaPriv/mechanics/enemy-ai-notes.md) — three states, signal parity, animation reading, weather AI, street demographics, open questions
- [Enemy AI Research](../../reference/enemy-ai-research.md) — utility AI, GOAP, BT hybrid architecture, archetypes, morale model, content tiers, game references

---

## Use Cases

### UC1 — Fighting an NPC

**Actor:** The player
**Goal:** Engage an NPC in combat and experience an opponent that makes real decisions based on its own skill set and resources
**Trigger:** Combat begins (NPC transitions from Normal to Engaged state)

**Step by step:**

1. The NPC enters the Engaged state — triggered by detecting the player as a threat (perception check), being attacked, or receiving an alert from an ally
2. The NPC evaluates its available actions. The action pool is the NPC's own skill list — the same unified action system the player uses. An NPC with Martial Arts 45 has different available actions than one with Hacking 60
3. Each available action is scored against the current situation: resource cost vs reserves (SP, MP, CP), expected effectiveness (NPC skill vs target's defensive skill), distance to target, animation commitment time, recent success rate, and personality weighting
4. The NPC selects and executes the highest-scoring action. Actions cost SP, commit to animations, and resolve through the same two-phase contest as player actions
5. After the action resolves, the NPC updates its effectiveness history — did the action succeed, fail, get parried, get dodged?
6. The NPC manages its resources throughout the fight. A cost-aware NPC will not burn all its SP on dodges and then be unable to attack. Low SP shifts action preferences toward lower-cost options
7. **Ammo depletion** — an NPC's ranged weapon tracks real ammo, the same as the player's. When the weapon runs dry mid-fight, the NPC makes a utility-scored decision from the same options a player would have:
   - **Reload** — if the NPC has spare ammo and enough distance/time to complete the reload animation before the player closes
   - **Drop and go melee** — drop the empty weapon, switch to fists or a melee weapon if carrying one
   - **Use as a club** — heavy ranged weapons (rifles, shotguns) can be used as blunt melee weapons. Less effective than a real melee weapon, but available immediately
   - **Throw it** — hurl the empty weapon at the player as a last-resort ranged attack. Desperation move — scores high only when other options score low
   - The choice depends on the situation: an NPC with distance and spare magazines reloads; an NPC with the player in their face drops and fights; a desperate NPC with nothing left throws the weapon. The ammo the NPC expends during the fight is genuinely gone — if they fired 15 of 20 rounds, there are 5 left when the fight ends (see UC13)
8. Personality weights differentiate NPC behaviour: an aggressive NPC scores offensive actions higher and overcommits; a cautious NPC shifts to defence and retreat earlier; a technical NPC favours actions that exploit the target's current vulnerability

**What the player experiences:** Fights feel different depending on who the NPC is, not just what archetype they are. The same "gang member" archetype plays differently based on individual skill levels, personality, and resource state. An NPC who overcommits and gets punished feels beatable. An NPC who plays safe and waits for openings feels challenging. Neither should feel like they are cheating. An NPC with a gun is dangerous at range but becomes a different fight when the magazine runs dry — the player can see and hear the reload attempt and rush to punish it, or they can pressure the NPC into burning through ammo faster.

---

### UC2 — The NPC Reads Your Attacks

**Actor:** The system / the player
**Goal:** Create fights where NPCs respond to the player's combat animations, not just run scripts independently
**Trigger:** The player initiates an attack while within an NPC's perception range

**Step by step:**

1. The player starts an attack animation (e.g. overhead slash, snap kick, grab attempt)
2. At 0ms, the NPC sees the animation begin but does not yet know what attack it is
3. After X milliseconds (the attack's **read time**), the NPC identifies the specific attack. Read time varies by attack type:
   - Simple, telegraphed attacks (big wind-up haymaker): short read time — easy to see coming
   - Fast attacks (quick jab, snap kick): longer read time relative to animation duration — harder to identify before it lands
   - Feints: the animation starts as one attack and transitions to another; the NPC's read may be wrong if it identifies too early
4. Once the NPC identifies the incoming attack, it chooses from:
   - **Counter** — initiate a defensive or counter action (block, dodge, parry) if there is time before impact
   - **Continue** — current action is already committed and will resolve first; ride it out
   - **Cancel own attack** — abort current animation to switch to defence; costs SP proportional to time invested (same cancellation rules as the player)
5. The SP cost of cancellation means the NPC cannot cancel-react to everything. An NPC that cancels at 80% animation completion eats ~80% of the action's SP cost for nothing — the same commitment/timing pressure the player faces
6. Read time can be tuned per NPC skill level — a high-skill fighter reads faster, a low-skill fighter reads slower or not at all

**What the player experiences:** Fights feel like the NPC is *watching you fight*, not running its own script independently. A skilled NPC reads your attacks and adapts; a low-skill NPC gets hit by things a better fighter would have seen coming. Feints become a real tactic — start one animation, cancel into another, and bait the NPC's counter.

---

### UC3 — NPC Perception and Detection (Signal Parity)

**Actor:** The system
**Goal:** Ensure the NPC uses only information it could realistically perceive — no cheating
**Trigger:** Ongoing — applies to all NPC decisions at all times

**Step by step:**

1. **Visual damage state** — the NPC reads the same visible cues the player reads:
   - Does the target look damaged? (visible injuries, stagger animations, clothing damage)
   - Is the target currently taking damage? (hit reactions, flinch)
   - What posture is the target in? (standing, crouching, crawling)
   - This is the baseline — available to all NPCs, no special equipment needed

2. **CP/SP information — widget-gated** — an NPC can only see a target's CP or SP bars if they have the appropriate HUD widget active:
   - An NPC with no CP/SP readout widget fights based on visual cues only
   - An NPC with the appropriate widget (e.g. security forces with tactical HUDs, bounty hunters with tracking gear) can see exact pool values and make informed decisions ("target is at 15% CP, press the attack")
   - The widget must be active — same point-budget and hardware constraints as the player's HUD

3. **Tag information — same tag system** — if a target breaks line of sight, the NPC can continue tracking them only if the NPC has an active tag on the target:
   - Tagging requires LOS at time of tagging
   - Tag has a duration timer — expires after a few minutes
   - Retagging in LOS resets the timer
   - Tag provides a world-anchored marker visible through walls until expiry
   - Once LOS is lost AND the tag expires, the NPC must relocate the target from scratch

4. **Detection radius** — based on the NPC's Perception skill level, using the same scaling as the player's Perception

5. **What the AI cannot do:**
   - No seeing through walls without a tag
   - No knowing the player's exact CP/SP without the right widget
   - No "I just know where you are" tracking after LOS breaks
   - No reading the player's skill levels or loadout unless they have STALKER black-market data access (a faction capability, not a default)
   - No reacting to things outside their Perception detection radius

**What the player experiences:** A street thug guesses your condition from how you look. A corporate security team reads your exact CP off their tactical overlay. If you break LOS and wait out the tag timer, the NPC genuinely loses you. Signal parity means the player can reason about what the NPC knows and exploit information asymmetry.

---

### UC4 — Breaking an NPC's Morale

**Actor:** The player
**Goal:** Pressure an NPC until their morale breaks and they retreat, flee, or surrender
**Trigger:** The NPC's morale score drops below their break threshold

**Step by step:**

1. Each NPC has a **morale score** (0–100) determined by personality and training. Different NPC types start at different levels:

   | NPC type | Starting morale | Break threshold | Behaviour at break |
   |----------|----------------|----------------|-------------------|
   | Trained soldier | 75 | 20 | Fighting withdrawal |
   | Gang member | 50 | 25 | Flee |
   | Bounty target | 60 | 15 | Escape attempt |
   | Fanatic | 90 | 5 | Fight to KO |
   | Civilian caught in a fight | 30 | 20 | Surrender immediately |

2. **Morale decreases** from:
   - Taking damage (proportional to damage amount)
   - Ally KO'd (large drop, especially if the leader)
   - Being grappled (ongoing drain while in grapple)
   - Being outnumbered (more opponents than allies)
   - Target demonstrating high skill (the NPC's own "fight" action scores are consistently low — "I can't win this")

3. **Morale increases** from:
   - Landing hits (proportional to damage dealt)
   - Ally reinforcements arriving
   - Target showing weakness (low CP, low SP, staggered)
   - Numbers advantage

4. **Morale state transitions:**
   - Morale > 50%: normal combat — full action pool available
   - Morale 25–50%: cautious — defensive actions score higher, retreat considerations appear
   - Morale < break threshold: **broken** — forced state transition from Engaged to Fleeing

5. **Fleeing behaviour** varies by NPC type:
   - **Fighting withdrawal** (soldiers): continue attacking while moving toward an exit
   - **Full flee** (gang members): sprint for nearest exit, ignore combat
   - **Surrender** (civilians): stop fighting, hands up
   - **Escape attempt** (bounty targets): use skills to evade and escape — see UC5

**What the player experiences:** Pressure works. An NPC who is losing does not fight to the bitter end unless their personality demands it. The player can break a gang by KO'ing the leader (large morale drop to the group), or push a bounty target into a panic escape by pressing hard. Morale gives fights a natural arc — they don't always end in KO.

---

### UC5 — Chasing a Fleeing Bounty Target

**Actor:** The player
**Goal:** Catch a bounty target who has switched from fighting to escaping
**Trigger:** The bounty target's morale breaks (see UC4) and their goal switches from "survive the fight" to "escape the area"

**Step by step:**

1. The bounty target's morale drops below break threshold. A stimulus override fires: the target's active goal switches from combat to escape
2. The target's AI replans — it chains its available skills into an escape route using whatever it has:

   **High-Parkour target:**
   - Breaks free from current engagement (Escape Artist)
   - Sprints toward rooftops (Running)
   - Vaults over railing, climbs fire escape (Parkour)
   - Crosses rooftop gap the player may not be able to follow (Parkour skill check)

   **High-Hacking target:**
   - Sprints toward an interior corridor
   - Hacks electronic door lock behind them (Hacking)
   - Accesses restricted area the pursuer cannot easily enter

   **High-Social target:**
   - Sprints into a crowd
   - Uses Stealth to blend in
   - Calls for help from contacts (Negotiation)
   - If cornered, attempts to bribe or negotiate surrender terms

3. The target uses whatever skills it has — the planner finds the best chain from available capabilities. A target with both Parkour and Hacking can combine them (sprint to building, Parkour up the exterior, Hacking a rooftop access door locked behind them)
4. The player must counter with matching skills: Parkour to follow rooftop routes, Hacking to bypass locked doors, Perception to track a target who blended into a crowd, Search to find a target who went to ground
5. If the target's escape plan is disrupted (player catches up, blocks the route, or disables a skill prerequisite), the target replans with remaining options
6. A chase is a contest of skill lists, not a scripted sequence. The same bounty target plays differently on different terrain

**What the player experiences:** Bounty hunts are not just "fight until KO." A losing target tries to escape, and the escape is shaped by who that target is. Catching a high-Parkour target requires the player to have Parkour; catching a hacker requires Hacking or an alternative route. The player builds their skill set to handle different escape strategies.

---

### UC6 — NPC Stealth Tactics in Combat

**Actor:** The system / the player
**Goal:** Allow NPCs to use stealth as a combat tactic — breaking LOS, hiding, and re-engaging from a new angle
**Trigger:** The NPC's utility scoring selects a stealth action during Engaged state

**Step by step:**

1. An NPC in the Engaged state evaluates stealth as a tactical option. Stealth is not a separate AI state — it is an action choice within Engaged, available when conditions favour it
2. The NPC breaks line of sight — ducks behind cover, moves around a corner, uses environmental concealment
3. The NPC uses Stealth (contested by the player's Perception) to hide. The same environmental modifiers apply:
   - Darkness helps concealment
   - Noise cover (rain, machinery, crowds) helps
   - Limited cover hurts
   - Quiet, well-lit environments hurt
4. While concealed, the NPC repositions — moves to a new angle, flanking position, or ambush point
5. The NPC re-engages from the new position. If the player is not facing the new angle, the NPC may get an opening attack before the player reacts
6. **Tag interaction:** If the player tagged the NPC before they hid, the tag marker tracks the NPC's position through concealment — stealth repositioning is visible through the tag. If the player did not tag them, the player must use Perception to find the NPC again
7. An NPC with high Stealth and the right environment can genuinely disappear mid-fight and ambush the player from a new position

**What the player experiences:** Some NPCs do not stand and trade blows. A high-Stealth opponent ducks behind cover, goes quiet, and reappears somewhere unexpected. The player can counter by tagging the NPC (making repositioning visible), by using Perception to track them, or by controlling sight lines to deny concealment. Stealth mid-fight is a real tactic, not a scripted event.

---

### UC7 — Fighting a Group

**Actor:** The player
**Goal:** Face multiple NPCs who coordinate their tactics based on shared information and complementary roles
**Trigger:** Multiple NPCs in the Engaged state are fighting the player simultaneously

**Step by step:**

1. Each NPC in the group runs its own AI — evaluating its actions independently based on its skill list, personality, and current situation
2. **Squad coordination** emerges from individual decision-making with shared information:
   - One NPC's utility scoring favours suppression (ranged fire to keep the player in cover) while another's favours flanking (moving to an angle)
   - The suppressor's action scores higher when an ally is flanking; the flanker's scores higher when an ally is suppressing. The coordination emerges from individual utility curves reading the same fight state
3. **Information sharing:** NPCs on the same communications network can share tag data. If one NPC tags the player, allies on the network see the tag marker. The scope of information sharing is an open question (see Open Items)
4. **Leader morale effect:** A leader NPC's presence buffs group morale. If the leader is KO'd, all group members take a large morale drop. KO'ing the leader can break an entire group
5. **Role shifting:** As the fight progresses, NPCs shift roles based on what is working. If the flanker takes heavy damage, they may shift to defensive; if the suppressor runs low on SP, they may switch to melee
6. NPCs read the same fight state — if the player focuses fire on one NPC, others may press the advantage or attempt to rescue the targeted ally

**What the player experiences:** Group fights feel coordinated without feeling scripted. NPCs work together because their individual decision-making responds to the same situation. Taking out the leader destabilises the group. Dividing attention between multiple angles is dangerous. The player can disrupt coordination by KO'ing key members, breaking LOS to subgroups, or focusing pressure to trigger morale breaks.

---

### UC8 — Weather Affecting Combat AI

**Actor:** The system
**Goal:** Make weather a real tactical factor — NPCs are affected by the same environmental conditions as the player
**Trigger:** Active weather condition modifies the combat environment

**Step by step:**

1. **Perception modifiers** — weather reduces NPC perception range the same way it reduces the player's:

   | Condition | Perception modifier |
   |-----------|-------------------|
   | Rain | ~75% detection range |
   | Thunderstorm | ~50% detection range; thunder masks combat sounds |
   | Snow | Slight reduction |
   | Fog | ~50% detection range |
   | High wind (3+) | Slight reduction (wind noise masks distant sounds) |

2. **Tactical shifts** — weather feeds into the NPC's action weighting as preference shifts, not hard behaviour switches:
   - **Rain / Thunderstorm:** ranged attacks become less attractive (accuracy penalty applies to NPCs too); melee and ambush tactics score higher; sound-masking makes stealth approaches more viable for NPCs as well as the player
   - **Fog:** NPCs shorten engagement range; may not initiate combat at normal detection distance because they cannot confirm the target; patrol formations may tighten (NPCs staying closer for mutual support)
   - **Snow:** NPCs may avoid open areas where footprints create trackable evidence; conversely, NPCs with Search skill can track the player's footprints
   - **High wind:** NPCs deprioritise thrown weapons and wind-affected ranged attacks

3. **Morale and weather:**
   - Gang NPCs: slightly lower baseline morale in thunderstorms
   - Security patrols: professional — no weather morale modifier
   - Bounty targets: may change hiding patterns based on weather (staying indoors during storms, altering investigation-phase behaviour)

4. **Shelter-seeking (Normal state):** When not engaged in combat, NPCs respond to weather severity:
   - Light rain: most NPCs continue normally; some civilians pop umbrellas or move under awnings
   - Rain / Thunderstorm: civilians seek shelter; non-essential NPCs move indoors; street population thins significantly
   - Heavy snow: similar to rain — civilians reduce outdoor activity
   - Fog: reduced outdoor movement; NPCs stick to familiar routes

**What the player experiences:** Weather is not just visual. A thunderstorm means reduced NPC perception (easier stealth approaches), fewer witnesses on the streets, and enemies that favour melee over ranged. The player can use weather tactically — picking fights during storms for stealth advantage, or avoiding fog which shortens everyone's detection range equally.

---

### UC9 — Content Tier Combat Escalation

**Actor:** The system
**Goal:** Gate combat actions by content tier so the same NPC archetype plays differently at different content settings
**Trigger:** Content tier setting determines which actions are available in the NPC's action pool

**Step by step:**

1. Each NPC has a content-tier flag matching the player's selected tier. The tier acts as a **filter on the action pool** — actions outside the permitted tier are excluded before any scoring occurs

2. **Available actions by tier:**

   | Tier | Actions available to the AI |
   |------|---------------------------|
   | **Base** | Standard melee/ranged combat, grapple chain (grab → pin → bind), flee, surrender, use environment, Negotiation |
   | **Spicy** | All Base + Rending (clothing destruction during grapple), clothing-targeted attacks, exploitation of clothing damage for tactical advantage |
   | **Super Spicy** | All Spicy + Intimate Techniques (CP drain through intimate acts), Mana Drain (dual-channel CP+MP drain), curse escalation tactics (placing cursed items as restraint anchors), intimate combat initiation from grapple/restraint |

3. The content tier does not change the AI architecture — same utility curves, same personality weights, same morale model. Only the available action pool changes

4. **Escalation patterns by tier:**

   **Base:**
   - Fight → Grapple → Pin → Bind → Deliver (bounty)
   - Fight → Morale break → Flee / Surrender

   **Spicy:**
   - Fight → Grapple → Rend clothing (strip defences) → Continue fight with advantage
   - Fight → Grapple → Pin → Bind → Deliver

   **Super Spicy:**
   - Fight → Grapple → Rend → Strip defences → Initiate intimate combat → CP drain to KO
   - Curse escalation: place cursed items → use as restraint anchors → intimate combat at severe advantage

5. The same NPC archetype produces different combat behaviour at different tiers. A gang enforcer in Base plays as a straightforward brawler. The same enforcer in Super Spicy has additional tactical options — Rending to strip defences, intimate combat as a finishing move

**What the player experiences:** The content tier changes what enemies can *do*, not how smart they are. A Base-tier fight is a clean combat encounter. A Spicy-tier fight includes clothing-targeted attacks and the threat of being stripped of armour mid-combat. A Super Spicy fight adds intimate combat as a win condition for enemies. The AI treats these as tactical options, not scripted sequences — the NPC uses them when the utility scoring says they are the best available action.

---

### UC10 — Being Chased by an NPC

**Actor:** The player
**Goal:** Flee from a pursuing NPC who follows through complex terrain by replaying the player's own traversal actions
**Trigger:** The player breaks from combat or flees, and an NPC pursues

**Step by step:**

1. The player disengages from combat and runs. The NPC enters pursuit — its goal switches to "catch the fleeing target"
2. The system maintains a **rolling buffer of the last 5–10 seconds** of the player's traversal actions. Each entry records a resolved traversal action at a world position — "wall-run on this surface," "vaulted this object," "jumped this gap" — not raw input
3. As the chasing NPC reaches the vicinity of each recorded action point, it **attempts the same traversal action** against its own skill level:
   - **Success:** the NPC follows the player's exact route and stays on their tail
   - **Failure:** the NPC's skill check fails — it cannot replicate the move
4. **On failure, the NPC does not stop.** It immediately falls back to conventional pathfinding (NavMesh + skill-gated NavLinks) to find an alternative route it *can* take:
   - Circle the building and find a door
   - Take the stairs instead of the wall-run
   - Go around the gap instead of jumping it
   - Find a different route to re-intercept the player
   - This is what a real person would do — if they can't follow directly, they take a route they know and try to pick up the chase
5. **Route comparison (Hard difficulty):** When the player transitions between zones or areas, a Hard-difficulty NPC can calculate the estimated time for the conventional route (stairs, doors, ground-level path) and compare it against attempting the player's recorded traversal. If the conventional route is faster or comparable — because the NPC's skill makes the parkour route slow or risky — the NPC takes the conventional route instead. This means a Hard NPC does not blindly follow; it evaluates whether following is actually the fastest way to catch up, and takes a shortcut through known paths when that is smarter. On Easy/Normal, the NPC either follows the replay or falls back on failure — it does not proactively compare routes
6. If the player gets far enough ahead that the buffer has expired (>5–10 seconds of lead), the NPC is fully on its own pathfinding. It tries to predict where the player is heading and intercept rather than follow — which can produce a more interesting chase than blind trailing
7. **Pursuit Tenacity** (from the [difficulty framework](../../mgaPub/gdd/difficulty.md)) controls how long the NPC keeps chasing before giving up entirely. On Easy/Short, the NPC gives up quickly. On Hard/Relentless, it keeps trying, picks better intercept routes, and may call for backup

**Difficulty interaction:**

| Difficulty | Effect on chase |
|-----------|----------------|
| **Easy** | NPC fails skill checks more often, fallback routing is less aggressive, gives up sooner. No route comparison |
| **Normal** | NPC succeeds on traversal within its skill range, reasonable fallback routing, standard pursuit duration. No route comparison |
| **Hard** | High-skill NPCs nail the replay, pick smarter intercept routes on fallback, don't give up easily, may coordinate cutoffs with allies. **Route comparison active** — NPC evaluates whether following the player's path or taking a conventional route is faster, and picks the better option |

**What the player experiences:** Running away is not automatic escape. An NPC that sees you wall-run across a gap will try to follow — and if it has the Parkour skill, it will. If it can't, it doesn't stand there staring; it goes around. The player can exploit the system by choosing traversal routes that exceed the NPC's skills, creating distance while the NPC takes the long way around. A high-skill pursuer on Hard is genuinely dangerous to run from — they follow your route, and when they can't, they find a smarter one.

**Relationship to UC5:** UC5 describes the player chasing a *fleeing NPC* (bounty target escapes). UC10 describes an *NPC chasing the player*. The replay buffer only applies to UC10 — when the NPC is following the player. Fleeing NPCs (UC5) use their own pathfinding and skill-gated routes to escape; they are not replaying anything.

---

### UC11 — Alert Escalation (Normal → Engaged)

**Actor:** The system / the player
**Goal:** NPCs transition from unaware to combat through an escalation sequence determined by who they are and where they are
**Trigger:** An NPC's Perception detects something anomalous — a sound, a visual cue, a missing ally, an open door that should be closed

**Step by step:**

1. **Unaware** — the NPC is in its Normal state. Patrolling, idling, going about its routine. Perception is passive — detection radius based on skill level, modified by weather (UC8) and environment (lighting, noise)

2. **Suspicious** — the NPC detects something that doesn't fit: a sound outside the expected pattern, movement at the edge of perception range, a door ajar, a missing patrol partner. The NPC does not yet know there is a threat — only that something is off
   - Visual indicator to the player: the NPC's behaviour changes subtly (stops, turns toward the stimulus, pauses mid-action)
   - The player can observe this and react — freeze, back off, find better cover

3. **Alert response** — what the NPC does next depends on **who they are and where they are:**

   **Solo, informal (gang lookout, street thug):**
   - Goes to investigate personally. Moves toward the stimulus location
   - Does not call it in — there is no one to call, or no protocol to follow
   - If they find nothing after searching, returns to routine (with heightened awareness for a period)
   - If they find the player → transitions directly to Engaged

   **Professional, networked (corporate security, facility guard):**
   - **Calls it in first** before moving. Reports the anomaly to their network (radio, comms)
   - This raises the **facility alert level** — other guards in the network become Alert even though they didn't personally detect anything
   - Patrols may tighten, additional guards may move toward the reported area, access points may lock down
   - *Then* the reporting guard investigates, potentially with backup en route
   - If they find the player → Engaged, and the facility is already on alert (faster reinforcement, coordinated response)

   **Paired / squad (police patrol, security team):**
   - Alert spreads to the immediate group — all members shift to Alert simultaneously
   - Group may split: one investigates while others cover exits or hold position
   - Group coordination difficulty parameter affects how effectively they split and cover

   **Civilian:**
   - Does not investigate. May freeze, back away, or flee the area
   - May call authorities (law enforcement response — if implemented)
   - Removes themselves from the situation rather than escalating toward it

4. **Investigating** — the alerted NPC (or group) moves toward the stimulus. During investigation:
   - Perception is heightened (actively searching, not passive)
   - The NPC checks the stimulus location, looks around corners, checks hiding spots
   - Search skill (if the NPC has it) allows more thorough investigation
   - The player's Stealth is contested by the NPC's active Perception — harder to stay hidden than against a passive patrol

5. **Resolution — three outcomes:**
   - **Find threat → Engaged.** The NPC confirms the player's presence and transitions to combat. For networked NPCs, this confirmation is broadcast — the facility knows the threat is real and where it is
   - **Find evidence but no threat.** KO'd ally, broken lock, missing item. The NPC does not return to Unaware — stays on heightened alert. Networked NPCs report findings, potentially escalating the facility further
   - **Find nothing.** After a search period, the NPC returns toward its routine. Alertness decays over time but does not instantly reset — the NPC is more attentive for a while. A second anomaly during this decay window escalates faster

6. **Facility alert levels** (for networked locations — corporate buildings, secure areas, guarded compounds):

   | Level | Trigger | Effect |
   |-------|---------|--------|
   | **Green** (routine) | Default | Normal patrols, standard access |
   | **Yellow** (anomaly reported) | Guard calls in a suspicious detection | Patrols tighten, guards more attentive, response teams on standby |
   | **Orange** (threat likely) | Evidence found (KO'd guard, breach detected) | Additional guards deployed, access points restricted, active sweeps begin |
   | **Red** (confirmed threat) | Player spotted and confirmed | Full lockdown, all available security responds, exits sealed |

   Alert level decays over time if no further incidents occur, but slowly — a facility that went to Orange does not drop back to Green in five minutes

**What the player experiences:** Stealth has real stakes. A solo gang lookout who hears you is a simple problem — deal with them before they find you. A corporate security guard who hears you is a different problem — they call it in *before* investigating, and now the whole building knows something is wrong even if you take them out quietly. The player learns to prioritise networked NPCs: silencing the guard before the radio call matters more than silencing them before the punch. Different infiltration targets require different approaches based on who is guarding them and how they are organised.

---

### UC12 — NPC Investigation Behaviour

**Actor:** The system / the player
**Goal:** NPCs actively search an area when alerted, with investigation quality driven by their Search skill and available tools
**Trigger:** An NPC enters the Investigating phase from UC11 (alert escalation), or discovers evidence (KO'd ally, broken lock, missing item, blood trail)

**Step by step:**

1. The NPC moves to the stimulus location — the point where the anomaly was detected. During movement, Perception is active (heightened), not passive

2. **Search quality is driven by the NPC's Search skill.** The same skill that governs the player's active investigation governs the NPC's:
   - **Low Search (street thug, gang lookout):** checks the obvious — glances around corners, looks behind the nearest large objects, checks the immediate area. Misses non-obvious hiding spots. Gives up quickly
   - **Mid Search (security guard, police patrol):** more systematic. Checks doors, looks under and behind furniture, opens closets, examines the wider area around the stimulus point. Spends more time before giving up
   - **High Search (bounty hunter, specialist investigator):** thorough. Follows evidence trails (footprints in snow, blood, scuff marks), checks camera feeds if available, examines the environment for signs of passage (disturbed objects, recently closed doors). Does not give up easily

3. **Lighting management** — if the investigation is at night or in a dark area, the NPC takes steps to improve visibility before or during the search:
   - Turns on overhead lights, room lights, facility lighting — whatever is available in the environment
   - Pulls out a flashlight if they carry one (most professional security; some gang members)
   - Opens blinds/shutters to let in ambient light
   - The NPC is actively working to remove darkness as an advantage for whoever they're looking for
   - **Player counterplay:** the player can stay outside the newly lit area, sabotage lighting in advance (cut power, remove bulbs, hack lighting systems), or use the moment the NPC is reaching for a light switch as a window to move

4. **Evidence trail following** — NPCs with sufficient Search skill can follow physical evidence:
   - Footprints in snow or mud (bidirectional — see UC8, weather)
   - Blood trails from a wounded target
   - Disturbed objects (knocked items, moved furniture, doors left ajar that should be closed)
   - KO'd allies — the NPC examines the body, determines direction of attack, and searches in that direction
   - The quality of trail-following depends on Search skill. A low-Search NPC walks right past footprints; a high-Search NPC tracks them

5. **Camera and tech integration** — NPCs with access to security systems can extend their investigation beyond personal perception:
   - Check security camera feeds for recent footage of the area
   - Access door logs to see which doors were opened recently
   - These are Hacking/Engineering-gated actions — a guard with no tech skill can only search physically; a guard with terminal access can pull up camera footage
   - **Player counterplay:** hacking cameras offline or looping feeds before infiltration denies this avenue

6. **Search duration and give-up** — how long the NPC investigates before standing down depends on:
   - **NPC type:** gang lookouts give up in under a minute; corporate security searches for several minutes; bounty hunters searching for a known target may not give up at all during a session
   - **Evidence found:** finding hard evidence (KO'd ally, clear signs of intrusion) extends the search and may escalate the facility alert level (UC11). Finding nothing starts the give-up timer
   - **Facility alert level:** at higher alert levels, search duration increases and NPCs are less willing to stand down. A Red-alert facility stays in active sweep mode

7. **Resolution:**
   - **Player found → Engaged.** NPC confirms the player visually and transitions to combat. For networked NPCs, location is broadcast immediately
   - **Evidence found, no player:** NPC reports findings. Facility alert escalates. NPC either continues searching in a wider area or holds position and waits for reinforcements. Does not return to Unaware — stays on heightened alert
   - **Nothing found:** NPC returns toward routine after the search duration expires. Alertness decays slowly (UC11) — a second anomaly during decay re-triggers investigation faster and with higher urgency

**What the player experiences:** Investigation is not a timer to wait out. A high-Search NPC actively looks for you — checks hiding spots, follows your trail, turns on lights to deny you darkness. The player needs to think about what evidence they leave behind (footprints, KO'd guards, opened doors) because all of it feeds the investigation. Different enemies investigate differently: a gang lookout you can hide from by ducking behind a dumpster; a corporate security specialist with camera access and high Search skill requires you to have prepared the environment in advance. Stealth is not just "don't be seen" — it's "don't leave traces."

---

### UC13 — Looting a KO'd NPC

**Actor:** The player
**Goal:** Take items from a defeated NPC, where the only lootable items are what the NPC was actually carrying and using
**Trigger:** The player interacts with a KO'd NPC's body

**Step by step:**

1. An NPC is KO'd (CP reaches zero). The NPC's body becomes a **world container** — the same container system used for backpacks, chests, and any other interactable storage (see [CB-inventory UC4](CB-inventory.md))

2. **No loot tables. No random drops.** The NPC's inventory is real. What they were carrying, wearing, and using during the fight is exactly what exists on the body. Nothing is generated at KO time. The NPC's inventory was determined when they spawned and modified by what happened during the encounter

3. **Items reflect the fight that just happened:**
   - A ranged weapon has only the ammo remaining after the fight. If the NPC fired 15 of 20 rounds, there are 5 left in the magazine
   - Spare magazines the NPC didn't use are still full
   - Clothing has whatever durability damage it sustained during combat (from the player's attacks, environmental damage, or Rending)
   - Consumables the NPC used during the fight (healing items, grenades) are gone
   - A weapon the NPC threw at the player (UC1, step 7) is wherever it landed, not on the body

4. **The body container shows the NPC's full carried inventory:**
   - Weapons (in hand or holstered)
   - Ammunition (loaded and spare)
   - Clothing (worn, in current durability state)
   - Items in pockets (same pocket system as the player's clothing — see [CB-clothing](CB-clothing.md))
   - Any other carried objects (phone, tools, restraints, keys, ID cards)

5. **Looting uses the standard inventory interaction** — the unified inventory screen shows the NPC's containers alongside the player's. The player drags items between containers, same as any other inventory interaction. Looting requires a free hand (same rules as CB-inventory UC4)

6. **Looting takes real time.** The player is interacting with a world container in the live world — no pause, no safe menu. Other NPCs can interrupt, combat can resume, and time spent looting is time not spent doing something else. Thorough looting of a fully equipped NPC takes longer than grabbing one item and running

7. **What the NPC was wearing is lootable but in its current state.** A kevlar vest that absorbed 30 hits during the fight is a damaged kevlar vest. The player gets exactly what survived the combat, not a fresh version. This connects to the repair system — a looted damaged jacket can be repaired by a tailor, but it's not free gear in pristine condition

**What the player experiences:** Loot is real. Before a fight, the player can observe what an NPC is carrying — that rifle, that jacket, those magazines — and decide whether the engagement is worth it for what they'd get. During the fight, every round the NPC fires is a round the player won't find. After the fight, the body has exactly what's left. There are no magical drops, no "kill rat, find sword" moments. A street thug has a knife and a cheap phone. A corporate security operator has body armour, a sidearm, spare mags, a comms unit, and an ID card that might open doors elsewhere. The loot tells you who the NPC was.

---

### UC14 — Post-Combat Behaviour

**Actor:** The system / the player
**Goal:** NPCs handle the aftermath of combat realistically — securing threats, aiding allies, recovering, and returning to routine
**Trigger:** Combat ends — either the player is KO'd/fled, or all hostile NPCs are KO'd/fled

**A. When the NPC wins (player KO'd):**

1. **Secure the player first.** The winning NPC's immediate priority is making sure the KO'd player cannot resume fighting when they wake up:
   - Restrain Meghan using the grapple chain — bind hands, bind legs if restraints are available
   - Search her — remove weapons, tools, anything that could be used to escape or attack
   - Depth of search depends on the faction and the NPC's assessment of the threat:

   | Faction | Search depth | Disposition |
   |---------|-------------|-------------|
   | **Gang members** | Thorough — strip weapons, check pockets, may strip clothing if valuable or to prevent concealed items | Check STALKER report on Meghan's skills. If she's valuable (good combat skills, useful abilities), route her to the black market pipeline for sale. If not worth the trouble, take what's valuable and leave her restrained |
   | **Corporate security** | Professional — confiscate all equipment, catalogue items, standard restraint protocol | Detain for interrogation or hand over to authorities. Corporate protocol — documented, procedural |
   | **Police / law enforcement** | Standard arrest procedure — weapons removed, restraints applied, rights equivalent | Arrest and process. Equipment held as evidence |
   | **Bounty hunters** | Thorough — they know the job. Strip weapons, check for concealed items, apply professional restraints | Deliver to their client. Meghan is the bounty — she gets transported |
   | **Civilians** | Minimal — they won the fight but don't know what to do with an unconscious person | Call authorities, back away, or flee the scene |

   - Full faction capture protocols are an open item for a future feature brief — the table above is starting direction, not final design

2. **Check on allies.** After securing the player, the NPC checks on KO'd teammates:
   - Move to each KO'd ally, assess their state
   - Attempt to revive them (first aid if they have the skill, or basic "shake them awake" after enough time has passed)
   - Revived allies are groggy — reduced effectiveness for a period (same post-KO state as the player, see [CB-knockout-recovery](CB-knockout-recovery.md))

3. **Report the incident.** Networked NPCs (corporate security, police) report the outcome:
   - Threat neutralised, prisoner secured, casualties reported
   - Facility alert level may begin decaying (UC11) now that the threat is contained
   - Non-networked NPCs (gang members) may report to their boss when they next see them, or not at all

4. **The failure pipeline takes over.** Once the NPC's post-combat behaviour completes, the player's experience transitions to the [failure pipeline](CB-failure-pipeline.md) — transport to holding, equipment confiscation, and the escape gameplay loop. The AI brief covers what the NPC *does*; the failure pipeline covers what the player *experiences*.

**B. When the player wins (NPCs KO'd):**

1. **KO'd NPCs are unconscious.** Their bodies are world containers available for looting (UC13). They remain KO'd for a period based on how much negative CP they reached (same mechanics as player KO — see [CB-knockout-recovery](CB-knockout-recovery.md))

2. **The player can secure KO'd NPCs.** If the KO'd NPC is a bounty target or someone Meghan wants to capture:
   - Use the grapple chain to bind them while KO'd (Binding skill + restraint item — no contest while unconscious)
   - When they wake up, they are already restrained — their available actions are limited by what is bound (hands, legs, both)
   - Lead the bound NPC to a bounty drop-off point for delivery
   - A bound NPC may attempt to escape during transport (Escape Artist vs Binding — ongoing contested checks). The player needs to maintain control

3. **KO'd NPCs eventually recover.** If the player leaves without securing or looting:
   - The NPC regains consciousness after a period
   - They wake up groggy — reduced combat effectiveness, disoriented
   - They have whatever inventory the player left them. If looted of weapons, they're unarmed. If stripped of clothing, they're in whatever remains
   - They assess their situation: are allies nearby? Is the threat gone? Is their equipment missing?

4. **Post-recovery NPC behaviour depends on faction and situation:**
   - **Gang member:** regroups with allies if possible, reports what happened to leadership, may seek revenge or avoid the area. A gang member who was stripped and humiliated may become a recurring hostile
   - **Corporate security:** reports the breach, triggers facility alert escalation, replacement guards are dispatched. The facility learns from the incursion
   - **Police:** calls for backup, files a report, the player may gain a wanted status or investigation flag
   - **Solo NPC (street thug, random encounter):** retreats to safety, avoids the area for a while. May or may not remember the player's face for future encounters

5. **NPC memory of the encounter:**
   - The NPC remembers being in a fight and being KO'd
   - If the NPC saw the player before being KO'd, they can recognise the player in future encounters
   - If the player was stealthy (KO'd the NPC from behind without being seen), the NPC knows they were attacked but not by whom
   - For named NPCs and bounty targets, encounter memory may persist across sessions (Nemesis-style — see Open Items)

**What the player experiences:** Combat has consequences that persist after the fight. If the player wins, they need to decide: loot and leave (the NPC wakes up and reports what happened), secure the target for bounty delivery (ongoing contest during transport), or leave quickly before reinforcements arrive. If the player loses, the NPC doesn't just stand over the body — they restrain, search, and process Meghan according to their faction's protocols. A gang that checks STALKER and finds Meghan has valuable skills sells her to the pipeline. Corporate security detains and interrogates. The post-combat behaviour makes factions feel distinct and feeds directly into the failure pipeline's playable capture content.

---

### UC15 — NPC Environmental Interaction in Combat

**Actor:** The system / the player
**Goal:** NPCs use the same interactive environment the player can — cover, throwable objects, hazards, doors, elevation — as tactical options during combat
**Trigger:** The NPC's utility scoring evaluates environmental actions alongside standard combat actions

**Step by step:**

1. **The environment is part of the NPC's action pool.** Interactive objects within the NPC's awareness are evaluated as potential actions the same way melee strikes and dodges are. The NPC does not need special scripting to use the environment — if an action is available and scores well, it gets used

2. **Cover** — NPCs use cover the same way the player does:
   - Move behind solid objects to break line of sight and block ranged attacks
   - Crouch behind low cover, lean out to fire, duck back
   - Evaluate cover quality: full cover (solid wall) vs partial cover (table, vehicle) vs destructible cover (furniture, thin walls)
   - **Destroy the player's cover:** if the NPC has the means (heavy weapon, explosives, sufficient melee damage), they can destroy cover the player is hiding behind rather than trying to flank around it

3. **Throwing environmental objects** — NPCs can pick up and throw objects in the environment:
   - Chairs, bottles, tools, debris — anything with a hand cost the NPC can manage (same hand cost system as the player — see [CB-inventory UC1](CB-inventory.md))
   - Thrown objects use the same throwing mechanics as the player (Throwing skill, accuracy calculation)
   - Low-skill NPCs throw inaccurately; high-skill NPCs land thrown objects reliably
   - A thrown chair is not a lethal weapon, but it staggers, disrupts, and buys time — it is a tactical action, not a damage source

4. **Hazard exploitation** — NPCs can use environmental hazards against the player:
   - Push or grapple the player toward a hazard (fire, electrical panel, chemical spill, height drop)
   - Trigger environmental hazards deliberately (shoot a fire extinguisher, break a gas line, activate electrical equipment)
   - Avoid hazards themselves — the NPC reads the same environmental danger the player does and routes around it
   - **Signal parity applies:** the NPC must perceive the hazard to use it. An NPC who has not seen the gas leak cannot deliberately ignite it

5. **Doors and chokepoints** — NPCs use doors tactically:
   - Close and lock doors to block pursuit (same as Hacking targets escaping in UC5)
   - Funnel the player through chokepoints where the NPC has the advantage
   - Open doors to create new routes or flank
   - Barricade doors with heavy objects if time permits (furniture, debris)

6. **Elevation** — NPCs seek advantageous elevation when their skills allow:
   - A ranged NPC moves to high ground for better firing angles and cover
   - A melee NPC may drop down onto the player from above for an ambush
   - Elevation changes use the same traversal system — Parkour-gated routes are only available to NPCs with sufficient skill

7. **Environmental action scoring:** Environmental actions compete with standard combat actions in the utility scorer. A table flip to create cover scores high when the NPC is under ranged fire and near a table; it scores low when the NPC is winning a melee fight. The NPC does not use the environment for spectacle — it uses it when the situation makes it the best option

**What the player experiences:** The environment is not just scenery or player-only tools. An NPC under fire ducks behind a counter. An NPC losing a melee fight grabs a chair and throws it to create space. A Technical archetype shoots out the lights before engaging. The player's cover gets destroyed by a heavy hitter who decided breaking the table was smarter than going around it. Environmental interaction makes fights feel like they happen *in* a space, not just *near* geometry.

---

### UC16 — Reinforcements and Escalation

**Actor:** The system / the player
**Goal:** NPCs can call for help during combat, bringing reinforcements that change the tactical situation
**Trigger:** An NPC decides to call for reinforcements based on combat pressure, faction protocol, or facility systems

**Step by step:**

1. **Calling for help is an action.** It costs time (the NPC must perform the call animation) and may cost resources. While calling, the NPC is committed to the action and vulnerable — the same animation commitment rules as any other action. The player can interrupt the call by attacking during it

2. **Call methods depend on who the NPC is:**

   | Method | Who uses it | Range | Player counterplay |
   |--------|------------|-------|-------------------|
   | **Shout** | Anyone | Nearby — only NPCs within earshot respond | Close distance fast to KO before the shout; fight in noisy environments (rain, machinery) where shouts don't carry as far |
   | **Radio / comms** | Professional security, police, organised gangs | Facility-wide or faction-wide — all networked NPCs receive | Jam comms, hack the radio, destroy the comms device, KO the NPC before the transmission completes |
   | **Alarm trigger** | Security guards near alarm panels | Facility-wide — automated response | Hack or disable alarm systems before infiltration; destroy the panel |
   | **Phone call** | Anyone with a phone | Unlimited range — but takes longer (dial, wait for answer) | Grapple to prevent the call; take their phone during the fight; signal jamming in the area |

3. **What triggers a reinforcement call:**
   - **Outnumbered** — the NPC is alone or losing numbers advantage
   - **Morale pressure** — morale dropping toward cautious range (25–50%); calling for help is a defensive utility action that scores higher as morale drops
   - **Leader order** — a Leader archetype NPC may order a subordinate to call for backup while the leader continues fighting
   - **Facility protocol** — networked locations may have automatic escalation (UC11 — alert level reaching Orange or Red triggers reinforcement dispatch without any individual NPC needing to call)
   - **Player identified as high threat** — NPC reads the player's combat performance (signal parity — based on what they can observe) and decides they need help

4. **Reinforcement arrival:**
   - Reinforcements do not teleport. They travel from wherever they are to the combat location using normal pathfinding
   - **Arrival time** depends on distance, route complexity, and the reinforcements' movement capabilities
   - The player can hear or see reinforcements approaching — footsteps, sirens, radio chatter confirming "en route"
   - This gives the player a window: finish the fight before reinforcements arrive, disengage and flee, or prepare for the larger engagement

5. **Reinforcement quality depends on the faction and location:**
   - **Gang territory:** nearby gang members respond. Quality varies — could be more grunts, could include a leader or specialist who happens to be close
   - **Corporate facility:** trained security response team dispatched. Professional, equipped, coordinated. Arrival time depends on facility size and team positioning
   - **Police:** patrol units respond. Response time varies by district — some areas have heavy police presence, others are effectively unpatrolled
   - **Bounty hunter team:** the target's allies or hired protection. May be pre-positioned if the target expected trouble

6. **Escalation cycle:** Reinforcements can themselves call for further reinforcements if the fight continues to go badly. This creates an escalation cycle — the longer a fight drags on in a populated or networked area, the worse it gets for the player. This incentivises:
   - Fast, decisive engagements
   - Stealth approaches that avoid open combat
   - Fighting in isolated areas where no reinforcements are available
   - Disrupting communications before engaging

7. **Difficulty interaction:**
   - **Group Coordination** parameter affects how quickly and effectively reinforcements integrate with the existing fight (share tags, coordinate tactics)
   - **Pursuit Tenacity** affects whether reinforcements who arrive after the player flees will join the pursuit
   - On Easy / Low coordination: reinforcements arrive but fight independently, poorly integrated
   - On Hard / High coordination: reinforcements arrive with shared tag data, immediately coordinate with existing combatants, may cut off escape routes

**What the player experiences:** Fights in populated areas have a clock. The longer the fight takes, the more likely reinforcements show up. The player hears the radio call and knows backup is coming — that creates urgency. A smart player finishes fast, disrupts communications before engaging, or picks fights in isolated locations. A corporate facility that goes to Red alert sends a response team — the player can hear them coming and must decide: hold position, flee, or set up an ambush for the arriving team. Reinforcements are not a surprise spawn — they are real NPCs travelling from real locations, and the player can intercept, avoid, or prepare for them.

---

## Reference Sections

### Enemy Archetypes

Each archetype is defined by personality weights on utility curves, not by separate code. The same engine drives all of them.

| Archetype | Role | MGA equivalent |
|-----------|------|---------------|
| **Grunt** | Straightforward melee, easy to engage | Low-skill gang member, street thug |
| **Squad** | Mid-to-long range, coordinated groups | Security teams, police squads |
| **Leader** | High survivability, buffs allies | Gang bosses, corporate security chiefs |
| **Tank** | Slow, durable, high damage | Heavyweight fighters, armoured opponents |
| **Swarm** | Fast, weak, high numbers | Petty criminals, panicked mobs |
| **Sniper** | Slow, vulnerable, dangerous at range | Corporate assassins, rooftop shooters |
| **Technical** | Uses Hacking/Engineering to control environment | Hacker opponents, trap-setters |
| **Social** | Uses Negotiation/Small Talk to call for help, bluff, stall | Smooth talkers, con artists |
| **Grappler** | Prioritises grab → pin → bind chain | Bounty hunters, law enforcement |
| **Intimate** (Super Spicy) | Uses intimate combat as primary CP drain | Succubus-class opponents, trained agents |

### Pattern Adaptation

The AI tracks a rolling window of action effectiveness history — the last N attempts per action type:

- Each action records: attempted, succeeded, failed (parried, dodged, blocked, missed)
- The failure rate becomes a consideration in the utility score: `pattern_score = 1.0 - (recent_failure_rate × adaptation_weight)`
- High failure rate penalises the action's score; the NPC naturally shifts toward alternatives
- The rolling window means old failures decay — if the player changes their defensive approach, the NPC may try previously-failing actions again
- No explicit "don't do this" rules — the math handles adaptation

**Cross-encounter memory** (Nemesis-style) is considered for bounty targets and named NPCs: enemies remember what happened in previous encounters and develop persistent adaptations (e.g. an enemy who was grappled develops counter-grapple behaviour; an enemy beaten by ice magic develops fire resistance tactics). This is an enhancement beyond per-encounter tracking — see Open Items.

### Street Demographics

Temperature and season shift the outdoor NPC racial balance on Terridyn's streets. This is a spawning weight modifier, not a hard rule — all types are present in all seasons; the balance shifts, it does not flip.

| Type | Build | Comfortable range |
|------|-------|-------------------|
| **Normal human** | Standard | ~5–30°C |
| **ROH** (Reduced-stature Offworld Human) | Smaller, lighter | ~0–20°C |
| **HW** (Heavyweight) | Larger, heavier | ~5–35°C |

| Season | Temperature range | Street demographic shift |
|--------|------------------|--------------------------|
| **Summer** (hot, long days) | 20–35°C+ | ROH thins outdoors — prefer air-conditioned interiors, underground, or evening activity. HW comfortable and visibly present |
| **Winter** (cold, short days) | -5–10°C | HW more visible outdoors — handle the cold better and work outside. ROH comfortable in cold and present normally |
| **Spring / Autumn** (mild) | 5–25°C | Most balanced — all three types comfortable outdoors. Closest to census demographic ratio |

**Gameplay impact:** Summer bounty hunts in outdoor areas are more likely to encounter HW NPCs. Winter nights may see more ROH activity. Gang territorial presence shifts — a gang with significant HW membership has stronger street presence in summer. The player notices the pattern over the in-game year: Terridyn's streets feel different in January than in July.

### Decision Architecture (Design Intent — Pending Prototyping)

The current architectural leaning is a three-layer hybrid. This is design intent, not confirmed implementation — it requires prototyping and complexity evaluation.

**Layer 1 — Utility AI Core** (tick-by-tick action selection): The NPC scores all available actions every decision tick against current game state — resource costs, expected effectiveness, distance, risk, recent effectiveness, personality weights, morale modifier, content tier filter. The highest-scoring action executes. Randomisation within a score threshold prevents robotic predictability.

**Layer 2 — GOAP Planner** (multi-step sequences): For goals requiring chained actions — escape plans, coordinated tactics, investigation sequences, resource acquisition. The planner fires when the current goal changes or the current plan is invalidated.

**Layer 3 — Stimulus Overrides** (event-driven forced responses): Immediate, non-negotiable responses — morale break, AoE incoming, ally KO'd, being grappled, environmental hazard. These fire unconditionally and can switch the active goal (triggering a GOAP replan) or temporarily override the utility scorer.

**Integration flow (design intent):**

```
Every decision tick:
  1. Check stimulus queue → if override active, execute it
  2. Check current GOAP plan validity → if invalid, replan
  3. If GOAP plan has next action, evaluate against utility alternatives
     → if plan action scores reasonably, execute (plan coherence)
     → if non-plan action scores much higher, interrupt plan
  4. If no active plan, run pure utility scoring on all available actions
  5. Execute selected action
  6. Update effectiveness history
  7. Update morale
```

**Navigation (design intent):** UE5 NavMesh + NavLink system — NavMesh provides walkable surfaces (auto-generated), NavLinks provide conditional connections (manually placed). Skill-gated vertical links (e.g. "requires Parkour 30 to wall-run across this gap") are the MGA-specific extension. This needs prototyping to confirm.

### Integration Points

Systems the AI hooks into:

- **Combat** — unified action system, two-phase resolution, SP/CP/MP pools, action cancellation, DoT timing, crit system
- **Perception** — passive detection, detection radius, concealment levels; AI Perception works identically to the player's
- **Stealth** — opposed Stealth vs Perception checks, environmental modifiers, CHA misdirection
- **STALKER** — NPC skill profiles drive the AI's available action pool; the AI reads the same profile the player can view
- **Combat posture** — standing/crouching/crawling affects detection profile and available actions
- **HUD / Tagging** — widget-gated information access and the tag tracking system
- **Morale** — morale score driving Engaged → Fleeing state transitions
- **Content tiers** — Base/Spicy/Super Spicy gating which actions the AI can evaluate
- **Clothing damage** — visual damage state the AI reads; clothing-targeted attacks in Spicy+ tiers
- **Weather / Environment** — perception modifiers, tactical adaptations, morale effects, shelter-seeking, demographic shifts
- **Traversal** — chase replay buffer records player traversal actions; NPC attempts replay against own skill; fallback to conventional pathfinding on failure

---

## Open Items

### From enemy-ai-notes.md — design questions

- **Animation read time formula:** flat ms value per animation, or derived from the NPC's skill level?
- **Feint interaction:** can the player deliberately start one animation and cancel into another to bait the AI's counter?
- **Group information sharing:** if one NPC tags the player, do allies on the same comms network see it?
- **NPC self-assessment:** does the AI read its own STALKER profile for self-assessment, or "know" its own capabilities implicitly?
- **NPC drones:** does an NPC's CAMERA drone extend their perception radius? (Probable: yes, same as the player's CAMERA)
- **Environmental destruction:** if the player creates a new path (blows a wall, breaks a floor), how quickly does the navigation update?

### Architecture decisions — blocked on prototyping

- **Navigation:** NavMesh + NavLink with skill-gated vertical links (current leaning) — needs prototyping to confirm viability and authoring cost
- **Chase replay buffer:** exact buffer duration (5–10 second range), skill check mechanics for replayed traversal, fallback pathfinding quality by difficulty tier
- **Decision system:** Utility AI + GOAP + BT stimulus hybrid (current leaning) — needs implementation complexity evaluation
- **Perception implementation:** visual cone + audio radius specifics, partial detection (shimmer equivalent for AI), STALKER integration

### From enemy-ai-research.md — design decisions

- **Pattern adaptation scope:** per-encounter only, or cross-encounter Nemesis-style memory for named NPCs/bounty targets?
- **Morale thresholds:** exact values per NPC type are TBD — table in UC4 is starting point, needs tuning
- **Squad coordination detail:** how does the squad manager assign goals? Emergent from individual GOAP plans or explicit assignment?
- **Making enemies feel smart without feeling unfair:** read times, reaction windows, and personality weights need tuning to avoid input-reading feel. An NPC that is too perfect at reading and countering feels like BS, not skill
