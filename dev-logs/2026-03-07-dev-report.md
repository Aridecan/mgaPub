# MGA Dev Report — March 7, 2026

*Ordinary Girl Meghan / Magical Girl Ava*

---

## Finishing What We Started

Last week's report was about the explosion — going from scattered notes to a structured design bible in eight days. This week is about the follow-through. If the February blitz answered "can we actually document this game?", this week answered "can we finish?"

Yes. We can. The creative brief library is effectively complete: 30 briefs, roughly 130 use cases, covering every major player-facing system in the game. The one exception is Enemy AI, which is deliberately blocked on technology research — I need hands-on time in UE5 before I can write that brief honestly. Everything else is on paper.

This week was entirely documentation. No art, no engine work, no code. But the design documentation phase of pre-production is done. The project is ready to shift from "what are we building" to "how do we build it."

---

## Creative Briefs: 12 → 30

The headline: 18 new creative briefs in one week. The Feb report shipped 12 briefs covering combat, clothing, inventory, camera, jobs, and the menu/HUD cluster. This week filled in everything that was missing.

### Core Gameplay

- **Combat Feedback** (8 use cases) — how hits feel. Hitstop, camera response, hit reactions, clothing destruction feedback, KO sequences.
- **Action Bar** (6 use cases) — the always-live skill bar. Input-method-dependent display, grapple mode automatic bar swap, form-specific bars for civilian Meghan vs magical girl Ava.
- **Knockout & Recovery** — the Brainwave Sync revival minigame. Negative CP depth scaling, five brain waves mapped to the action bar, plate-spinning at deep knockouts.
- **Traversal** — movement, parkour, vehicles. How Meghan gets around the city and how skill progression opens new routes.
- **Weather & Environment** (8 use cases) — almanac-driven day-night with dual moons, volumetric weather, temperature effects, seasonal atmosphere.
- **Exploration** (6 use cases) — city navigation, active search, bounty investigation, restricted area infiltration, map as earned knowledge.

### Progression

- **Studying** (6 use cases) — two study modes (time-skip or player-driven textbooks), three exam tiers, skill-aided answers, education as a DLC/mod content platform.
- **Training** — 8 training hubs across the city, lesson-then-drill structure, cooperative training with party members, 4-tier dev priority for skill implementation.
- **Meditation** (6 use cases) — CP recovery from marginal to genuine sleep alternative, shrine amplification, curse-breaking (80 hours across 4 levels), in-combat micro-meditation at high skill.

### Social & Narrative

- **Social** (7 use cases) — no visible relationship meters, breadth-of-interaction over grinding, seduction overflow, inhibition as hidden content gating.
- **Dialogue** (8 use cases) — NPC-initiated contact, phone communication, conversation as a skill-integrated system.
- **Failure Pipeline** (6 use cases) — capture routes into gameplay, not game over. Black market holding, three escape phases, consequences that matter without being punishing.
- **Notes & Discovery** (9 use cases) — the game's answer to quest systems. Or rather, the deliberate absence of one.
- **Cutscenes** (6 use cases) — authored cinematics, player agency preservation, seamless transitions between gameplay and narrative.

### Systems

- **Tutorials** — 11 tutorial beats integrated into the prologue. Chapter 1 opens as pure open world with no hand-holding.
- **Lock Picking** (7 use cases) — concentric ring minigame with ball physics. Soft skill gate — any lock attemptable at any level.
- **Hacking** (2 new use cases added to existing brief) — the Cylinder minigame. Research depth spectrum, script kiddy option, firmware uncertainty.
- **Repair** (7 use cases) — signal routing puzzle. Diagnosis phase plus grid routing, material efficiency as profit margin.

### Updated Briefs

- **Mod Manager** — added 3 views and dependency cascade logic.
- **Main Menu** — fleshed out the 3-menu architecture (title, character select, in-game).
- **In-Game Menus** — added System button and Memories section.

### Design Spotlights

A few of the designs I'm particularly happy with:

**Notes & Discovery** is probably the most important brief I wrote this week. MGA does not have a quest system for the main story. No waypoints, no objective checklists, no "3/7 clues found." The player discovers the story by reading notes that Meghan writes on her phone — her observations, leads, conversations recalled. The game recognises their actions and advances the story. Jobs get proper tracking (bounty contracts, delivery deadlines), but the main story, world discovery, and character relationships all live in freeform notes. This is the Elden Ring approach: the game tells you what happened and what someone said. It never tells you what to do next. I also built in a mission ranking system (F through SSS) for jobs and a chain mission concept where completing one contract organically leads to the next.

**Combat Feedback** codified something I've felt strongly about for a while: no floating damage numbers. MGA communicates damage through visual consequence — hitstop on impact, directional camera nudge, clothing tearing away in real time, the target staggering in the direction of the hit. The CP bar gives you the quantitative read; everything else is qualitative. There's a four-tier hit reaction system (flinch, stagger, knockback, knockdown) where severity scales with damage relative to the target's Fortitude, and every knockout has an authored sequence instead of a generic ragdoll. The no-death design rule shapes the whole feedback language — no blood, no gore, characters go unconscious, not dead — which means leaning harder on motion, sound, light, and material response.

**Weather & Environment** connects the almanac tool I built in February to actual gameplay. The day-night cycle is driven by almanac sun position data. Terridyn has two moons — Naidra (27.6-day cycle) and Sirdal (60-day cycle) — and when both are full, the night is nearly as bright as dusk with two distinct shadow directions. Weather affects gameplay through signal parity: rain cuts Perception by 25% for everyone, player and AI alike. Fog cuts it by 50%. Snow leaves footprints that the Search skill can track. The weather system doesn't cheat.

**Action Bar** solved a display problem I'd been thinking about. The bar looks different depending on your input method — keyboard players see a straight row of 10 slots, gamepad players see 4 groups mirroring the face button + modifier layout. It switches automatically based on last detected input. The grapple mode is another piece I like: when you initiate a grapple, the bar automatically swaps to grapple-specific actions. No manual switching, no mode toggling. And since civilian Meghan and magical girl Ava have fundamentally different available actions, each form gets its own bar and profile set.

---

## Minigame Systems

Four interactive systems were fully designed from scratch this week:

1. **Brainwave Sync Revival** — the KO recovery minigame. When CP hits zero, damage continues as negative CP (knockout depth). The player manages five brain waves mapped to the action bar, tracking procedurally generated waveforms by riding a needle up and down. Shallow knockouts only require one or two waves; deep knockouts activate all five and it becomes genuine plate-spinning. Meditation skill provides look-ahead preview at low levels and auto-sync at mastery. Players can also skip the minigame entirely — natural recovery advances game time instead.

2. **Concentric Ring Lock Picking** — a ball bounces inside concentric rings (one per tumbler). The player rotates rings to guide the ball to targets. Two strategies: static (plan the geometry, execute once) or dynamic (react to the ball in real-time). Any lock is attemptable at any skill level — higher skill widens targets, adds bounce preview, and pre-solves inner rings. Advanced locks introduce block phasing: blocks appear and disappear as rings are solved, changing the geometry at each transition.

3. **The Cylinder (Hacking)** — a spinning drum with concentric layers divided into logic circuit sections. The player inserts hardware interface keys at the cylinder ends to send signals through circuits, solving sections to peel layers away while managing watchdog timers and alert levels. Eight skills feed into performance. There's a research depth spectrum — you can go in blind or spend time on pre-mission intel to reveal circuits before you start. The script kiddy option lets low-skill players brute-force simple systems at the cost of noise and time.

4. **Signal Routing (Repair)** — two phases: diagnosis (find what's broken via testing and skill shortcuts) then signal routing (lay wire routes across a grid board). Routes can't overlap, damaged zones are impassable, and you have limited materials. Six skills contribute to diagnosis. The economy hook is material efficiency — efficient routing means more profit per job. Field repair uses the same puzzle under mission pressure with limited materials and salvage options.

Seven minigame systems remain undesigned (grapple struggle, escaping grapple, binding application, restraint weakening, cooking, crystal purification, and trap disarming).

---

## World & Lore Expansion

### Mana Aspects: 8 → 15

The mana system expanded from 8 aspects to 15: the 7 deadly sins, the 7 heavenly virtues, and Pure (all 14 in balance). The core insight was the fuel/engine model — sin or virtue mana is the fuel source, the magical girl plus her wand is the elemental engine, and elemental output (ice, fire, lightning, etc.) is the result. The elemental type is fixed to the girl; the mana aspect can shift. This opened up conversion arcs as a narrative tool: Meghan shifts from Patience to Lust when her rebuilt wand absorbs Lust-aspected mana in Chapter 2. Other party members undergo their own conversions through the story.

The colour logic fell into place too: purple (all 7 sins combined) plus its RGB complement (all 7 virtues combined) produces white (Pure). Additive colour mixing as magical cosmology.

### Prologue Rewrite

The prologue expanded from 78 to 238 lines — a complete rewrite. 11 tutorial beats now live entirely within the prologue, teaching every major system through story context. By the time the prologue ends, the player has a phone, a place to sleep, a reason to go to school, two people they've met organically, and first-hand experience with every control they'll need. Chapter 1 opens clean — pure open world, no tutorials interrupting.

### Thematic Design

I spent a session mapping MGA's thematic architecture: ordoliberalism by absence (Terridyn has no regulatory framework and the consequences are everywhere), DC vs Marvel character arcs (Meghan is a Marvel-style character — she starts as a person and becomes a hero, rather than starting as a symbol), and discovery through lived experience rather than exposition. This isn't a design document per se, but it sharpened how I think about every system.

### Enemy AI

Working notes written: three states (Normal, Engaged, Fleeing), signal parity (AI uses the same perception and information systems as the player — no god-mode data), and animation reading as a design principle. The full creative brief is blocked on technology research. I need to prototype in UE5 before I can commit to a behaviour architecture (utility AI, GOAP, behaviour trees, or a hybrid). The notes and research exist; the brief waits for engine time.

### Weather Lore

Moon cycle correction (Naidra's orbital period was wrong in one file), ROH and Heavy Worlder winter fashion details, and a street demographics system where the mix of human subspecies on the streets shifts by temperature and season.

---

## Quality & Maintenance

- **Spoiler audit.** Swept the public repo for unprotected story spoilers. Wrapped references in `<details>` tags across 12 files so SubscribeStar supporters who browse the GitHub can't accidentally trip over major plot points.
- **README updates.** Both repos got refreshed README files reflecting the current state of documentation.
- **ChatGPT dump review.** Completed — all 36+ conversation exports reviewed and everything worth capturing has been integrated into the proper documents. That backlog is fully cleared.
- **Data corrections.** Personal shield confirmed as CP (not a separate pool), CAMERA drone origin corrected (Calibran tech, not invented in Chapter 4), Pure Ice renamed to Patience Ice to match the expanded mana system, Arri's mana alignment corrected, Naidra's orbital period fixed.

---

## By the Numbers

- **30** creative briefs (was 12)
- **~130** use cases documented
- **4** minigame systems designed (7 remaining)
- **15** mana aspects (was 8)
- **14** sessions (3 late Feb + 11 March)
- **1** remaining audit gap (Enemy AI — blocked on tech research)

---

## What's Next

The documentation phase of pre-production is done. The next phase is about proving the designs work.

- **Technology experimentation in UE5.** Prototype core systems, validate design assumptions, find out what breaks when theory meets the engine.
- **Feature briefs.** The next detail layer after creative briefs — player-facing design specifications that bridge "what the player experiences" to "what we need to build."
- **Enemy AI creative brief.** After hands-on time with UE5's AI systems — behaviour trees, navigation, perception. The notes and research are ready; the brief needs engine truth.
- **Animation architecture.** Dependent on engine experimentation. I need to understand UE5's animation pipeline before I can design the hit reaction, combo, and transition systems properly.

The shift from "what are we building" to "how do we build it" starts now.

---

*MGA is a solo-developed adult action RPG currently in pre-production. Follow along for more updates.*
