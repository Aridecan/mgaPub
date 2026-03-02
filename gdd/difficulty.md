# Difficulty Framework

## Design Philosophy

Difficulty tuning is **combat-only**. The life-sim half of the game — school, economy, time pressure, jobs, CP daily drain, social consequences — runs at a fixed intensity regardless of setting. Players who find combat frustrating can soften it without trivialising the civilian systems that give the story its weight.

This follows [Manifesto Principle 7 — Structure Friction Control](manifesto.md): assists are opt-in, shame-free, and reduce friction without removing the underlying systems.

---

## Presets and Custom Mode

Three presets — **Easy**, **Normal**, **Hard** — bundle eleven individual combat parameters into a consistent profile. A fourth option, **Custom**, opens a panel where the player can fine-tune each parameter independently.

Custom starts from whatever preset was last active. Selecting a preset at any time resets all parameters to that bundle. Changing any individual parameter while on a preset automatically switches the dropdown to Custom.

Difficulty can be changed at any time from the settings menu, including mid-game. Changes take effect on the next combat encounter (not retroactively mid-fight).

---

## Tunable Parameters

| Parameter | What it controls | Easy | Normal | Hard |
|-----------|-----------------|------|--------|------|
| **Enemy Aggression** | How often enemies press attacks vs wait/defend | Low | Medium | High |
| **Enemy Read Speed** | How fast AI identifies player attack animations (ms) | Slow | Medium | Fast |
| **AI Adaptiveness** | Whether AI learns from failed attacks and varies sequences | Scripted | Mixed | Adaptive |
| **Resource Exploitation** | Whether AI monitors PC pools and exploits low CP/SP/MP | Off | Partial | Full |
| **Pursuit Tenacity** | How long and aggressively AI chases a fleeing PC | Short | Standard | Relentless |
| **Reaction Windows** | Player timing windows for parry/dodge/counter | Wide | Standard | Narrow |
| **Incoming Damage** | Multiplier on damage dealt to the player | 0.7× | 1.0× | 1.3× |
| **Outgoing Damage** | Multiplier on damage dealt by the player | 1.3× | 1.0× | 0.85× |
| **Group Coordination** | How effectively enemy groups share tags/info, flank, combo | Low | Medium | High |
| **Recovery Rate** | Player CP/SP/MP recovery speed during combat | Fast | Standard | Slow |
| **Cancellation Cost** | SP cost for cancelling an action mid-animation | Reduced | Standard | Full |

Exact numerical values for each level are TBD — the table defines direction and relative intensity, not final calibration.

---

## Parameter Details

### Enemy Aggression

Controls the weighting on offensive vs. defensive/waiting behaviors in the AI's utility curves. At Low, enemies pause longer between actions and are more likely to hold at range. At High, they press constantly, chain attacks, and exploit openings faster.

This is a multiplier on the per-NPC base aggression from their STALKER profile — a street thug on Hard is still less aggressive than a corporate security operator on Easy.

### Enemy Read Speed

The time (in ms) the AI takes to identify a player attack from its animation startup. Faster read speed means the AI decides to counter, continue, or cancel earlier. Slower read speed gives the player more time before the AI reacts.

Maps directly to the animation read time system in the [enemy AI notes](../../mgaPriv/mechanics/enemy-ai-notes.md). The difficulty setting applies a multiplier to the per-NPC base read time set by their skill level.

### AI Adaptiveness

Controls whether enemy behavior trees use fixed attack sequences or adapt to the player mid-fight.

At **Scripted**, enemies follow predictable move chains — a mid punch is always followed by a high punch, followed by a low kick. The player can learn the patterns and counter reliably. The behavior tree selects from a fixed set of sequences appropriate to the NPC's STALKER profile and does not deviate based on outcomes.

At **Mixed**, enemies have some sequence variation. If a sequence fails repeatedly (blocked, dodged, countered), the AI may switch to a different chain, but it does not actively analyze *why* the sequence failed. It rotates through its available patterns rather than optimizing.

At **Adaptive**, the AI tracks which attacks have been blocked, dodged, or countered during the encounter and deprioritizes them. It actively varies its sequences to avoid repeating what isn't working. A high-skill NPC on Adaptive will read the player's defensive habits and shift to attacks that exploit gaps — if the player favors parry over dodge, the AI shifts toward grabs and unblockable moves; if the player blocks high consistently, the AI targets low.

This is the parameter that most directly modifies the behavior tree structure. Scripted uses simpler, more linear trees. Adaptive uses the full utility-weighted selection with outcome tracking.

### Resource Exploitation

Controls whether and how the AI monitors the player character's CP, SP, and MP during combat and adjusts tactics in response.

At **Off**, the AI does not factor PC resource state into its decisions. It selects actions based on positioning, aggression, and its own state only.

At **Partial**, the AI has awareness of visibly depleted resources (the same information available through the tag/widget system — it does not cheat). When the PC is clearly low on SP, enemies may push harder knowing cancels and dodges are limited. They won't perform deep tactical analysis, but they respond to obvious vulnerability.

At **Full**, the AI actively exploits resource state. If it determines the PC lacks the SP to dodge, it pushes power attacks that bleed through blocks. If SP and CP are both low, it may attempt grabs or clothing-targeted attacks (content tier permitting) that are harder to mitigate without resources. High-skill NPCs on Full difficulty will time their pressure to moments when the player is resource-starved — backing off when the player is fresh and pressing when they're spent.

Resource exploitation respects signal parity — the AI uses the same perception and information systems available to the player. It does not access hidden data. The difficulty setting controls how *intelligently* the AI uses the information it can legitimately see.

### Pursuit Tenacity

Controls how the AI responds when the player character attempts to disengage or flee from combat.

At **Short**, enemies pursue briefly and give up quickly. The player can break contact by creating moderate distance. Enemies do not call for backup when the player flees.

At **Standard**, enemies pursue for a reasonable distance based on their STALKER profile. Aggressive NPCs chase longer than cautious ones. Some enemies may alert nearby allies if the player runs through their territory, but there is no coordinated cutoff behavior.

At **Relentless**, enemies pursue aggressively and for extended distances. High-skill enemies or group leaders may call for backup to cut off escape routes. Groups may split — some pursuing directly while others move to intercept. Fleeing through enemy territory risks triggering additional encounters as pursuers alert nearby hostiles. Escape requires genuinely breaking line of sight and outpacing pursuit, or finding a defensible position.

Pursuit tenacity interacts with Group Coordination — at Low coordination, even Relentless pursuit is individual rather than tactically coordinated. At High coordination + Relentless pursuit, organized enemy groups become genuinely dangerous to run from.

### Reaction Windows

The timing windows available to the player for parry, dodge, and counter inputs. Wide windows are more forgiving; Narrow windows demand precision. This affects the player's side of the timing equation — it does not change enemy attack speeds.

### Incoming Damage

A flat multiplier applied to all damage dealt to the player. 0.7× on Easy means the player takes 30% less damage; 1.3× on Hard means 30% more. Applied after all other damage calculations (armor, resistance, clothing absorption).

### Outgoing Damage

A flat multiplier applied to all damage dealt by the player. 1.3× on Easy means the player deals 30% more damage; 0.85× on Hard means 15% less. Applied after all other damage calculations.

### Group Coordination

Controls how effectively enemy groups operate as a unit. At Low, enemies act mostly independently — limited tag sharing, infrequent flanking, no combo setups. At High, groups share perception tags efficiently, flank aggressively, and set up coordinated attacks.

### Recovery Rate

Affects how quickly CP, SP, and MP regenerate during combat. At Fast, recovery is accelerated — the player can sustain longer and recover from mistakes more quickly. At Slow, resource management is tighter and every expenditure carries more weight.

### Cancellation Cost

The SP cost for cancelling a committed action mid-animation. At Reduced, cancels are cheap — the player can experiment and recover from misreads with minimal penalty. At Full, cancels cost their standard SP amount and committing to an action carries real risk.

---

## What Difficulty Does Not Affect

The following systems are fixed regardless of difficulty setting:

- **School calendar and exams** — deadlines, study requirements, exam difficulty
- **Economy** — rent, food, tuition, clothing costs, debt, job pay rates
- **CP natural drain** — daily life CP drain rate outside combat
- **Jobs** — job difficulty, task requirements, reputation gains/losses
- **Time system** — day length, scheduling pressure, event timing
- **Failure pipeline** — consequences for failure remain the same
- **Player character stats** — attributes, skills, and skill progression are unaffected
- **Uniform piece bonuses** — skill bonuses from [magical girl uniform pieces](../../mgaPriv/mechanics/magical-girl-uniform-pieces.md) provide the same effective level regardless of setting

---

## Interaction with Content Tiers

Content tiers (Base / Spicy / Super Spicy) and difficulty are **independent axes**.

Content tiers gate which actions exist in the AI's action pool — clothing-targeted attacks in Spicy, intimate combat techniques in Super Spicy. Difficulty controls how aggressively and effectively the AI uses whatever actions are available to it.

- **Easy + Super Spicy**: full action pool, less aggressive enemies, slower reads, wider player windows
- **Hard + Base**: standard action pool, aggressive enemies, fast reads, narrow player windows

The two settings never interact mechanically. A player adjusting one has no effect on the other.

---

## Interaction with Enemy AI

The difficulty parameters map to tunable axes in the enemy AI system:

- **Enemy Aggression** → personality weighting on utility curves (base from STALKER profile, modified by difficulty offset)
- **Enemy Read Speed** → animation read time per NPC (base value from NPC skill level, modified by difficulty multiplier)
- **AI Adaptiveness** → behavior tree complexity; Scripted uses fixed sequence nodes, Adaptive enables utility-weighted selection with outcome memory
- **Resource Exploitation** → enables/disables the utility score bonuses for targeting resource-depleted players; respects signal parity (AI reads the same widgets the player sees)
- **Pursuit Tenacity** → chase duration, pursuit break-off distance, backup call probability
- **Group Coordination** → tag sharing frequency, flanking probability, combo setup triggers

These are multipliers and offsets on per-NPC base values, not flat overrides. Enemy identity is preserved — difficulty makes enemies relatively better or worse, not uniformly identical. A street thug on Adaptive + Full exploitation is still working with a street thug's base skill level and STALKER profile — they're smarter within their capabilities, not transformed into a different NPC.

---

## No Shame Design

Following Manifesto Principle 7:

- Presets are labelled **Easy / Normal / Hard** — straightforward names, no euphemisms, no judgmental language
- No achievements, content, story branches, or endings are gated by difficulty
- Difficulty can be changed at any time, including mid-game
- No confirmation dialog when selecting Easy
- No in-game visual indicators that broadcast the current difficulty setting to the player or during gameplay
- The tutorial and encyclopedia systems explain mechanics as they work on Normal — they do not reference difficulty settings or suggest the player should change them

---

## Custom Mode UI

Selecting Custom in the Difficulty dropdown expands an additional panel below the dropdown, following the same expand/collapse pattern used by Display → Advanced in the [settings menu](../../mgaPub/creative-briefs/CB-settings-menu.md).

Each parameter is presented as a labelled slider or dropdown with its current value visible. The panel shows which preset the current configuration most closely resembles (if any) for reference.

Returning to a preset collapses the panel and resets all values.
