# Creative Brief — Hacking (The Cylinder)

---

## Overview

Hacking in MGA is resolved through an interactive logic puzzle called **the Cylinder** — a spinning drum with concentric layers that the player peels back to reach the core. Each layer is divided into sections containing logic circuits. The player inserts hardware interface keys at the cylinder ends to send signals through the circuits, solving sections to peel layers away while managing watchdog timers and avoiding police blocks that raise the alert level.

The Cylinder is the electronic counterpart to the [concentric ring minigame](../../mgaPriv/mechanics/skills/lock-picking.md) used for mechanical locks. Any electronic system — door locks, energy tether restraints, corporate databases, security overrides, drone reprogramming — uses the Cylinder.

Eight skills feed into hacking performance: Hacking (core), Engineering (key identification), Knowledge Recall (pre-reveal without probing), Research (pre-mission intel), Problem Solving (pattern extrapolation), Target Analysis (critical path), Situational Awareness (alert/watchdog visibility), and Planning (solve order optimisation).

**Related documents:**

- [Hacking Minigame Detail](../../mgaPriv/mechanics/skills/hacking-minigame.md) — full mechanic specification
- [Hacking Skill](../../mgaPriv/mechanics/skills/hacking.md) — skill progression, training, synergies
- [Escape Artist](../../mgaPriv/mechanics/skills/escape-artist.md) — restraint escape flow (hacking = electronic restraint stage)
- [Lock Picking](../../mgaPriv/mechanics/skills/lock-picking.md) — mechanical lock counterpart
- [CB-lock-picking](CB-lock-picking.md) — lock picking and restraint escape creative brief
- [CB-knockout-recovery](CB-knockout-recovery.md) — brainwave sync revival (another minigame system)

---

## UC1 — Simple Electronic Lock (Learning the Mechanic)

**Context:** Meghan encounters a basic electronic padlock — a storage locker, a cheap door, or simple energy tether restraints. 1 layer, 2–3 sections.

**Player experience:**

- The Cylinder appears — a small spinning drum with a few visible sections
- The player stops the spin to inspect — the alert bar begins to fill slowly
- The cylinder ends show notch patterns; the player selects a key from their toolkit that matches the pin configuration
- The key is inserted at one end — signals flow through the section's logic circuit
- If the signals reach the release port, the section solves and flips away
- With 2–3 sections and no watchdog timers, this is a brief puzzle — understand the circuit, insert the right keys, solve in sequence
- When all sections are solved, the layer peels away and the core is exposed; one final key insertion completes the hack

**What the player learns:** The core loop — inspect the cylinder, select keys, insert at the ends, read the circuit, solve sections. The alert bar exists but is forgiving on simple systems. Wrong keys send wrong signals and waste time.

---

## UC2 — Key Selection (The Interface Problem)

**Context:** The player faces a system where multiple keys look physically similar but have different active pin configurations.

**Player experience:**

- The notch pattern on the cylinder end accepts several keys from the toolkit — they all physically fit
- But different keys have different active pins — like an RJ45 connector where 10Base-T uses 2 pairs and 100Base-T4 uses all 4
- Inserting the wrong key sends signals through the circuit — but the wrong signals
- Wrong signals may route to police blocks (alert spike), miss the release port entirely, or disrupt a watchdog's pet signal
- The player must identify the correct key type before inserting
- **Engineering** recognises the interface type from the pin pattern without trial-and-error
- **Knowledge Recall** recalls which key type matches which notch configuration from prior experience

**What the player understands:** Keys are not interchangeable. Physical fit is not the same as correct function. Skills that identify the right key before insertion save noise and avoid cascading problems.

---

## UC3 — Multi-Section Layer with Cross-Communication

**Context:** A corporate terminal with 3–4 sections per layer. Sections cross-communicate — signals in one section flow into adjacent sections.

**Player experience:**

- The player probes sections to see their circuit layouts (each probe costs alert)
- Section A's circuit has a path that crosses into section B
- A key inserted to solve section A also sends signals into section B — which might hit section B's release port (solving both), or might route into section B's police block
- The player must think about the entire layer's signal flow, not just individual sections
- **Problem Solving** helps: after probing one section, the player extrapolates adjacent circuit configurations
- **Target Analysis** highlights which sections are critical-path and which police blocks to avoid
- The player finds the key configuration and insertion order that chains through the layer cleanly

**What the player feels:** The puzzle is interconnected. Individual sections cannot be solved in isolation — the whole layer is one system. Finding the configuration that cascades through multiple sections is deeply satisfying.

---

## UC4 — Watchdog Timer Management

**Context:** A secure system with watchdog timers that must be managed during the solve.

**Player experience:**

- The player probes and discovers a watchdog timer in section C
- Section A's circuit is petting the watchdog — sending a heartbeat signal on a regular cycle
- If the player solves section A first, the heartbeat stops and the watchdog countdown begins
- **Option 1:** Solve section C first (remove the watchdog before severing its heartbeat)
- **Option 2:** Insert a key that generates the pet signal before solving section A (reroute the heartbeat)
- **Option 3:** Solve section A and race to solve section C before the watchdog expires
- **Situational Awareness** shows the watchdog countdown timer
- **Planning** reveals the dependency chain and optimal solve order
- If the watchdog expires on a complex system: the alert floor permanently rises (security floor mode)
- If the watchdog expires on a simple system: phase 2 resets entirely, mandatory reboot wait (reset mode)

**What the player understands:** Solve order matters. Sections have dependencies. Watchdogs turn the logic puzzle into a sequencing puzzle — you can't just solve sections in any convenient order.

---

## UC5 — Port Exposure (Layer Transitions)

**Context:** A multi-layer system where solved sections expose ports on deeper layers.

**Player experience:**

- The player solves section B on layer 1 — it flips away
- Beneath section B, previously blocked ports on layer 2 are now exposed
- These ports are the only place to insert keys for layer 2's sections
- The player realises: they need to solve section B on layer 1 *first* because it's covering the port they need on layer 2 — even though section A would have been easier to solve
- On harder systems, the port exposure creates a spatial dependency chain across layers:
  - Solve layer 1 section B → exposes layer 2 port X → insert key at port X → solve layer 2 section A → exposes layer 3 port Y → ...
- **Planning** reveals which outer sections expose which inner ports

**What the player feels:** The layers are not independent. The order you peel the onion determines what's available on the next layer. Strategic thinking extends beyond individual circuits to the whole cylinder.

---

## UC6 — Energy Tether Restraint Escape

**Context:** Meghan is restrained with energy tether cuffs during combat. The Escape Artist flow routes to hacking for the electronic restraint.

**Player experience:**

- Meghan's hands are cuffed behind her back with energy tethers
- The Cylinder appears — small system, 1 layer, 3 sections, one watchdog timer (reset mode)
- The player works the puzzle while combat continues around her — allies are fighting, the situation is evolving
- A section is solved and flipped — but the watchdog timer starts counting
- The player must solve the remaining sections before the watchdog expires
- **If the watchdog triggers:** The tether resets — all sections reappear, mandatory reboot wait. Meghan is still cuffed. She has to wait, then try again.
- **If successful:** The tether deactivates, Meghan is free, she can rejoin the fight
- Throughout: the alert bar is filling; if it hits critical, the tether may tighten or shock (additional consequence)

**What the player understands:** Electronic restraint escape is the hacking minigame under time pressure. The watchdog creates urgency. Combat continues — every second spent on the hack is a second not spent fighting. This is the electronic parallel to lock picking for handcuffs.

---

## UC7 — High-Security System (Full Complexity)

**Context:** Military or corporate high-security system. 4+ layers, 5+ sections per layer, phased blocks, multiple watchdogs, cross-communication, police blocks.

**Player experience:**

- The Cylinder is large and complex — many sections visible on the outer layer
- Multiple watchdog timers across different sections with interleaved dependency chains
- Cross-communication between sections means signals cascade unpredictably
- Police blocks are positioned at signal intersections — routing errors trigger detection
- Key selection requires precise pin matching; similar keys with subtle differences
- The player must plan the full solve sequence across layers: which sections to solve, in which order, managing watchdogs, exposing the right ports for deeper layers
- **All eight skills** contribute meaningfully at this complexity level
- A player with high Hacking but low supporting skills is probing extensively, filling the alert bar, and struggling with watchdog management
- A player with broad INT/WIS investment has pre-revealed circuits, identified dependencies, planned the sequence, and executes with minimal probing

**What the player feels:** A genuine challenge. The Cylinder at full complexity is a multi-dimensional puzzle — logic, spatial reasoning, sequencing, and time management all at once. Successfully cracking a high-security system is a peak gameplay moment.

---

## UC8 — Pre-Mission Research Advantage

**Context:** Meghan has invested time in Research before the hack — studying the target system's architecture, manufacturer, security model.

**Player experience:**

- Before the Cylinder appears, the Research results are applied
- Sections of the cylinder start **pre-revealed** — circuit layouts visible without probing
- Key types for some notch patterns are pre-identified
- Watchdog locations and timer durations are known
- The player enters the hack with a significant information advantage — fewer probes needed, lower alert accumulation
- This is the payoff for time invested in the research/planning phase of the mission

**What the player understands:** Preparation matters. Hacking is not just the minigame — it starts with mission planning. A player who rushes in blind faces a harder, noisier hack than one who did their homework.

---

## UC9 — Recognising a Familiar System (Fixed Puzzles)

**Context:** Meghan encounters a system she has hacked before — same manufacturer, same version number.

**Player experience:**

- The system label is visible: "Straton SecureGate v3.2"
- The player recognises it — they've solved this exact puzzle before
- **Knowledge Recall** pre-reveals the circuit layout (the game confirming what the player already knows)
- The player inserts the same keys in the same positions — the Cylinder solves quickly and quietly
- Minimal probing needed, minimal alert accumulation
- What took 2 minutes the first time takes 20 seconds the second time

**What the player understands:** Knowledge accumulates. Learning a system's puzzle once pays off every subsequent encounter. Hacking skill isn't just a number — it's represented by the player's actual memory of solutions. This is the core of the fixed puzzle system.

---

## UC10 — Corporate Signature Recognition

**Context:** Meghan encounters a new version from a manufacturer she's familiar with.

**Player experience:**

- The label reads "Straton SecureGate v4.0" — a version she hasn't seen, but a manufacturer she knows
- The puzzle is new, but the **corporate signature style** is familiar — Straton systems always use heavy cross-communication, indirect solutions
- The player knows not to try the obvious key placement — Straton's style means the real solution is in a different section
- Even though the specific puzzle is new, the player's familiarity with Straton's design philosophy gives them an approach
- The version is harder than v3.2 (more sections, tighter watchdogs) but the style is the same

**Corporate puzzle signatures:**

| Manufacturer | Style |
|-------------|-------|
| **Straton Technologies** | Cross-communication heavy — indirect solutions, keys unlock other sections |
| **QNS** | Dense, militaristic — heavy watchdog coverage, police blocks everywhere |
| **Machina Dynamics** | Elegant, layered — clean logic but many layers deep |
| **Mautorium** | Linear, direct — sequential, minimal cross-talk; easiest to learn |
| **HLM** | Decoy-heavy — fake release ports, police blocks disguised as targets |
| **Military** | Full complexity — all features, all difficulty |

**What the player feels:** Each corporation has a personality in their security. The player develops opinions — dreading QNS systems, finding Mautorium trivial, respecting Straton's cleverness. The manufacturer label becomes meaningful information before the hack even begins.

---

## Open Items

- Voxel grid dimensions for the cylinder ends — how many key positions are available?
- Key inventory management — how many keys does Meghan carry? Consumable or reusable? Acquired through Engineering, purchase, or loot?
- Whether inserted keys can be removed (at alert cost) or are permanent for the attempt
- Visual design of logic gates — readable representation of AND/OR/NOT/XOR for quick comprehension
- Audio design — signal routing, watchdog countdowns, alert level ambient changes
- Whether partial hacking progress persists (hack some layers, withdraw, return later) or if disconnecting resets
- Multi-user hacking — can an ally manage watchdogs or insert keys at the other end while Meghan works the circuits?
- Whether the hacking minigame tutorial is a dedicated training scenario (hacking simulator mentioned in hacking.md) or learned through early easy encounters
- Accessibility — what happens for players who struggle with the logic puzzle? Does high Hacking skill eventually trivialise simple systems (auto-solve)?
- Full corporate signature catalogue — which of the Big 20 produce security systems beyond the six listed?
- How many unique version/product ID puzzles are needed for the full game (content budget)
- Whether firmware updates happen dynamically in response to player actions or at fixed story points
- Whether Meghan can document solved puzzles in-game (notes/journal) to assist recall
