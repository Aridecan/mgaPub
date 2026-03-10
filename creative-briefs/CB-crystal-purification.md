# Creative Brief — Crystal Purification

---

## Overview

Crystal purification is a **dual-channel peak management** minigame. The player manages two rising meters — **Mana Output** and **Lust** — and must keep both near their peaks simultaneously to maximize the displacement of corrupted mana from a crystal. The session ends in a climax, which costs CP proportional to its intensity. The longer the player sustains the dual-peak state before resolution, the more purification is achieved.

This is a **Super Spicy DLC** minigame. In the base game, crystal purification is handled as a time-skip — the player selects the crystal, confirms the action, and game time advances. The minigame is only active when Super Spicy content is enabled.

The process requires physical contact between the magical girl and the crystal — channelling mana through the body into the crystal to displace the corrupted (Lust-aspected) mana that Tierney's conversion has introduced. The intimate nature of this contact drives the lust buildup, which is both the obstacle (premature climax wastes potential) and the mechanism (lust energy is what pushes the corrupted mana out at resolution).

**Related documents:**

- [Crystal Purification Minigame Notes](../../mgaPriv/mechanics/skills/crystal-purification-minigame.md) — detailed mechanics, channel interactions, skill scaling
- [Mana Aspects Notes](../../mgaPriv/world/mana-aspects-notes.md) — 15-aspect system, Lust aspect, corruption/conversion
- [Meditation Creative Brief](CB-meditation.md) — Meditation skill, curse-breaking (related but distinct)
- [Combat Creative Brief — UC7](CB-combat.md) — intimate combat mechanics (shared CP/lust systems)
- [Knockout Recovery Creative Brief](CB-knockout-recovery.md) — CP zero = KO (climax consequence at high intensity)
- [Combat Resources](../../mgaPriv/mechanics/combat-resources.md) — CP/SP/MP systems

---

## Use Cases

### UC1 — Performing a Purification Session

**Actor:** The player
**Goal:** Purify a corrupted crystal by channelling mana through it, displacing the corrupted mana
**Trigger:** The player initiates crystal purification on a corrupted crystal (requires Super Spicy content enabled, a suitable location, and a corrupted crystal in inventory)

**Step by step:**

1. **Setup** — the player selects a corrupted crystal and begins the purification process. A suitable location is required (private, safe — the apartment, a shrine, or any secure space). The minigame UI opens showing two channels:
   - **Mana Output** — a meter representing the flow of clean mana being pushed into the crystal
   - **Lust** — a meter representing the player character's arousal, driven by the intimate nature of the process

2. **Both channels rise over time.** Mana Output rises as the character channels energy into the crystal. Lust rises due to the physical contact and the Lust-aspected corrupted mana being displaced (the corruption pushes back through the channel, affecting the caster).

3. **The player manages both channels** using two skill-based controls:
   - **Meditation** — stabilises and sustains the Mana Output channel. Higher Meditation skill = smoother mana flow, less fluctuation, more efficient output
   - **Intimate Techniques** — manages the Lust channel. Higher skill = better control over the rate of buildup, ability to hold lust near peak without tipping over

4. **The peak zone** — when both channels are near their maximum simultaneously, the purification rate is highest. Corrupted mana is displaced most efficiently when clean mana output is high AND lust energy is high (the two forces combine to push the corruption out). The player's goal is to enter and sustain this dual-peak state as long as possible.

5. **The balance challenge** — the two channels interact:
   - Mana Output rising too fast without managing Lust causes the lust channel to spike (the corruption pushes back harder)
   - Lust rising too fast without maintaining Mana Output means the energy isn't being channelled productively (high arousal with low output = wasted potential)
   - The optimal state is both channels rising together, entering the peak zone at roughly the same time, and holding there

6. **Resolution** — eventually, Lust reaches its threshold and triggers a climax. This is inevitable — the question is when and at what intensity:
   - **CP cost** — the climax costs CP proportional to its intensity. A controlled climax at moderate intensity costs moderate CP. An intense climax at maximum dual-peak costs more CP but achieves more purification
   - **Purification amount** — determined by the total time spent in the dual-peak zone before resolution. Longer sustain = more corrupted mana displaced = more purification progress
   - **Session ends** after resolution. The character needs recovery time before another session

7. **Crystal state** — each crystal has a corruption level (percentage). Each session reduces the corruption by the amount purified. Multiple sessions may be needed for heavily corrupted crystals. Progress is saved between sessions.

**What the player experiences:** The minigame is a sustained balancing act. Both meters are climbing — the player uses Meditation inputs to keep mana flowing smoothly and Intimate Techniques inputs to regulate lust buildup. The tension comes from wanting to stay in the peak zone as long as possible for maximum purification, while knowing that the longer you hold, the more intense the climax and the higher the CP cost. An experienced player with high skills can sustain the dual peak for a long time, achieving significant purification per session. A low-skill player gets brief peak windows and smaller purification increments — but progress is never lost.

---

### UC2 — Managing the Dual-Channel Balance

**Actor:** The player
**Goal:** Keep Mana Output and Lust near their peaks simultaneously for as long as possible
**Trigger:** The purification session is active (UC1 in progress)

**Step by step:**

1. **The Mana Output channel:**
   - Rises when the player actively channels (Meditation-based input)
   - Falls when focus lapses or when the corruption pushes back
   - **Meditation skill effect:** higher skill = mana output rises faster, holds more steadily at peak, resists corruption pushback better
   - Near peak: mana flow is at maximum efficiency — clean mana is flooding the crystal
   - The channel has natural fluctuation — the player must actively manage it, not just set and forget

2. **The Lust channel:**
   - Rises passively due to the nature of the process — the player cannot prevent it from rising
   - Rate of rise is affected by how much corrupted mana is being displaced (higher mana output = more corruption flowing back through the channel = faster lust buildup)
   - **Intimate Techniques skill effect:** higher skill = finer control over the rate; can slow the rise, hold near peak, and delay resolution
   - **Composure (WIS):** passive resistance to lust spikes — reduces the amplitude of sudden surges
   - Near peak: the character is at the edge of climax — maximum energy but maximum risk

3. **The dual-peak zone:**
   - Both channels above ~80% simultaneously = the peak zone
   - Purification rate in the peak zone is dramatically higher than at lower levels
   - The challenge: Mana Output at peak means maximum corruption pushback, which accelerates Lust buildup. Lust at peak means the character is close to resolution. The player must manage both pressures simultaneously
   - **Skill expression:** a high-Meditation, high-Intimate Techniques character can hold the dual peak for extended periods. A character with one skill high and the other low will find one channel easy and the other overwhelming

4. **Channel interaction — the feedback loop:**
   - High Mana Output → more corruption displacement → faster Lust rise
   - High Lust → more energy available to push mana → Mana Output gets a boost
   - This creates a positive feedback loop: once both channels are high, they reinforce each other — which makes the dual peak both more productive and more dangerous (climax approaches faster)
   - The player rides this feedback loop, trying to surf the peak as long as possible

5. **Player inputs:**
   - **Meditation input** — active control over mana flow. Could be sustained (hold to channel) or rhythmic (timed inputs for optimal flow). Meditation skill widens the timing window and reduces the penalty for mistimed inputs
   - **Intimate Techniques input** — active control over lust rate. Timed regulation inputs that slow, hold, or redirect the buildup. Higher skill = more effective regulation per input
   - The two inputs run simultaneously — the player is managing both channels at once, which is the core challenge

**What the player experiences:** Two meters, two sets of inputs, one goal. The player watches both channels rise, managing each with different controls. Early in the session, it's relaxed — both channels are low, there's time to find the rhythm. As they approach peak, the pace intensifies — the feedback loop accelerates everything, and the player is making rapid decisions to keep both channels in the zone. The moment both hit peak simultaneously is the most productive and most precarious state. A skilled player holds it there, milking every second of purification. An unskilled player or a tired character (low CP) gets a brief peak before resolution forces the session to end.

---

### UC3 — Skill Integration and Progression

**Actor:** The system
**Goal:** Ensure skills meaningfully affect purification capability and create progression
**Trigger:** Any purification session

**Step by step:**

1. **Primary skills:**

   | Skill | Effect on Purification |
   |-------|----------------------|
   | **Meditation** (WIS) | Controls the Mana Output channel. Higher skill = faster output rise, steadier peak hold, better resistance to corruption pushback |
   | **Intimate Techniques** (CHA) | Controls the Lust channel. Higher skill = finer rate control, longer peak hold, ability to delay resolution |

2. **Supporting skills:**

   | Skill | Effect |
   |-------|--------|
   | **Composure** (WIS) | Passive lust spike resistance. Reduces amplitude of sudden surges. Does not prevent the rise — just smooths it |
   | **Mana Control** (POW) | Increases base mana output efficiency. More purification per unit of time spent in the peak zone |
   | **Crystal Knowledge** (INT/Research) | Understanding of crystal corruption. Higher knowledge = better assessment of the crystal before starting (estimated sessions needed, optimal approach) |

3. **Skill progression effect on the minigame:**

   | Skill Range | Purification Capability |
   |------------|------------------------|
   | 1–20 | Difficult to reach dual peak. Brief peak windows (~2–3s). Small purification per session. Multiple sessions needed for even lightly corrupted crystals |
   | 21–50 | Can reach dual peak with effort. Moderate peak windows (~5–8s). Reasonable purification per session |
   | 51–80 | Reliable dual-peak achievement. Extended peak windows (~10–15s). Significant purification per session. Can handle moderately corrupted crystals in 2–3 sessions |
   | 81–100 | Sustained dual peak. Long peak windows (~20s+). Major purification per session. Can handle heavily corrupted crystals efficiently |

4. **CP cost scaling with skill:**
   - Higher Intimate Techniques = more controlled climax = lower CP cost for the same purification amount
   - A master can achieve high purification with moderate CP cost; a novice pays high CP for less purification
   - If CP reaches zero from the climax → KO (per CB-knockout-recovery). This is a real risk at low skill with high-corruption crystals

5. **Training loop:**
   - Purification sessions themselves train both Meditation and Intimate Techniques
   - Practice on lightly corrupted crystals builds skill for heavily corrupted ones
   - Shrine locations may provide bonuses (amplified mana flow, calmer environment)

**What the player experiences:** Skill investment directly translates to purification capability. A character with high Meditation but low Intimate Techniques can push strong mana output but can't manage the lust buildup — sessions are short and costly. A character with both skills high glides through purification efficiently. The progression feels earned: early sessions are clumsy and expensive; later sessions are controlled and productive. The CP risk keeps it meaningful even at high skill — pushing for maximum purification always carries cost.

---

### UC4 — Purification Results and Recovery

**Actor:** The player
**Goal:** Understand the outcomes of a purification session and manage recovery
**Trigger:** A purification session ends (climax resolution)

**Step by step:**

1. **Purification result** — the session summary shows:
   - Corruption displaced: percentage of corrupted mana removed from the crystal this session
   - Crystal state: current corruption level (reduced by the amount purified)
   - CP cost: how much CP the climax consumed
   - Remaining CP: the character's current CP after resolution

2. **Crystal corruption levels:**

   | Corruption Level | Sessions Needed (approximate) | Notes |
   |-----------------|------------------------------|-------|
   | Light (10–25%) | 1–2 sessions | Common crystals, minor corruption |
   | Moderate (26–50%) | 2–4 sessions | Standard quest crystals |
   | Heavy (51–75%) | 4–6 sessions | Significant investment; plot-relevant crystals |
   | Severe (76–100%) | 6+ sessions | Major plot crystals; requires high skill to make meaningful progress per session |

3. **Recovery between sessions:**
   - The character needs CP recovery before another session (natural recovery, Meditation, rest)
   - Attempting purification at low CP is risky — the climax could trigger KO
   - No minimum CP threshold to attempt — the player decides whether the risk is worth it
   - Multiple sessions can be done in a single game day if CP allows, but each session has diminishing returns (the character's focus degrades — Meditation effectiveness decreases for subsequent sessions within the same day)

4. **Fully purified crystal:**
   - Corruption reaches 0% — the crystal is clean
   - Clean crystals can be used for their intended purpose (mana storage, equipment enhancement, quest objectives)
   - Some crystals gain properties based on the mana used to purify them (the clean mana that replaced the corruption carries the caster's aspect)

5. **Failed session** (CP reaches zero during resolution):
   - The character is KO'd (enters knockout recovery per CB-knockout-recovery)
   - Purification progress from that session is partially retained (~50% of what was achieved before KO)
   - The crystal is not damaged — it simply retains its current corruption level
   - This is a setback, not a catastrophe — the player recovers and continues

**What the player experiences:** Each session is a meaningful investment of CP with visible progress. The player sees the corruption meter on the crystal drop after each session and can plan how many sessions they need. Recovery time between sessions creates natural pacing — purification is a multi-day project for heavily corrupted crystals, not something rushed through. The KO risk on high-corruption crystals creates real tension: "One more session today, or rest and come back tomorrow?"

---

### UC5 — First Purification (Tutorial)

**Actor:** The player
**Goal:** Learn the purification minigame through a guided first experience
**Trigger:** The player attempts crystal purification for the first time (Super Spicy content enabled)

**Step by step:**

1. **Context** — the first purification occurs when the player has a corrupted crystal and access to the knowledge of how to purify it. This may come from:
   - Story progression (learning about crystal corruption through the Tierney/mana plot)
   - NPC instruction (an ally or mentor explains the process)
   - Discovery (the player figures it out through experimentation or Research skill)

2. **Guided session** — the first purification has tutorial elements:
   - The two channels are explained (Mana Output and Lust)
   - The relationship between them is described (high output = faster lust rise)
   - The goal is stated clearly: keep both channels near peak as long as possible
   - The player inputs are taught (Meditation control, Intimate Techniques control)
   - The climax/CP cost relationship is explained

3. **A lightly corrupted crystal** — the tutorial uses a crystal with low corruption (10–15%), ensuring the first session can fully purify it regardless of skill level. This gives the player a complete success on their first attempt.

4. **Post-tutorial** — after the first successful purification, the system is fully available. No further hand-holding — subsequent sessions use whatever crystal the player has, at whatever corruption level.

**What the player experiences:** The first purification is gentle. A lightly corrupted crystal, guided instructions, and a guaranteed success. The player learns the dual-channel system, understands the peak zone concept, and sees the CP cost in a low-stakes context. From there, they're on their own — and the stakes escalate with crystal corruption level and story significance.

---

## Open Items

- Visual representation: abstract UI (two meters with controls) or in-world (character animation with overlay meters)
- Exact player input mechanic for each channel — sustained hold, rhythmic tapping, directional input, or something else
- Whether the two inputs can be mapped to different hands/controls (left hand = Meditation, right hand = Intimate Techniques) for simultaneous management
- How the feedback loop acceleration feels — should it be gradual or have distinct intensity phases?
- Exact CP cost formula per climax intensity level
- Whether purification can be interrupted voluntarily (pull out early with partial progress and lower CP cost, or is climax always the resolution?)
- Whether different mana aspects affect the purification process (Lust corruption specifically, or does this system work for other corruption types?)
- Whether Ava and Meghan have different purification capabilities (magical girl vs civilian form)
- Sound and visual design for the peak zone state
- Whether allies can assist in purification (shared channelling — multiplayer input concept?)
- Shrine bonuses: which shrines enhance purification, and by how much?
- Whether crystal corruption can increase if purification is repeatedly failed (corruption fights back)
- How crystal purification connects to the curse-breaking meditation system (CB-meditation UC3) — related processes or completely distinct?
- Session diminishing returns: exact degradation formula for multiple sessions per day
- Whether the crystal's corruption aspect matters (Lust vs other aspects — or is all corruption Lust-aspected due to Tierney?)
