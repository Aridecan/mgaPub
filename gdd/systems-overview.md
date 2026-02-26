# MGA — Systems Overview

*One paragraph per system. Links to detail documents where they exist. Use this as the index connecting the game's parts to each other.*

---

## Foundation

### Attribute System

Meghan has ten core attributes — STR, FOR, POW, DEF, INT, WIS, ACC, AGI, CHA, and STA — spanning physical, magical, mental, and social domains. Attributes define the mathematical foundation for every skill check and resource pool. They advance through dedicated training, equipment, and story events. No attribute is irrelevant: the social attributes (CHA, WIS) govern negotiation and resistance in the same resolution system that governs combat.

*Detail: forthcoming.*

### Skill System

Skills are learned capabilities that build on base attributes. Each skill has a primary attribute (full weight) and in most cases a secondary attribute (half weight). Skills scale from 1 to 100 and progress through successful execution — the same Parkour skill levelled during a courier run contributes to its combat and chase applications. There is no separate life-sim progression and combat progression. One character, one system.

*Detail: forthcoming.*

### Progression Philosophy — No Levels, No Feats

MGA does not use a character level or a feat system. This is a design assumption, not a technical constraint — nothing prevents levels or feats from being added later, but the current design proceeds on the basis that they are not needed.

The reasoning: skill rank progression, action unlocks at rank thresholds, and licence gates together provide all the mechanical function that a separate level/feat system would provide. Skill ranks give granular progression. New actions unlocking at rank thresholds give the milestone satisfaction that levelling up is supposed to deliver. Licence caps create deliberate ceilings that must be purchased through, functioning the same way feat gates do in other systems. The breadth of 50+ skills means build identity emerges from investment choices rather than from a separate layer of character options.

The other function levels commonly serve — increasing resource pool sizes (HP, CP, SP, Mana) — is also already covered. Each pool has its own dedicated training path; pool capacity grows through use and investment rather than through a level-up event. Adding levels would introduce a redundant abstraction — a single derived number that summarises what skill ranks and attribute values already communicate, and one that would sit awkwardly on top of a STALKER profile designed around certified capabilities rather than character tiers.

The closest equivalent to a character level in the fiction is the **Overall Classification** field in the STALKER profile header — a derived label reflecting Meghan's current skill bracket. It is a label, not a tracked number, and it carries no mechanical weight beyond describing where she sits in STALKER's assessment.

### No-Death Design Rule

**Nobody dies on Terridyn.** This is a foundational design constraint, not a suggestion:

1. **Content creation friendly:** No blood, no death animations. YouTube and Twitch influencers can cover MGA without colour filters or censorship workarounds
2. **NPC progression tracking:** STALKER tracks skills and growth on enemy NPCs. If enemies die, that progression data is lost — the world loses texture every time a fight ends. Keeping everyone alive means the city's population is a persistent, evolving system
3. **Defeat is content, not failure:** The game continues even when Meghan loses. Defeat routes into the failure pipeline, not a game-over screen. This keeps the player engaged and creates opportunities for recovery gameplay
4. **Demographic protection:** Players cannot go on killing sprees and wreck the city's population balance, economy, or gender ratio — all of which are load-bearing narrative elements

**In-fiction justification:** Personal shield nanites powered by Terridyn's energy crystals absorb damage as feedback that causes loss of consciousness rather than lethal injury. The Consciousness Pool (CP) replaces a traditional health bar for organic characters. At zero CP, the target is unconscious — not dead.

**Planet-centric limitation:** The nanites only function on Terridyn because they are powered by the planet's energy crystals. Off-world, death is common — corporate wars, inter-state conflicts, and pirate actions all produce real casualties. This is one of the reasons the gender ratio is so heavily skewed female: males are disproportionately drafted and killed in off-world conflicts. The no-death rule is Terridyn-specific.

### Resource Pools

Four active resource pools govern Meghan's capability moment to moment:

| Pool | Governs | At zero |
|------|---------|---------|
| **HP** (Health Points) | Physical injury tolerance | Downed |
| **CP** (Consciousness Pool) | Cognitive and emotional endurance | Forced 6-hour downtime |
| **SP** (Stamina Pool) | Physical exertion | Reduced physical effectiveness |
| **Mana** | Magical capability | Magical abilities unavailable |

Enemies and machines have equivalent pools; mechanical opponents use a Fuel resource in place of Mana. Each pool has distinct recovery conditions — HP through rest and healing, CP through sleep, SP through rest between exertion, Mana through recovery mechanics. Depletion effects are specific to the pool; losing CP does not reduce physical strength, but it does reduce cognitive and emotional performance.

*Detail: forthcoming.*

---

## Moment to Moment

### Time System

MGA runs on a real-time 24-hour clock. Actions advance the clock proportionally: studying for one hour passes one hour; a delivery route takes however long it takes. There are no discrete action slots. Efficient play stretches days further; careless play burns time. The clock creates natural pressure without hard-gating content.

*Detail: [Core Loop — The 24-Hour Clock](core-loop.md#the-24-hour-clock)*

### Combat

Combat is skill-driven and action-oriented. The attributes and skills developed in daily life apply directly in fights; a player who has invested in Parkour, Negotiation, and Situational Awareness arrives at combat with different tools than one focused on Melee Combat and Ranged Weaponry — but no investment is wasted. Encounters involve HP, SP, and Mana management across melee, ranged, and magical capability. Failure in combat is contextual: a downed Meghan is not necessarily a game over.

*Detail: forthcoming.*

### Traversal

Moving through Terridyn is active and skill-dependent. Parkour covers urban acrobatics — vaults, climbs, wall-runs, chases, and crowd navigation while carrying items. Driving covers vehicle operation from bicycle through hoverbike. Both skills transfer directly to job performance and combat mobility; a player who has couriered extensively knows the city's geography before they need it for anything more dangerous.

*Detail: forthcoming.*

---

## Mid Loop

### Economy

Meghan has ongoing fixed expenses — rent, food, tuition, textbooks, clothing replacement, and debt repayments — that cannot be ignored. Income comes from the five available jobs. No expense is optional; the question is how to manage them all simultaneously without letting any one obligation collapse.

*Detail: [Core Loop — The Economy](core-loop.md#the-economy)*

### Jobs

Five income sources are available before stable employment arrives in Chapter 3: Waitress at Burton's Bar, Courier/Delivery, Mechanic, Hacker, and Bounty Hunter. Each trains a distinct skill cluster and maintains an independent reputation track. Higher reputation unlocks better-paying contracts, equipment, NPC connections, and story events. The hacker, mechanic, and bounty hunter jobs involve clients whose interests conflict — faction reputation is a portfolio, not a single track.

*Detail: [Core Loop — The Five Jobs](core-loop.md#the-five-jobs)*

### School Calendar

Meghan has four months from arrival to pass the MCI entrance examination. Failing to enrol is a game over; failing to maintain academic performance after enrolment is also a game over. Studying costs time (advancing the clock by study duration) and trains INT/WIS skills. The Studying skill reduces time cost at higher levels. The school calendar is the structural pressure that makes time management essential across the early game.

*Detail: [Core Loop — The School Calendar](core-loop.md#the-school-calendar--primary-structural-pressure)*

### Failure Pipeline

Failing a bounty hunting or hacking subjob does not trigger a game over. Meghan enters the city's black market holding pipeline and must escape using the tools available at her current capability level. Early game: lockpicking and stealth only. Post-Ava restoration: magical abilities and potential team rescue. Late game: escalating security, higher stakes. The pipeline is failure content, not a dead end.

*Detail: [Core Loop — The Failure Pipeline](core-loop.md#the-failure-pipeline)*

---

## Character Development

### Relationship System

Meghan's relationships with NPCs develop through repeated interaction, shared experience, and player choices. Relationship state affects dialogue options, available content, NPC assistance in missions, and the conditions under which other systems activate. Relationships are not a simple approval meter — they have texture that is only visible with sustained investment.

*Detail: forthcoming.*

### Inhibition

Inhibition is a hidden character stat tracking how open Meghan is with herself — her willingness to act outside the careful, contained identity she built before arriving on Terridyn. It is not displayed; it manifests through what becomes available. A high-inhibition Meghan deflects opportunities outside her comfort zone; a low-inhibition Meghan engages from a place of genuine openness. Inhibition decreases through relationship progression and story milestones.

*Detail: forthcoming (Spicy/Super Spicy design documentation).*

---

## Magical Girl Systems

### Elemental Magic

Each magical girl has a distinct elemental affiliation governing her spells' type, behaviour, and interactions. Elemental oppositions and synergies affect combat — some elements suppress others, some amplify combinations. The system extends to the environment: the ambient mana of Terridyn has its own properties, and Ava must re-align to it before she can function at full magical capacity.

*Detail: forthcoming.*

### Magical Girl Transformation

Transformation from Meghan to Ava is not a mode switch — it is a state change within the same character and skill system. The skills built as Meghan are the skills available as Ava. Transformation does not unlock a separate power set; it amplifies and extends the existing one. The Razor holds through transformation.

*Detail: forthcoming.*

### Wand System

Ava's wand is the primary instrument of her magical capability. It was broken before the game begins; its reconstruction is a core arc of Chapter 2. Beyond the wand, each magical girl carries a distinctive personal weapon reflecting her elemental affiliation. Wand and weapon systems interact with mana management, skill use, and combat flow.

*Detail: forthcoming.*

---

## World and Social Systems

### Faction Reputation

The hacker, mechanic, and bounty hunter jobs each involve clients whose interests conflict. Working for activist groups affects corporate relationships; working for one corporation may alienate rivals; working for gang leaders affects law enforcement standing. Faction reputation is a portfolio the player actively manages, not a single approval track.

*Detail: [Core Loop — Job Reputation](core-loop.md#job-reputation)*

### Clothing and Equipment

The clothing destruction system (Spicy DLC) ties wardrobe management directly to the economy. Combat damages and destroys garments; replacement is an ongoing cost. The system interacts with the skills Rending (deliberate garment destruction during grapples) and Wardrobe Warding (magical reinforcement against tearing). In the base game, clothing functions as a standard equipment system without the destruction mechanics.

*Detail: [Content Architecture & DLC](content-and-dlc.md)*

---

## Organisational Systems

### CAMERA System

CAMERA (Cognitive Augmented Monitoring, Engagement & Reconnaissance Assistant) provides the in-fiction justification for the third-person camera. Meghan's personal CAMERA drone is Calibran technology she brought with her — it provides the third-person view from the moment she steps off the ship in the prologue. In Chapter 3, Meghan and Imara Kesari upgrade the existing drone using Xtalics technology and build new drones for the other magical girls. Once allies have their own calibrated drones, full party switching and team management become available.

*Detail: [CB-camera](../creative-briefs/CB-camera.md)*

### Content Tiers

MGA is structured in three content tiers — Base, Spicy, and Super Spicy — delivered as modular DLC. The base game contains the full story and all core systems; higher tiers add mature content without altering the story. Certain gameplay options and job opportunities are gated by content tier and by character state.

*Detail: [Content Architecture & DLC](content-and-dlc.md)*
