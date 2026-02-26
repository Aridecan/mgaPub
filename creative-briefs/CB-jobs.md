# Creative Brief — Job System

---

## Overview

The five jobs — Waitress, Courier, Mechanic, Hacker, Bounty Hunter — are the economic engine of Chapters 1 and 2. Each job is a distinct minigame with its own skill training, reputation track, and client ecosystem. Together they provide the income that covers Meghan's ongoing expenses (rent, food, tuition, clothing) while creating the skill foundation she carries into every other context in the game.

All five jobs use the same unified action system. The skills trained during a waitress shift — Parkour, Small Talk, Conflict Resolution, Situational Awareness — are the same skills used to navigate a bounty mission or negotiate a hacking fee. There is no job-specific progression silo. One character, one skill system, applied across all contexts.

Jobs unlock in a staggered sequence. The player does not face all five options simultaneously. Early jobs (Waitress, Courier) are low-risk, skill-building introductions to the city. Later jobs (Mechanic, Hacker, Bounty Hunter) carry higher risk, higher reward, and faction reputation consequences that persist through the game.

This brief covers the player experience of each job: what the player sees, what they do moment-to-moment, and what choices they face. The economic framework, time system, and reputation rules are documented in [Core Loop](../gdd/core-loop.md). The combat mechanics referenced in bounty hunting are documented in [CB-combat](CB-combat.md). The grapple chain used for target acquisition is [CB-combat UC6](CB-combat.md). The clothing system interacting with all physical jobs is [CB-clothing](CB-clothing.md). The inventory system governing carried items is [CB-inventory](CB-inventory.md).

**Related documents:**

- [Core Loop](../gdd/core-loop.md) — economy, time system, 24-hour clock, job reputation, failure pipeline, school calendar
- [CB-combat](CB-combat.md) — combat resolution, grapple chain (UC6), attack data model
- [CB-clothing](CB-clothing.md) — clothing damage, degradation, repair economics
- [CB-inventory](CB-inventory.md) — hand cost, containers, carry weight
- [CB-hud-modification](CB-hud-modification.md) — widget point budget, phone widgets (delivery app, GPS)
- [CB-camera](CB-camera.md) — CAMERA drone, third-person view during jobs
- [Systems Overview — Jobs](../gdd/systems-overview.md#jobs) — index entry

---

## Use Cases

### UC1 — Working a Shift at Burton's Bar (Waitress)

**Actor:** The player
**Goal:** Complete a waitress shift at Burton's Bar, earning money through wages and tips
**Schedule:** Mon–Fri 5–9pm, Friday extended to 11pm, Saturday 10am–2am, closed Sunday
**Available from:** Chapter 1 (story event — Celeste introduces Meghan to Burton)

The only job with fixed, time-gated availability. Burton's Bar is the lowest-variance income source — reliable but limited by operating hours. This is Meghan's first job and the player's introduction to the job system.

**A shift, step by step:**

1. **Arrive at Burton's Bar** during operating hours. Meghan changes into work attire (apron, comfortable shoes — provided by Burton)
2. **Celeste assigns a section** — a set of tables that are Meghan's responsibility for the shift. Section size scales with reputation (more tables = more income potential, but higher difficulty)
3. **Customers arrive and seat themselves.** The player watches the floor — customers appear at tables in Meghan's section over the course of the shift
4. **Approach a table and take orders** — a dialogue interaction. Customers describe what they want in natural speech. The player must remember the details — there is no automatic order list. Order complexity increases with bar reputation (early: "two beers"; later: specific drinks, modifications, dietary requirements)
5. **Navigate the crowded floor** to the bar or kitchen. Meghan carries a tray (hand cost applies — one hand occupied). The floor is physically modelled: other patrons, staff, chairs, and moving obstacles. Parkour skill governs crowd navigation — higher skill means smoother movement, fewer near-collisions, faster delivery
6. **Place the order** at the bar or kitchen window. Wait time varies by order complexity
7. **Collect and deliver** the completed order. Match the right items to the right table — the system does not auto-sort. Delivering the wrong order to a table is a mistake with consequences (customer dissatisfaction, wasted time, potential re-make)
8. **Handle disruptions throughout the shift:**
   - **Drunk or aggressive patrons** — Conflict Resolution skill. A successful de-escalation resolves the situation without violence. Failure may escalate to a physical confrontation (bouncer intervention or Meghan handling it herself, which trains Melee Combat but risks damage)
   - **Wrong orders** — player error; requires re-delivery, time cost, customer patience impact
   - **Spills and collisions** — AGI check when navigating with a full tray through a moving crowd. Failure = dropped items, wasted time, possible clothing damage (spilled drinks)
   - **Customer complaints** — Small Talk and Conflict Resolution to salvage the interaction
9. **Shift ends.** Payment is calculated based on:
   - Base wage (fixed per shift)
   - Tips (performance-based — order accuracy, delivery speed, customer satisfaction, disruption handling)
   - Penalties for mistakes (wrong orders, spills, unresolved complaints)

**Resource drain:** SP is the primary cost. A full Saturday shift (10am–2am) is a marathon. CP degrades from sustained social interaction and stress. A low-CP Meghan makes more mistakes — wrong tables, forgotten details, slower conflict resolution.

**What trains:**

| Skill | How it trains |
|-------|---------------|
| Small Talk | Customer interaction, order-taking, complaint handling |
| Conflict Resolution | Drunk patron de-escalation, complaint recovery |
| Situational Awareness | Tracking multiple tables, noticing new customers, reading the floor |
| Parkour | Crowd navigation with a loaded tray |

**Long-term progression:** Higher bar reputation means more tables, more complex orders, higher-paying clientele, access to private events (story content), and NPC connections through regulars who become recurring characters in Meghan's life.

---

### UC2 — Running a Delivery (Courier)

**Actor:** The player
**Goal:** Pick up a package and deliver it to a destination, earning money based on speed and condition
**Schedule:** On demand via the delivery app (phone widget)
**Available from:** Immediately — NPC dispatcher at the courier hub, delivery app financed by Terry

The courier job is the city exploration engine. A player who couriers extensively knows Terridyn's geography — shortcuts, restricted areas, traffic patterns, checkpoint locations — before they need that knowledge for anything more dangerous.

**A delivery, step by step:**

1. **Accept a job from the delivery app.** The app shows available deliveries with pickup location, delivery address, estimated distance, time window, and pay. The player chooses which to accept
2. **Navigate to the pickup location.** The GPS widget (if equipped — see [CB-hud-modification](CB-hud-modification.md)) provides routing; without it, the player uses the phone map manually (must stop to check, costs time)
3. **Collect the package.** Packages have a hand cost (small parcel = 0.5, standard box = 1.0, large crate = 2.0 requiring both hands). Fragile packages carry a damage sensitivity — rough handling, drops, and collisions degrade package integrity
4. **Navigate to the delivery address.** This is the core gameplay: traversal through the city under time pressure. The route is the player's choice — follow GPS directions on safe roads, or take shortcuts through alleys, rooftops, and restricted areas
5. **Handle obstacles en route:**
   - **Traffic and crowds** — Driving skill (on vehicles) or Parkour/Running (on foot) to navigate efficiently
   - **Access restrictions** — gated areas, locked doors, security checkpoints. Options: Negotiation (talk through), Hacking (electronic bypass), Parkour (go over/around), or simply find another route
   - **Time pressure** — the delivery window is real. Late deliveries pay less and damage reputation
   - **Weather and terrain** — environmental conditions affect vehicle handling and foot traversal
   - **Package integrity** — rough vehicle handling, falls during parkour, and combat encounters can damage the package. A destroyed package fails the delivery entirely
6. **Deliver the package.** Hand it to the recipient at the address
7. **Rating received:** Based on delivery time (early, on-time, late) and package condition (pristine, minor damage, significant damage, destroyed). Both factors affect pay and reputation

**Vehicle progression:**

| Vehicle | Unlock | Capacity | Speed | Range |
|---------|--------|----------|-------|-------|
| On foot | Default | Hands only | Running + Parkour | Full city (slow) |
| Bicycle | Early purchase | Small rack | Moderate | City-wide |
| Motorbike | Mid-game purchase | Side bags | Fast | City + outskirts |
| Hoverbike | Late Ch.2 / Ch.3 | Cargo mount | Fastest | Full region |

Vehicles require maintenance — damage from crashes, wear from use. This feeds into the Mechanic job relationship: a courier who also repairs saves money; one who doesn't pays mechanics regularly.

**What trains:**

| Skill | How it trains |
|-------|---------------|
| Running | On-foot deliveries, sprinting to meet time windows |
| Driving | Vehicle operation, traffic navigation |
| Parkour | Shortcut routes, rooftop traversal, obstacle bypass |
| Perception | Route awareness, spotting shortcuts, noticing hazards |
| Negotiation | Checkpoint bypass, tipping interactions |
| Hacking | Electronic gate bypass, security system circumvention |

**Long-term progression:** Higher courier reputation unlocks better-paying routes, larger delivery contracts, access to restricted-area deliveries (corporate, government), and vehicle upgrade opportunities. Extensive delivery experience also provides city knowledge that transfers to every other job and story mission.

---

### UC3 — Performing a Repair (Mechanic)

**Actor:** The player
**Goal:** Diagnose and repair a customer's vehicle or device, earning money based on work quality
**Schedule:** On demand at the repair shop
**Available from:** Organic introduction — a delivery to a repair shop leads to a trial repair

The mechanic job bridges the gap between traversal skills and technical capability. It trains the engineering toolkit that becomes essential for CAMERA upgrades, Xtalics work at Machina Dynamics, and equipment maintenance.

**A repair, step by step:**

1. **Customer arrives at the repair shop** with a vehicle or device. The customer describes the problem in general terms (may be inaccurate — customers don't always know what's wrong)
2. **Diagnostic phase — inspection minigame.** The player physically examines the item using Perception:
   - Visual inspection of components — the player moves a cursor or camera over the item, examining individual parts
   - Highlighted areas indicate potential issues (skill-gated — higher Perception reveals more subtle problems)
   - Multiple faults may be present. Finding all of them improves repair quality and customer satisfaction
   - Misdiagnosis is possible — identifying the wrong component wastes time and materials
3. **Select a repair approach.** For most repairs, multiple valid approaches exist:
   - **Quick fix** — faster, cheaper materials, lower quality outcome. Good for low-value clients or time pressure
   - **Standard repair** — balanced cost, time, and quality
   - **Premium repair** — expensive materials, longer time, highest quality. Justifies higher pricing
   - **Unauthorised modification** — DRM bypass required (Hacking skill). Higher risk, potentially higher reward, faction implications if the client is using it for illegal purposes
4. **Execute the repair — assembly/connection puzzle minigame.** This is the core mechanic gameplay:
   - Fit components into position (spatial reasoning)
   - Route circuits and connections (path-finding logic)
   - Calibrate tolerances (precision timing or value matching)
   - Engineering skill determines available tools, techniques, and error tolerance within the puzzle
   - The puzzle difficulty scales with the repair complexity. Early repairs are straightforward; advanced repairs involve multi-step sequences with tight tolerances
5. **Negotiate payment with the client.** Price is not fixed — it's a Negotiation interaction:
   - Base price reflects work quality and materials used
   - Negotiation skill and client relationship affect the final amount
   - Repeat clients pay better; first-time clients may haggle harder
   - Gang clients may threaten if unsatisfied; corporate clients may offer ongoing contracts if impressed
6. **Faction implications based on client type:**
   - **Civilian clients** — neutral; reputation builds organically
   - **Gang clients** — higher pay, repair requests may involve stolen vehicles or illegal modifications. Accepting builds gang reputation, may degrade law enforcement trust
   - **Corporate clients** — steady work, lower individual pay, potential for contract arrangements. Working for one corporation may alienate rival companies

**Toolkit requirement:** The mechanic job requires a toolkit (purchased item). Toolkit tier gates what repairs the player can attempt:

| Toolkit tier | Capability | Approximate cost |
|-------------|-----------|-----------------|
| Basic | Simple mechanical repairs, routine maintenance | Entry-level |
| Standard | Electrical work, moderate vehicle repair | Mid-range |
| Advanced | Complex diagnostics, electronic component work | Expensive |
| Specialist | DRM bypass tools, Xtalics-compatible instruments | Very expensive / quest-gated |

**What trains:**

| Skill | How it trains |
|-------|---------------|
| Engineering | Every repair — the core mechanic skill |
| Repair | Restoration quality, material efficiency |
| Perception | Diagnostic accuracy, fault identification |
| Negotiation | Client pricing, repeat business |
| Hacking | DRM bypass on locked devices, unauthorised modifications |

**Long-term progression:** Higher mechanic reputation unlocks corporate repair contracts, rare material access, advanced toolkit purchases, and the engineering foundation needed for CAMERA upgrades at Machina Dynamics in Chapter 3.

---

### UC4 — Executing a Hack (Hacker)

**Actor:** The player
**Goal:** Infiltrate a location, access a terminal, complete a hacking objective, and exfiltrate
**Schedule:** On demand via contacts or burner phone
**Available from:** After hacking 5 electronic locks without detection — contacted via the delivery app or an anonymous burner phone

The hacker job is the stealth-intelligence hybrid. Every contract involves two phases: physical infiltration (getting to the terminal) and digital intrusion (the hacking puzzle itself). Failure consequences are the harshest of any non-bounty job — device lockouts, police flags, and restricted digital access persist until resolved.

**A hacking contract, step by step:**

1. **Accept a contract.** Hacking contracts come through two client pools:
   - **Activist groups** — lower pay, but provide information, connections, and access to underground networks. Ideologically motivated targets (corporate surveillance, government records, media manipulation)
   - **Authorities and corporations** — higher pay, morally ambiguous targets (competitor espionage, citizen surveillance, evidence suppression). Working both pools degrades trust with each
2. **Briefing.** The contract specifies: target location, objective type (data extraction, system modification, evidence planting/deletion), time constraint (if any), and known security measures. Higher-reputation contracts provide better intel
3. **Travel to the target location.** This is not a fast-travel job — the player physically navigates to the site. Locations vary from office buildings to rooftop server farms to underground facilities
4. **Infiltrate.** Getting to the terminal is its own challenge:
   - **Parkour** — rooftop access, construction site traversal, vent navigation, fire escape routes
   - **Stealth** — avoid patrols, security cameras, and building occupants. Detection before reaching the terminal may abort the mission or escalate difficulty
   - **Hacking (peripheral)** — disable security systems, unlock electronic doors, loop camera feeds on the approach
   - **Negotiation** — social engineering past guards, receptionists, or building staff. Higher risk of being remembered
5. **Reach the terminal — hacking puzzle minigame.** This is the core hacking experience:
   - **Pattern matching** — identify and replicate data sequences under time pressure
   - **Sequence breaking** — find the correct order of operations to bypass security layers
   - **Node routing** — navigate a network topology, choosing paths that avoid detection nodes while reaching the objective
   - All three elements may combine in a single hack. Difficulty scales with contract tier
   - **Detection timer** — a progress bar representing the system's intrusion detection. The timer runs throughout the puzzle. If it fills, the hack is detected — consequences depend on how close to completion the player was
   - **Hacking skill** determines available tools within the puzzle: more commands, faster execution, wider error margins, ability to pause or reset the detection timer temporarily
6. **Complete the objective.** Data extracted, system modified, evidence handled. The contract specifies what must be done
7. **Exfiltrate.** Leaving is not automatic. The player must physically exit the location. If detection occurred during the hack, security may be heightened — patrols redirected, doors locked, authorities en route. Clean hacks allow clean exits; messy hacks require escape skills

**Failure consequences (persistent):**

| Failure type | Consequence | Resolution |
|-------------|-------------|------------|
| Detected during hack | Target system locks out Meghan's signature; future hacks at that location are harder | Time passes (lockout expires) or new exploit found |
| Caught on-site | Police flag added to STALKER profile; increased scrutiny during other activities | Flag decays over time or removed via counter-hack |
| Contract failure | Reputation loss with the contracting client pool; may affect future contract availability | Successful contracts rebuild trust |

Hacker job failures do NOT feed into the failure pipeline (that is reserved for bounty hunting). But the persistent digital consequences — lockouts, flags, restricted access — affect Meghan's daily life beyond the job itself.

**What trains:**

| Skill | How it trains |
|-------|---------------|
| Hacking | Every hack — the core intelligence skill; transfers to courier gate bypass, mechanic DRM, bounty intel |
| Perception | Site reconnaissance, security system identification |
| Stealth | Physical infiltration, patrol avoidance, camera evasion |
| Negotiation | Social engineering past building staff |

**Long-term progression:** Higher hacker reputation unlocks more lucrative contracts, better pre-mission intel, access to exclusive digital tools, and connections to underground networks that provide story content and NPC relationships unavailable through other jobs.

---

### UC5 — Completing a Bounty (Bounty Hunter)

**Actor:** The player
**Goal:** Locate, subdue, and deliver a target for a bounty contract
**Schedule:** On demand via contracts
**Available from:** Two routes — official (10 courier + 3 hacking + 5 combat encounters, then approached by a contact) or freeform (organic city reputation through unsolicited interventions)

The highest-risk, highest-reward job. Bounty hunting is the primary vehicle for paying down the debt to Terry — side jobs cover survival expenses, but bounty income is what reduces the debt. This job exercises the full combat toolkit and is the reason Meghan needs to be dangerous.

**A bounty contract, step by step:**

1. **Accept a contract from a client.** Contract sources vary:
   - **Police** — volume-based, legitimate targets (bail jumpers, warrant subjects). Straightforward contracts, moderate pay
   - **Corporations** — specialist contracts tied to corporate interests. Mautorium contracts favour stealth; JMC contracts favour strength; Hitsunami contracts favour tactical approach
   - **Gang leaders** — rival elimination or retrieval. Higher pay, morally complex, faction reputation consequences
   - **Civilians** — stolen goods recovery, missing persons, personal grudges. Variable pay, sometimes lead to unexpected story content
2. **Contract briefing.** The contract provides: target identity (name, description, last known location), reason for the bounty, delivery location, pay, and time constraint (if any). Higher-reputation contracts provide better target intel — more recent location data, known associates, combat capability assessment
3. **Investigation phase — Search skill.** Locating the target is active player work:
   - **NPC interviews** — ask around at known locations. Small Talk and Negotiation determine how much information NPCs share
   - **Record access** — hack databases, access public records, check STALKER data (Hacking skill)
   - **Physical observation** — stake out locations, follow leads, observe patterns (Perception + Search)
   - The investigation phase may take in-game hours or days depending on the target's profile and the player's skill investment. Higher Search skill finds better leads faster
4. **Locate the target.** Investigation narrows the target's location. The player physically travels there
5. **Approach — player choice:**
   - **Stealth approach** — get close without the target knowing. Stealth skill determines detection radius. Allows a surprise grab attempt (advantage on the initial grapple)
   - **Direct approach** — announce presence, demand surrender. Negotiation may convince a low-will target to comply without a fight. Most targets run or fight
   - **Lure** — use information from the investigation to draw the target to a controlled location. Requires planning and setup
6. **Engage the target.** This is combat per [CB-combat](CB-combat.md). The critical difference from other combat: the target must be taken alive. The no-death rule means the target is never killed, but the contract requires conscious delivery — a KO'd target must be restrained before they wake up
7. **Subdue via grapple chain** (per [CB-combat UC6](CB-combat.md)):
   - **Grab** — initiate grapple (ACC + Martial Arts vs target AGI + Evasion)
   - **Pin** — advance from grappled to pinned (STR + Martial Arts vs target STR + Escape Artist)
   - **Bind** — apply restraint item (AGI + Binding vs target STR + Escape Artist). Requires restraint item in hand (rope, cuffs, cable ties — purchased from [CB-inventory](CB-inventory.md) stock)
   - Multiple restraints may be needed for strong targets
   - The target resists throughout — SP attrition war. A low-SP Meghan cannot maintain a grapple
8. **Deliver the bound target** to the specified location. The target is a physical object Meghan must transport. Options depend on vehicle availability and target cooperation:
   - Walk/drag on foot (slow, conspicuous)
   - Vehicle transport (faster, requires loading the target)
   - Public or private transport depending on the contract's legality
9. **Payment received.** Amount depends on contract terms, target condition (uninjured bonus vs roughed-up penalty, where applicable), and delivery time

**Failure routes:**

Bounty hunting failure feeds into the **failure pipeline** documented in [Core Loop](../gdd/core-loop.md#the-failure-pipeline). A failed bounty does not trigger a game over — Meghan enters the black market holding pipeline and must escape. This is failure content, not a dead end.

| Failure type | Consequence |
|-------------|-------------|
| Target escapes | Contract failed, reputation loss, target is harder to find next time |
| Meghan KO'd by target | Enters the pipeline — black market holding, escape required |
| Meghan KO'd by third party during bounty | Same pipeline entry |
| Target delivered late | Reduced pay, minor reputation impact |

**What trains:**

| Skill | How it trains |
|-------|---------------|
| Melee Combat | Physical engagement with the target |
| Ranged Weaponry | Ranged engagement, suppression |
| Stealth | Approach, observation, ambush positioning |
| Negotiation | Client interaction, target surrender, NPC interviews |
| Binding | Restraint application on pinned targets |
| Hacking | Record access during investigation, electronic surveillance |
| Search | Investigation phase — finding the target |
| Martial Arts | Grapple chain execution |

**Long-term progression:** Higher bounty hunter reputation unlocks better-paying contracts, higher-profile targets, corporate specialist contracts, access to restricted equipment (restraint tools, surveillance gear), and NPC connections in law enforcement and the underworld. Bounty hunting is also the primary debt repayment vehicle — the faster Meghan builds bounty reputation, the faster she can address the debt to Terry.

---

### UC6 — Discovering and Unlocking Jobs

**Actor:** The player
**Goal:** Gain access to each of the five jobs through gameplay

Jobs unlock through a staggered sequence — the player is never presented with all five simultaneously. This paces the introduction of complexity and ensures each job receives focused attention before the next appears.

**Unlock sequence:**

| Job | Unlock trigger | Timing |
|-----|---------------|--------|
| **Waitress** | Story event — Celeste introduces Meghan to Burton | Early Chapter 1 |
| **Courier** | Available immediately via NPC dispatcher at courier hub + delivery app (financed by Terry) | Game start |
| **Mechanic** | Organic — a delivery to a repair shop leads to conversation, then a trial repair | After several courier deliveries |
| **Hacker** | 5 electronic locks hacked without detection → contacted via delivery app or anonymous burner phone | After demonstrating hacking aptitude |
| **Bounty Hunter** | Two routes: official (10 courier + 3 hacking + 5 combat encounters) OR freeform (organic city reputation) | Mid Chapter 1 to early Chapter 2 |

**Unlock details:**

**Waitress:** The player cannot seek this out independently. It is a story-gated introduction — Celeste takes Meghan to Burton's Bar as part of helping her settle in. Burton offers a trial shift. Completing the trial makes the job available on an ongoing basis.

**Courier:** The lowest barrier. Terry finances Meghan's smartphone (monthly repayment with interest — this is Terry's first economic hook into Meghan's life). The delivery app comes pre-installed. An NPC dispatcher at the courier hub explains the system. The player can start delivering immediately.

**Mechanic:** The player does not apply for this job. During a delivery to a repair shop, the shop owner notices Meghan's interest (or competence, based on dialogue choices) and offers a trial repair. Completing the trial successfully opens the job. This organic discovery rewards the player who explores conversations during courier deliveries rather than rushing through them.

**Hacker:** The game tracks Meghan's hacking activity from the start. Every electronic lock bypassed during courier deliveries, exploration, or story missions counts. After 5 successful hacks without triggering detection, an anonymous contact reaches out through the delivery app or via a burner phone drop. The contact offers a contract, and completing it opens the job channel. The player who never hacks may never unlock this job in a given playthrough.

**Bounty Hunter:** Two parallel unlock paths:
- **Official:** Complete 10 courier jobs + 3 hacking contracts + survive 5 combat encounters. A contact approaches Meghan with a proposition. This path ensures baseline competence across traversal, intelligence, and combat
- **Freeform:** Build organic city reputation through unsolicited interventions — stopping muggings, helping in fights, visibly handling dangerous situations. The city notices, and a contact reaches out. This path rewards the player who engages with the world proactively

Both paths lead to the same first bounty contract. The player can satisfy either path without knowing the other exists.

---

### UC7 — Managing Job Reputation

**Actor:** The player
**Goal:** Build and maintain reputation across jobs and faction relationships

Each job has an independent reputation track. Reputation is not a single number — it is a portfolio the player actively manages. Client choices within each job are where faction management happens.

**Reputation mechanics:**

- **Increases through:** Successful job completion, high-quality work, client satisfaction, reliability (on-time delivery, clean hacking, well-executed bounties)
- **Decreases through:** Failed jobs, poor quality, missed deadlines, client conflicts, faction betrayal

**What reputation unlocks:**

| Reputation tier | Effect |
|----------------|--------|
| **Low** | Basic contracts, standard pay, limited client pool |
| **Medium** | Better-paying contracts, equipment access, NPC introductions, repeat client relationships |
| **High** | Rare contracts, exclusive materials, story events, faction-level access, NPC connections that provide information and assistance outside the job context |

**Faction tension — the reputation portfolio:**

The hacker, mechanic, and bounty hunter jobs each involve clients whose interests conflict. The player cannot maximise reputation with all factions simultaneously — choices have consequences.

| Faction relationship | Tension |
|---------------------|---------|
| **Activists ↔ Corporations** | Hacking for activists undermines corporate interests; hacking for corporations undermines activist trust |
| **Gangs ↔ Law enforcement** | Mechanic work on gang vehicles, bounties for gang leaders — both degrade the relationship with the opposing faction |
| **Corporation A ↔ Corporation B** | Mechanic or bounty contracts for one corporation may alienate rival companies |
| **Activists ↔ Law enforcement** | Overlapping tensions with hacking and bounty work |

**Client selection is faction management.** Within each job, the player sees available contracts from different client types. Choosing which contracts to accept — and which to decline — is how the player shapes their faction portfolio. A player who consistently takes activist hacking contracts and police bounties builds a specific reputation profile that opens some doors and closes others.

**Cross-job reputation effects:** Reputation in one job can affect opportunities in another. A mechanic known for gang work may receive bounty contracts targeting gang rivals. A hacker known for corporate work may receive mechanic contracts from those same corporations. The reputation system creates an interconnected web — the player's choices ripple across all five jobs.

**Chapter 3 transition:** When Meghan begins the Machina Dynamics internship in Chapter 3, the existing job reputation portfolio does not reset. Corporate reputation earned through mechanic and hacking work affects how MD colleagues and competitors view Meghan. Bounty hunting reputation affects how dangerous she is perceived to be. The portfolio built in Chapters 1–2 has consequences that extend through the entire game.

---

## Open Items

- **Waitress order memory system** — exact mechanics for how the player remembers orders. Pure memory? Note-taking option? Visual cues? How does difficulty scale from "two beers" to complex orders with modifications?
- **Hacking puzzle design** — the three puzzle types (pattern matching, sequence breaking, node routing) need full minigame specifications. What are the inputs? What does the player see? How does the detection timer interact with each puzzle type?
- **Mechanic assembly puzzle design** — the spatial/connection/calibration puzzle needs full specification. What components are manipulated? How does the player interact with them? How does Engineering skill modify the puzzle?
- **Vehicle progression tiers and costs** — exact prices for bicycle, motorbike, hoverbike. Maintenance costs. Insurance? Theft risk?
- **Exact reputation thresholds and unlock gates** — how many successful jobs to reach each tier? How many failures to drop a tier?
- **Faction relationship matrix** — full mapping of which client types conflict with which. Quantified tension values (does one activist job cancel out one corporate job, or is the ratio asymmetric?)
- **Ch.3 Machina Dynamics interaction** — does the internship replace existing jobs? Supplement them? Reduce available time for them? How does the economic transition work?
- **Content-gated job opportunities** — Spicy and Super Spicy tier jobs are mentioned in [Core Loop](../gdd/core-loop.md#content-gated-jobs). What are they? How do they unlock? What do they train?
- **Non-bounty, non-hacking job failure consequences** — beyond lost time and income, does failing a waitress shift, delivery, or mechanic repair have lasting consequences? Reputation loss only, or additional effects?
- **Mechanic toolkit tiers** — exact capabilities per tier. Can higher tiers be found, or must they all be purchased? Quest-gated specialist tools — what quests?
- **Delivery app UI** — is this a phone widget (consuming widget points) or a separate phone app section (always available when phone is in hand)?
- **Bounty contract quality scaling** — do higher-reputation contracts provide better target intel (last seen location, combat assessment, known associates)? How much better?
- **Waitress tip calculation** — what formula? Skill-based (Small Talk, Conflict Resolution)? Customer satisfaction score? Random element? All three?
- **Whether studying at MCI counts as a "job"** in this framework or is documented separately (currently in [Core Loop — School Calendar](../gdd/core-loop.md#the-school-calendar--primary-structural-pressure))
- **Courier GPS widget** — does this consume widget points from the eye AR or CAMERA budget? Or is it a phone-only tool?
- **Bounty Hunter restraint item economy** — where are restraints purchased? Do they have a per-use cost (cable ties consumed) or durability (rope reusable)?
- **Mechanic DRM bypass — legal consequences** — does performing unauthorised modifications create a paper trail? Can it lead to police flags like hacking failure?
- **Bar shift scheduling flexibility** — can Meghan leave a shift early? Arrive late? What are the reputation consequences?
- **Bounty target combat capability scaling** — how do targets scale with player capability? Fixed per contract, or dynamically adjusted?
