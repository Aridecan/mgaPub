# Creative Brief — Repair (Signal Routing)

---

## Overview

Repair in MGA is resolved through a two-phase interactive system: **diagnosis** (figure out what's broken) followed by **signal routing** (bridge the damaged connections using available materials). The player investigates symptoms, narrows down faults, then lays wire routes across a grid to reconnect signals — all while managing a limited material budget.

Repair is the most economically self-contained job. Unlike bounty hunting (combat skills), waitressing (relationships + city rumours), hacking contracts (Tabithia missions + CAMERA), or delivery (city knowledge), repair is a direct economic loop: materials in, credits out, skill progression along the way. The real payoff comes when those skills are needed for field repairs during missions.

Six skills feed into repair performance: Repair (core), Engineering (system architecture + optimal paths), Knowledge Recall (manufacturer failure patterns), Problem Solving (symptom reasoning + constraint analysis), Search (spotting subtle damage), and Intuition (sensory diagnosis).

**Related documents:**

- [Repair Minigame Detail](../../mgaPriv/mechanics/skills/repair-minigame.md) — full mechanic specification
- [Repair Skill](../../mgaPriv/mechanics/skills/repair.md) — skill progression, training, synergies
- [Engineering Skill](../../mgaPriv/mechanics/skills/engineering.md) — design, construction, and system understanding
- [Hacking Minigame](../../mgaPriv/mechanics/skills/hacking-minigame.md) — electronic security counterpart (the Cylinder)
- [CB-jobs](CB-jobs.md) — job system creative brief

---

## UC1 — Simple Repair (Learning the Mechanic)

**Context:** Meghan takes her first repair job at the mechanic shop — a customer's broken power tool with a fried wire harness. 1 fault, 2–3 signals.

**Player experience:**

- The power tool is placed on the workbench — it won't turn on
- Phase 1: Visual inspection shows a burned-out wire harness — diagnosis is trivial, the damage is obvious
- Phase 2: The board view appears — a small grid with 2 signal sources on the left and 2 destinations on the right
- The gap where the harness was is a damaged zone in the middle
- The player lays wire routes around the damage, connecting source A to destination A and source B to destination B
- Routes are short, material is plentiful, no bridges needed
- The repair completes — the tool powers on, the customer pays

**What the player learns:** The core loop — inspect the damage, identify the fault, then route signals from source to destination around damaged zones. Materials are consumed by routing. Shorter routes use less material.

---

## UC2 — Diagnosis Challenge (Finding the Fault)

**Context:** A customer brings a vehicle control unit that "sometimes works." No visible damage.

**Player experience:**

- Visual inspection reveals nothing — the board looks clean
- The player runs signal tests — sends test signals through paths and reads outputs
- Path A checks out fine. Path B checks out fine. Path C returns a wrong output.
- The player isolates Path C and runs component tests on each node along it
- Node 3 of Path C is the fault — a component that passes idle tests but fails under load
- A load test confirms: the component works at low signal strength but breaks down at operating levels
- **Knowledge Recall** kicks in: "This is a Mautorium power coupling — the thermal paste dries out after 6 months"
- The player could have skipped three tests and gone straight to the likely component
- Now Phase 2 begins — route around the failed component

**What the player understands:** Diagnosis is not always obvious. Intermittent faults require deeper investigation. Knowledge Recall and Problem Solving shortcut the detective work — experience is worth more than brute-force testing.

---

## UC3 — Material-Constrained Repair (Tight Budget)

**Context:** A standard repair job — 5 signals across a medium board with several damaged zones. Meghan's material inventory is low.

**Player experience:**

- Diagnosis is straightforward — two components failed in a cascading failure
- Phase 2: The board has 5 signals to route, but multiple damaged zones force long detours
- The obvious routing (around the large damaged zone) would use more wire than Meghan has
- The player must find **efficient paths** — threading routes through narrow gaps between damaged zones
- One route needs to cross another — a bridge component is required, and Meghan only has one
- The player rearranges routes to minimise crossings, saving the bridge for the one unavoidable intersection
- The repair completes with material to spare — barely
- A less efficient routing would have run out of wire on signal 4 of 5, forcing Meghan to buy more materials (eating into profit) or salvage from the shop's junk pile

**What the player feels:** Material matters. The routing puzzle isn't just "connect A to B" — it's "connect A to B using as little material as possible." Efficient routing is the difference between profit and loss. Higher Repair skill means less material consumed per route, widening the margin.

---

## UC4 — Field Repair Under Pressure

**Context:** Meghan's vehicle breaks down during a mission. She needs to fix it to reach the objective before the window closes.

**Player experience:**

- The vehicle won't start — engine turns over but stalls immediately
- Phase 1: Meghan pops the access panel. Visual inspection shows a melted connector assembly near the fuel injection system
- **Problem Solving** narrows it down: "Stalling on start means fuel delivery, not ignition — check the injection routing"
- A signal test confirms: the fuel injection control signals aren't reaching the injectors
- Phase 2: The board is the vehicle's injection control harness — 4 signals, medium grid, one large damaged zone where the connector melted
- Meghan has limited field materials — what's in her pack, not a full shop inventory
- She routes three signals cleanly but the fourth requires a long detour that would use her remaining wire
- She strips a non-essential wire from the vehicle's interior lighting circuit — salvaged material, just enough
- The vehicle starts. The interior lights don't work anymore. Good enough for the mission.

**What the player understands:** Field repair is the same puzzle under harder constraints. Less material, no shop to buy from, and time pressure from the mission. Improvisation and salvage fill the gaps. The skills built doing repair jobs pay off when it matters.

---

## UC5 — Cascading Failure (Multi-Fault Diagnosis)

**Context:** A complex piece of machinery from the mechanic job — an industrial fabricator that's producing defective output.

**Player experience:**

- The fabricator runs but its output is wrong — dimensions are off, material bonds are weak
- Visual inspection shows no obvious damage
- Signal tests reveal Path D is carrying the wrong signal strength — the calibration is off
- The player replaces the calibration component and tests again
- Output improves but is still wrong — the bonds are still weak
- A deeper diagnosis reveals: the original calibration failure ran the heating element at wrong temperatures for weeks, which degraded the heating element's wiring
- **Problem Solving** connects the chain: "Bad calibration → wrong temperatures → heat damage to the heater wiring"
- The player now has a second fault to fix — the heater's wire harness
- Phase 2 runs twice: once for the calibration routing (simple, 2 signals) and once for the heater routing (moderate, 4 signals with heat-damaged zones)
- After both repairs, the fabricator runs correctly

**What the player understands:** Complex systems have cascading failures. Fixing the obvious problem doesn't always fix the system. Diagnosis is iterative — repair, test, diagnose again. Knowledge Recall helps: "These fabricators always cook their heater wiring when the calibration drifts."

---

## UC6 — CAMERA Hardware Repair

**Context:** Meghan's CAMERA hardware takes damage during a mission — her surveillance drone clips a wall and the main board cracks.

**Player experience:**

- The drone's camera feed is dead and the signal relay isn't responding
- Phase 1: Meghan opens the chassis. The main board has a visible crack across 3 trace paths
- **Engineering** identifies the affected subsystems: "The crack crosses the camera bus, the signal relay, and the power regulation line — all three need rerouting"
- Phase 2: The board is the drone's main PCB — 6 signals, large grid, the crack is a long diagonal damaged zone
- The routing is complex: signals must cross the crack zone at narrow points, and the camera bus signals need shielded wire (standard wire would introduce interference)
- Meghan has shielded wire for the camera bus but it's expensive — using too much eats into her CAMERA upgrade budget
- **Planning** shows a ghost overlay suggesting an efficient path that threads two routes through the same narrow gap across the crack
- The repair completes. The drone is functional again, but Meghan used 3 units of shielded wire that she'd been saving

**What the player understands:** CAMERA hardware repair is the high-end application of the repair system. The signals are more demanding, the materials are more expensive, and the board complexity is higher. Engineering and Planning skills shine here — they show optimal paths that save expensive materials.

---

## UC7 — The Repair Job (Economic Loop)

**Context:** Meghan works a shift at the mechanic shop — multiple repair jobs available, each with visible difficulty and payment.

**Player experience:**

- The job board shows three available repairs:
  - Broken comm unit — Easy, 2 signals, pays 50 credits
  - Vehicle sensor array — Medium, 5 signals, pays 150 credits
  - Industrial motor controller — Hard, 7 signals, pays 300 credits
- Meghan has moderate materials in stock and intermediate Repair skill
- She takes the vehicle sensor array — good pay for her skill level
- Diagnosis takes 2 tests (Knowledge Recall identified the likely fault). Routing uses 4 of her 6 available wire units
- Payment: 150 credits. Materials consumed: ~80 credits worth. Profit: ~70 credits
- She considers the industrial motor controller — higher pay, but she'd burn through her remaining materials and might need to buy more mid-repair
- She takes the comm unit instead — fast, cheap, guaranteed profit
- End of shift: 2 repairs completed, ~100 credits profit, Repair skill progressed

**What the player understands:** The repair job is an economic puzzle layered on top of the routing puzzle. Which jobs to accept depends on skill level, material inventory, and the profit-to-cost ratio. Higher skill = more efficient routing = more profit from the same job. The player is incentivised to build Repair skill because it directly increases income.

---

## Open Items

- Grid dimensions for routing boards — what's the playable area for each complexity tier?
- Visual design of the board, signal sources/destinations, damaged zones, and wire routes
- Audio design — testing sounds, routing placement feedback, completion/failure
- Whether routes can be partially undone (remove and reclaim some material) or if placement is permanent
- How CAMERA hardware routing rules differ from standard repairs — crystal-adjacent specifics TBD
- Material pricing and availability across shops and regions of the city
- Salvage mechanics — what can be stripped from junk/robots, and how?
- Whether the repair job has a reputation system (quality/speed affects customer flow)
- How Engineering crafting of bridge components works
- Whether inferior/improvised materials have any lasting mechanical consequence or if all completed repairs are equal
- Repair job difficulty progression — does the shop offer harder jobs as Meghan's reputation grows?
