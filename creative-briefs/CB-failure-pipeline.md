# Creative Brief — Failure Pipeline

---

## Overview

When Meghan fails a bounty hunting job — knocked unconscious during a fight, captured during infiltration — she does not see a game over screen. She enters the **black market holding pipeline**: Terridyn's shadow economy for forced labour acquisition. The pipeline is failure *content* — a playable sequence with its own challenges, escape mechanics, and consequences. The player always has a way out; the cost of getting out is the punishment, not the capture itself.

The pipeline serves three design purposes. First, it keeps the player engaged after defeat — unconsciousness routes into gameplay, not a reload screen. Second, it creates meaningful consequences for failure without permanent loss — time, money, reputation, and story state all shift. Third, it reinforces the world's themes: Terridyn's economy runs on exploitation, and Meghan is not exempt from it.

**Related documents:**

- [Core Loop](../gdd/core-loop.md) — failure pipeline overview, game over conditions
- [Systems Overview](../gdd/systems-overview.md) — no-death rule, defeat as content
- [Black Market Job Fairs](../../mgaPriv/world/job-fairs.md) — full pipeline infrastructure, corporate participation, Tax Code 8129
- [Capture and Restraint](../../mgaPriv/mechanics/capture-restraint.md) — restraint mechanics, Super Spicy dimension
- [Escape Artist](../../mgaPriv/mechanics/skills/escape-artist.md) — escape skill, minigame, synergies
- [CB-jobs](CB-jobs.md) — bounty hunting failure trigger, hacker job consequences
- [CB-combat](CB-combat.md) — defeat conditions, CP depletion

---

## Use Cases

### UC1 — Capture

**Actor:** The system (triggered by defeat)
**Goal:** Transition Meghan from a failed encounter into the holding pipeline
**Trigger:** Meghan is knocked unconscious (CP reaches zero) during a bounty hunting job, or is physically restrained and unable to escape during an infiltration

**Step by step:**

1. **Defeat occurs.** Meghan's CP hits zero or she is captured by enemies who have restrained her. The screen does not fade to a game over — it fades to a transition
2. **Time passes.** The clock advances by a variable period — Meghan was unconscious or restrained during transport. The time lost depends on where she was captured and where she ends up (1–4 hours typical)
3. **Meghan wakes in holding.** She is in a holding cell or processing area within the pipeline infrastructure — a converted subway station beneath one of the corporate headquarters. Her equipment has been confiscated. She retains only what was hidden (items in concealed pockets or body-hidden slots, if the Stealth/Search skill check succeeds against the captors' search)
4. **Assessment screen.** The player sees Meghan's current state: CP (partially recovered during unconsciousness, but not fully), equipment status (what was taken, what remains), and the holding environment

**What the player lost:** Time on the clock, equipment (temporarily), job reputation (the failed bounty is marked incomplete), and whatever the bounty target was doing while Meghan was unconscious. The target may have fled, escalated, or become harder to re-engage.

**What the player still has:** All skills, any concealed items, and a way out.

---

### UC2 — Escape (Pre-Ava)

**Actor:** The player
**Goal:** Escape the holding pipeline using mundane skills
**Trigger:** Meghan is in the pipeline during Chapters 1–2, before magical abilities are restored

**Step by step:**

1. **Assess the environment.** The holding area has physical obstacles: locked doors, guard patrols, surveillance systems, other captives. Perception and Search reveal details — guard schedules, lock types, structural weaknesses, potential allies among other captives
2. **Choose an escape approach:**
   - **Lockpicking / Engineering** — defeat physical locks on the cell and exit doors
   - **Hacking** — disable electronic locks, loop surveillance feeds, unlock doors remotely
   - **Stealth** — avoid guards, move through blind spots, time movements to patrol gaps
   - **Martial Arts / Combat** — overpower guards directly (risky — Meghan is CP-depleted and unequipped)
   - **Negotiation / Small Talk** — talk a guard into releasing her, convince another captive to create a distraction, bluff her way through a checkpoint
   - **Escape Artist** — if restrained, the escape minigame must be completed before any other approach is viable
3. **Execute the escape.** Each approach plays out as a sequence of skill checks with the same resolution system as any other encounter. Failure at a stage does not end the escape — it raises the alert level and makes subsequent stages harder
4. **Reach the exit.** Meghan escapes into the city. Her equipment is either recovered en route (if she detours to the confiscation storage) or lost (recovered later via a retrieval mission or purchased back)

**Skill investment payoff:** A player who invested in Stealth and Hacking during courier and hacker jobs has an easier escape than one who relied purely on combat. The pipeline rewards breadth — the same skills Meghan built doing ordinary work are what get her out.

**Alert escalation:** Each failed check or detected action raises the alert level. At low alert, guards are on routine patrol. At high alert, additional guards are called, exits are locked down, and the difficulty of remaining checks increases. A full alert state does not make escape impossible — it makes it expensive in CP and time.

---

### UC3 — Escape (Post-Ava)

**Actor:** The player
**Goal:** Escape the holding pipeline with magical abilities available
**Trigger:** Meghan is in the pipeline during Chapter 3+, after Ava's powers are restored

**Step by step:**

1. **All UC2 options remain available.** Mundane escape routes still work. Magical abilities expand the toolkit, they do not replace it
2. **Additional magical options:**
   - **Transformation** — if Meghan can transform (requires sufficient mana and the uniform is not curse-locked), she gains access to enhanced physical abilities, ice magic, and drastically improved combat capability. Transformation in a confined space is conspicuous — it draws immediate attention
   - **Elemental manipulation** — ice to freeze locks, create barriers, or disable guards non-lethally
   - **Enhanced physical stats** — transformed Meghan can force her way through obstacles that would stop her civilian self
3. **Team rescue possibility.** If Meghan's teammates are aware she's been captured (via CAMERA, phone check-in failure, or story state), they may mount a rescue. This plays out as a timed event — if Meghan hasn't escaped within a window, the team arrives and the escape becomes a cooperative extraction. The player may still need to reach a rendezvous point

**Transformation risk:** Transforming inside the pipeline reveals that Meghan is a magical girl. This has story consequences — the pipeline operators and their corporate clients now know what they captured. Subsequent capture attempts may be better-prepared for magical resistance. The player must weigh immediate escape against long-term exposure.

---

<details>
<summary>⚠️ Story Spoiler — UC4: Terry's Intervention (Late Game)</summary>

### UC4 — Terry's Intervention (Late Game)

**Actor:** Terry / Tierney (NPC)
**Goal:** Remove Meghan from the pipeline via purchase at auction
**Trigger:** Meghan reaches the auction stage of the pipeline in late game, and Tierney becomes aware

**Step by step:**

1. **Meghan reaches auction.** If Meghan does not escape during the holding phase, she is moved to an auction — a formal sale event within the pipeline infrastructure. The clock advances further (hours to a day)
2. **Terry purchases Meghan.** Tierney, operating through Terry or one of her corporate personas, places a winning bid. This is not a rescue in any visible sense — it is a commercial transaction
3. **Meghan is released.** She wakes up in a safe location (Terry's apartment, MCI residential campus, or a neutral handoff point). Her equipment is returned
4. **Debt generated.** The purchase price becomes a formal debt to Terry, added to Meghan's existing debt structure. This debt is real — it must be repaid through bounty hunting income, same as the original debt

**What the player feels:** Relief at escape, but unease at the mechanism. Terry did not storm in with a rescue team. She *bought* Meghan. The debt is a reminder that Meghan's safety has a price, and Terry is willing to pay it — but expects repayment. Whether this is protective or controlling depends on where the player is in understanding Tierney's nature.

**Meghan's awareness:** Meghan may not know Terry arranged the purchase. She may wake up and not understand how she got out. Depending on story state and relationship level, Terry may be transparent about it, evasive, or completely silent. The information surfaces through dialogue and investigation over time.

</details>

---

### UC5 — Consequences and Recovery

**Actor:** The player
**Goal:** Deal with the aftermath of a pipeline cycle
**Trigger:** Meghan has escaped or been released from the pipeline

**Consequences accumulate across pipeline entries:**

| Consequence | Effect |
|-------------|--------|
| **Time lost** | Hours to a full day consumed by capture, holding, and escape. Clock advances are not recoverable. Scheduled events (exam periods, job shifts, story deadlines) may have been missed |
| **Equipment loss** | Confiscated items must be recovered or replaced. Recovery mission: return to the pipeline area and retrieve from confiscation storage (stealth/combat). Replacement: purchase new equipment at market cost |
| **CP/SP depletion** | Meghan exits the pipeline in poor condition. Meditation or sleep needed before engaging in demanding activity |
| **Job reputation** | The failed bounty job is marked incomplete. Reputation with the contracting client decreases. Repeated failures degrade client trust and reduce available contract quality |
| **Debt (if purchased)** | Terry's auction purchase generates debt. Debt compounds with existing obligations |
| **Story state** | NPCs react to Meghan's capture. Teammates express concern or frustration. The bounty target's status has changed during the time Meghan was held. Some story opportunities may have expired |
| **Intelligence exposure** | Pipeline operators now have data on Meghan — her skills, her equipment, her value. Subsequent encounters with the same faction may reference this knowledge. If she transformed during escape, magical girl exposure has additional long-term consequences |

**Recovery loop:** The pipeline exit feeds back into the core loop. Meghan needs rest, equipment, and income. The pipeline didn't end her game — it set her back and created new problems to solve. A player who enters the pipeline once treats it as a learning experience. A player who enters repeatedly is signalling that they need to invest in different skills or take on lower-risk contracts.

---

### UC6 — Pipeline as Story Content

**Actor:** The player / the narrative
**Goal:** Use the pipeline as a window into Terridyn's hidden economy
**Trigger:** Any pipeline entry

The pipeline is not just a mechanical consequence — it is a narrative environment. Each entry exposes the player to:

- **Other captives.** NPCs in the holding area have stories. Some are targets of opportunity; some were specifically hunted. Conversations with captives reveal how the pipeline works, who funds it, and what happens to people who don't escape. Jennifer Belford may be encountered here — she is a recurring target due to her Heavy Worlder physiology
- **Corporate infrastructure.** The holding areas are beneath corporate headquarters. The architecture, signage, and guard uniforms reveal which corporations participate. Tax Code 8129 references appear on paperwork. The player who pays attention learns how deep the system runs
- **Guard behaviour.** Guards are not faceless enemies — they are employees doing a job. Some are indifferent, some are sympathetic, some enjoy their authority. Negotiation and social skills exploit these differences
- **Pipeline progression.** Each entry can expose Meghan to a different stage — holding, processing, auction preparation. A player who has been captured multiple times sees more of the system than one who escapes quickly every time

**Repeatable but varied.** The pipeline is not a single fixed sequence. Holding locations vary. Guard configurations differ. Other captives change based on story state. The core structure repeats, but the details shift to prevent the experience from feeling like a penalty loop.

---

## Open Items

- Exact holding locations — how many pipeline areas exist? Do they rotate or are they fixed per corporate district?
- Whether the player can choose to stay in the pipeline longer to gather intelligence (voluntary delay for information rewards)
- Whether teammates can be captured alongside Meghan (party capture scenario) or if only Meghan enters the pipeline
- Equipment confiscation details — what check determines which items are found vs concealed? Is it a flat skill check or item-by-item?
- Auction mechanics — does the player see the auction? Can Meghan influence the outcome (bidding war, sabotage, escape during transport to auction)?
- Whether hacking job failure can also route into the pipeline (CB-jobs says no; core-loop.md implies yes — needs reconciliation)
- Pipeline entry frequency — is there a cooldown or escalation mechanic to prevent the player from repeatedly farming pipeline content?
- MCI campus prison alternative (Ch.4+) — does minor infraction arrest route through the campus prison instead of the general pipeline? Different escape mechanics?
- Whether the pipeline experience changes in Spicy/Super Spicy content tiers
- Whether other party members can independently enter the pipeline through their own failed missions (off-screen or playable)
- Guard difficulty scaling — do guards scale with Meghan's level/capability, or are they fixed-difficulty with stronger captors in later game?
