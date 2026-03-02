# Creative Brief — Meditation

---

## Overview

Meditation is Meghan's primary recovery tool between encounters. Sleep is the only source of full CP and mana restoration, but sleep requires a bed and consumes the rest of the day. Meditation fills the gap — a deliberate pause that recovers CP, mana, and clears mental status effects at the cost of game time. A player who meditates regularly keeps Meghan operating near peak; a player who never meditates accumulates hidden stress and enters each encounter slightly degraded.

Meditation also serves a second, heavier purpose: breaking curses on the magical girl uniform. Curse removal requires sustained meditation measured in hours per curse level — a significant time investment that competes directly with everything else Meghan needs to do.

**Related documents:**

- [Meditation Skill](../../mgaPriv/mechanics/skills/meditation.md) — skill progression, status effect clearing, shrine amplification
- [Pool Training](../../mgaPriv/mechanics/pool-training.md) — mana recovery rates, sleep vs meditation
- [Magical Girl Uniform](../../mgaPriv/mechanics/magical-girl-uniform.md) — curse system, curse-breaking meditation, cascading interruption
- [Core Loop](../gdd/core-loop.md) — time system, daily rhythm
- [CB-training](CB-training.md) — meditation training at shrines and MCI (Priority 1 skill)

---

## Use Cases

### UC1 — Basic Meditation Session

**Actor:** The player
**Goal:** Recover CP, mana, and clear mental status effects between encounters
**Trigger:** Player chooses to meditate during free time

**Step by step:**

1. **Player initiates meditation.** Meghan sits down at her current location. Any location works, but quiet environments are more effective at low skill levels (levels 1–20 require an undisturbed environment; higher skill levels can meditate in noisier settings)
2. **Player chooses duration** — selectable in increments. Longer sessions recover more but cost more game time
3. **Time advances.** Meghan meditates. The world continues around her — she is stationary and vulnerable during the session
4. **Results applied:**
   - **Mana recovery:** Approximately 1 hour per 1/6th of the mana pool at baseline. Higher Meditation skill improves the rate. At mastery, near-complete mana recovery in a fraction of the time
   - **CP recovery:** Partial CP recovery scaling with skill level. At basic levels, the recovery is minor — enough to take the edge off but not a substitute for sleep. At advanced and mastery levels, meaningful CP restoration that can extend a day
   - **Status effect clearing:** Mental status effects accumulated from encounters (Shaken, Flustered, Provoked, Embarrassed) are cleared during the session. Lower-level effects clear first; higher skill levels clear more severe effects. Duration of clearing decreases as skill increases
   - **Meditation skill XP** gained from the session
5. **Session ends.** Meghan stands up, recovered. The clock has advanced

**What the player decides:** Is the recovery worth the time? A 30-minute meditation after a tough fight might be the difference between entering the next encounter healthy or compromised. A 2-hour session before an exam clears stress and improves cognitive performance. But that's 2 hours not spent studying, working, or socialising.

**CP recovery — resolving the open question:** Meditation provides meaningful mid-day CP recovery at higher skill levels. At basic levels (1–20), the CP recovery is marginal — enough to notice, not enough to rely on. At stable practice (21–50), it becomes a useful top-up. At deep practice (51–80), it can meaningfully extend Meghan's effective day. At mastery (81–100), meditation is a genuine alternative to sleep for CP recovery, though sleep remains faster and more complete. This progression gives the player a reason to invest in Meditation even if they never engage with the magical girl curse system.

---

### UC2 — Shrine Meditation

**Actor:** The player
**Goal:** Meditate at a shrine for amplified recovery and elemental attunement
**Trigger:** Player initiates meditation while at a Terridyn shrine

**Step by step:**

1. **Player reaches a shrine.** Shrines are fixed locations scattered across Terridyn — in parks, on hillsides, in quiet corners of the city, at elevated points with views
2. **Player initiates meditation at the shrine.** Same interface as basic meditation, but with visible shrine bonus indicators
3. **Shrine amplification applies:**
   - **Recovery rates are significantly boosted.** Mana and CP recover faster than baseline meditation at the same skill level. A shrine session is the most time-efficient meditation available
   - **Elemental shrines** are attuned to specific elements. Meditating at an ice shrine provides an ice mana bonus; a fire shrine provides fire mana bonus. This bonus affects mana quality, not just quantity — elementally attuned mana is more efficient for matching spells
   - **Meditation skill XP is amplified.** Shrine practice counts as higher-quality training than self-practice
4. **Session ends.** Enhanced recovery applied. Elemental attunement fades over time after leaving the shrine (duration scales with Meditation skill)

**Shrine discovery:** Shrines are not all marked on the map from the start. Some are visible and accessible early; others are hidden, require traversal skill to reach, or are gated behind story progression. Finding a new shrine is a minor exploration reward — the player gains a new recovery point with potential elemental bonuses.

**Shrine as rest point:** Shrines serve a tactical function in the game world. A player planning a difficult encounter can meditate at a nearby shrine beforehand to enter at full strength. Knowledge of shrine locations becomes part of the player's mental map — a form of environmental mastery that rewards exploration.

---

### UC3 — Curse-Breaking Meditation

**Actor:** The player
**Goal:** Remove curse levels from the magical girl uniform through sustained meditation
**Trigger:** Meghan's uniform has accumulated curse levels from combat; player chooses to meditate to break them

**Context:**

When the magical girl uniform absorbs damage in combat, it can accumulate curse levels. Each curse level reduces the uniform's maximum durability cap and applies restrictive cursed outfit items that persist even when untransformed. Removing curses requires sustained meditation — this is not a quick recovery session but a multi-hour commitment per level.

**Curse meditation requirements:**

| Curse Level | Cap Reduction | Meditation Required |
|-------------|--------------|-------------------|
| Level 1 | Cap at 75% | 8 hours |
| Level 2 | Cap at 50% | 16 hours |
| Level 3 | Cap at 25% | 24 hours |
| Level 4 | Cap at 0% | 32 hours |
| **Total (all levels)** | — | **80 hours** |

Curses must be broken from the most restrictive level outward (level 4 first, then 3, then 2, then 1).

**Step by step:**

1. **Player initiates curse-breaking meditation.** This is a separate option from basic meditation — the player explicitly targets a curse level
2. **Meditation begins.** Clock advances. Mana is channeled steadily into breaking the curse. This is slower and more demanding than basic meditation — it does not provide the same CP/mana recovery benefits simultaneously
3. **Progress saves between sessions.** The player does not need to complete a curse level in one sitting. 4 hours today and 4 hours tomorrow complete a level 1 curse. Progress accumulates across sessions
4. **Session ends** when the player chooses to stop or the curse level breaks

**Interruption and risk:**

- **A cursed attack resets all current meditation progress to zero.** If Meghan has meditated 12 hours toward a 32-hour level 4 curse and takes a cursed hit, those 12 hours are lost. She must start that level from zero
- **Cascading interruption:** A cursed attack mid-meditation may also apply new curse levels if it drives the uniform below a new threshold. The player loses progress, gains a deeper curse, and faces a longer total meditation requirement
- **Prerequisite:** Meditation on a given curse level requires the uniform to be at or above the cap threshold of that level. Meghan cannot meditate on a level 3 curse (cap 25%) if her uniform is currently below 25%. She must break deeper curses first or recharge the uniform to the threshold

**What the player feels:** Curse-breaking meditation is a tax on time. 80 hours of meditation to clear a full curse stack is a significant portion of the game calendar. The player must balance curse removal against study, work, and story progression. Ignoring curses is viable in the short term but compounds — cursed outfit items restrict movement and create vulnerability. The system punishes procrastination and rewards defensive play that minimises curse accumulation in the first place.

---

### UC4 — In-Combat Micro-Meditation

**Actor:** The player
**Goal:** Briefly stabilise mana disruption or recover composure during an active fight
**Trigger:** Player activates micro-meditation during combat (available at Meditation skill 51+)

**Step by step:**

1. **Player activates micro-meditation** from the action bar during combat
2. **Meghan enters a brief moment of stillness.** She is stationary and vulnerable for a short window — she cannot dodge, block, or attack during this moment
3. **Stabilisation effect applies:**
   - At levels 51–80: partial mana disruption stabilisation, minor composure recovery. The effect is real but modest — it buys breathing room, not a full reset
   - At levels 81–100: significant stabilisation. A single focused moment provides meaningful mana and composure recovery without requiring extended stillness. This is a genuine combat tool at mastery level
4. **Stillness ends.** Meghan returns to full combat capability

**Risk/reward:** The vulnerability window is real. An enemy who reads the micro-meditation (via the animation read system) can punish it with a free hit. The player must find a safe moment — after dodging an attack, when the enemy is recovering, when an ally draws aggro. Using micro-meditation at the wrong time is worse than not using it at all.

**Not a healing button.** Micro-meditation does not recover CP or mana in meaningful amounts during combat. It stabilises disruption and recovers composure — it keeps Meghan functional, not invincible. Full recovery requires leaving combat and meditating properly.

---

### UC5 — Session Stress Management

**Actor:** The system (passive)
**Goal:** Track and manage the hidden background stress that accumulates during extended difficult gameplay
**Trigger:** Automatic; tracked continuously

**How it works:**

Extended difficult gameplay — multiple tough fights, high-stakes missions, stressful social encounters, time pressure — raises a hidden background stress level. This stress is not displayed directly but manifests through subtle performance degradation:

- Slightly slower reaction windows
- Slightly reduced decision quality in AI-contested actions
- Slightly less efficient mana usage
- Slightly reduced skill check performance

The degradation is small per unit of stress but compounds over a long play session without recovery. A player who runs six bounty contracts back-to-back without pause will notice Meghan performing slightly worse on the sixth than the first — not catastrophically, but measurably.

**Meditation as the counter:** Regular meditation practice keeps session stress at or near zero. The higher the Meditation skill, the less stress accumulates in the first place and the faster it clears:

| Meditation Level | Stress accumulation | Clearing rate |
|-----------------|-------------------|---------------|
| 1–20 | Full rate | Slow; requires dedicated sessions |
| 21–50 | Reduced | Moderate; shorter sessions needed |
| 51–80 | Nearly eliminated with regular practice | Fast; brief meditation clears significant stress |
| 81–100 | Negligible regardless of encounter intensity | Near-instant clearing |

**Design intent:** Session stress is not meant to punish the player. It is meant to reward the rhythm of play — work, rest, recover, repeat. A player who naturally takes breaks to meditate, eat, socialise, or study between encounters never notices the system. A player who grinds relentlessly feels a gentle nudge to take care of Meghan. The system reinforces the game's core theme: Meghan is a person, not a combat machine.

---

### UC6 — Daily Loop Integration

**Actor:** The player
**Goal:** Fit meditation into the daily time budget alongside study, work, and social activities

Meditation is not an isolated activity — it is a recurring maintenance task that competes for the same limited hours as everything else Meghan does.

**Typical daily budget considerations:**

| Activity | Time cost | Competes with meditation? |
|----------|-----------|--------------------------|
| Sleep | 6–8 hours (full CP/mana reset) | Meditation can reduce sleep dependency at high skill |
| Classes | Fixed schedule (MCI hours) | Cannot meditate during class |
| Study | Variable (reduced by Studying skill) | Study and meditation compete for the same downtime |
| Jobs | Variable (shift-based or contract) | Cannot meditate during work |
| Meditation | 15 min – 2 hours typical | Fits in gaps between other activities |
| Social | Variable | Social time and meditation compete |
| Curse breaking | Hours per session | Major time sink when curses are active |

**The efficient player** meditates in the gaps — 30 minutes after a fight, 15 minutes before class, an hour at a shrine after a job. These small sessions keep stress at zero, maintain CP/mana, and clear status effects without displacing major activities.

**The neglectful player** skips meditation to maximise job or study time. This works in the short term but compounds — stress builds, CP stays low, status effects linger into the next encounter. Eventually, degraded performance costs more time than meditation would have.

**The curse-burdened player** faces a genuine scheduling crisis. 80 hours of curse-breaking meditation on top of study, work, and story obligations is a serious compression of the calendar. This is intentional — curse accumulation has consequences that extend beyond combat into the daily life loop.

**Meditation and studying interaction:** Meditation before a study session clears stress and recovers CP, improving study quality. A player who meditates for 30 minutes before a 2-hour study block gets more out of the study session than one who studies while stressed and CP-depleted. The two systems reinforce each other.

---

## Open Items

- Exact mana recovery formula per Meditation skill level — the 1-hour-per-sixth baseline needs scaling coefficients for each tier
- CP recovery formula per skill level — how much CP does each tier restore per hour of meditation?
- Whether meditation is interruptible by external events (NPC approaching, combat encounter, robot patrol on campus) or is a protected state once started
- Shrine locations — how many shrines exist? Which elements are represented? How are they distributed across the city?
- Whether shrine elemental attunement provides any benefit outside of magical girl content (base game relevance vs Spicy/Super Spicy only)
- Session stress visibility — is there any UI indicator (subtle mood icon, animation tell) or is it entirely hidden until the player notices performance degradation?
- Whether teammates can assist with curse-breaking meditation to reduce the time requirement
- Cursed mana meditation time — is this a separate timer from curse level meditation, or measured the same way?
- Micro-meditation vulnerability window duration — exact frames/seconds
- Whether meditation XP scales with duration (longer sessions = more XP per hour) or is flat rate (consistent XP per hour regardless of session length)
- Whether there are meditation-hostile locations where meditation is impossible or penalised (noisy industrial zones, active combat areas, the black market holding pipeline)
- Environmental meditation — does meditating in specific non-shrine locations provide minor bonuses? (Park bench, rooftop with a view, MCI onsen)
