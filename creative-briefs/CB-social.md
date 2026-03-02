# Creative Brief — Social Encounters & Relationships

---

## Overview

Social interaction is one of MGA's five core loop activities alongside traversal, work, study, and combat. Meghan builds relationships through conversation, shared experience, and daily proximity — not through a gift-giving menu or an approval meter. The system rewards sustained engagement with specific NPCs and punishes neglect through missed opportunities and cooled availability.

Relationships are not a stat the player optimises. They have texture — an NPC who trusts Meghan behaves differently from one who tolerates her, and the player reads this through dialogue, body language, and willingness to help. There is no visible relationship score. The player's sense of where a relationship stands comes from paying attention to the same cues Meghan would.

Social skills (Small Talk, Negotiation, Conflict Resolution, Seduction/Charm, Social Adaptability, Leadership) are trained through social encounters the same way combat skills are trained through fighting. A player who spends time at Burton's Bar talking to regulars arrives at a bounty negotiation with the same social toolkit — no mode switch, no separate social minigame. The unified skill system means every conversation is practice.

**Related documents:**

- [Core Loop](../gdd/core-loop.md) — social as core loop activity
- [Systems Overview](../gdd/systems-overview.md) — relationship system overview, inhibition
- [Relationship Dynamics](../../mgaPriv/plot/relationship-dynamics.md) — core cast relationships, Terry/Tierney mechanics
- [Inhibition](../../mgaPriv/mechanics/inhibition.md) — hidden openness stat, content gating
- [Small Talk](../../mgaPriv/mechanics/skills/small-talk.md) — social skill progression
- [Negotiation](../../mgaPriv/mechanics/skills/negotiation.md) — deal-making, bargaining
- [Conflict Resolution](../../mgaPriv/mechanics/skills/conflict-resolution.md) — de-escalation, mediation
- [Seduction/Charm](../../mgaPriv/mechanics/skills/seduction-charm.md) — influence, overflow mechanic
- [Social Adaptability](../../mgaPriv/mechanics/skills/social-adaptability.md) — contextual flexibility
- [Leadership](../../mgaPriv/mechanics/skills/leadership.md) — team direction, milestone perks
- [CB-jobs](CB-jobs.md) — job interactions as social training
- [CB-training](CB-training.md) — cooperative training as relationship building

---

## Use Cases

### UC1 — Casual Conversation

**Actor:** The player
**Goal:** Talk to an NPC during downtime — at the bar, on campus, at home, in the street
**Trigger:** Player initiates dialogue with an available NPC

**Step by step:**

1. **Player approaches an NPC and initiates conversation.** NPCs have availability windows based on their schedules. Celeste is at Burton's during shifts and at the apartment evenings. Terry is around campus or the apartment. Random NPCs appear in locations based on time and story state
2. **Dialogue plays.** Conversations use a branching dialogue system. Meghan's responses range from casual to probing, kind to blunt. The available options depend on relationship state, story context, and skill levels — higher Small Talk opens warmer options; higher Perception reveals subtext cues the player can act on
3. **NPC reacts.** The NPC's responses reflect accumulated relationship state. An NPC who trusts Meghan shares more freely. One who is neutral gives surface-level responses. One who is annoyed or hurt may deflect, cut the conversation short, or refuse to engage on certain topics
4. **Relationship state shifts.** The conversation adjusts the NPC's internal state — not by a visible number, but through flags: topics discussed, promises made, emotional tone of the exchange. These flags gate future dialogue options and story content
5. **Time passes.** Conversations cost clock time. A brief exchange costs minutes; a deep conversation over drinks costs an hour or more

**What the player decides:** Is this conversation worth the time? Talking to Celeste after a rough day might reveal something about her past. Talking to Terry might ease the debt tension. But that's time not spent studying or working. Social investment competes with everything else in the daily loop.

---

### UC2 — Skill-Gated Social Encounters

**Actor:** The player
**Goal:** Navigate a conversation where the outcome depends on social skill levels
**Trigger:** A conversation reaches a critical juncture — a negotiation, a confrontation, a request for trust

**Step by step:**

1. **The conversation reaches a decision point.** An NPC is upset, a deal is on the table, or Meghan needs something she can't just ask for. The dialogue branches into options that correspond to different social approaches
2. **Skill check determines available options and success:**
   - **Small Talk** options warm the NPC, build rapport, deflect tension
   - **Negotiation** options push for better terms, extract concessions, find compromise
   - **Conflict Resolution** options de-escalate, mediate, redirect anger
   - **Seduction/Charm** options influence through presence, body language, and implication (content tier determines the ceiling)
   - **Social Adaptability** options pivot when the conversation goes sideways — recovering from a faux pas, matching an unexpected tone shift
3. **Resolution.** The skill check against the NPC's resistance determines the outcome. Higher skill provides more favourable options and better success rates. Failure is not catastrophic — a failed Negotiation attempt gets a worse deal, not a slammed door. A failed Conflict Resolution lets the argument continue rather than resolving it
4. **Consequences persist.** Promises made, deals struck, and emotional impressions carry forward. An NPC who was successfully calmed remembers that Meghan helped. An NPC who was unsuccessfully pressured remembers the attempt

**Overflow mechanic (Seduction/Charm):** A roll that dramatically exceeds the intended scope can produce results the player did not intend. The NPC takes the interaction further than Meghan meant — an intended light flirtation becomes an earnest advance, a charm offensive becomes an uncomfortable fixation. High skill reduces overflow risk through precision; low skill increases it through clumsiness.

---

### UC3 — Relationship Progression

**Actor:** The player / the narrative
**Goal:** Build a meaningful relationship with a core cast member over time
**Trigger:** Sustained interaction with the same NPC across multiple days, story events, and shared experiences

**How relationships develop:**

Relationships in MGA do not progress through a single metric. They develop through accumulated interaction history — conversations, shared activities, story choices, and time spent together. The system tracks:

- **Frequency of interaction.** Regular contact maintains and deepens a relationship. Long gaps cause cooling — the NPC may be less available or less forthcoming after absence
- **Quality of interaction.** Deep conversations, honest exchanges, and shared difficulty build trust faster than surface pleasantries. Helping an NPC in a crisis is worth more than a hundred casual greetings
- **Consistency of choices.** NPCs observe Meghan's behaviour. A player who is kind to Celeste but cruel to strangers creates a specific impression. A player who makes promises and breaks them erodes trust regardless of charm
- **Story milestones.** Key relationship events are gated by both relationship state and story chapter. Some moments cannot be rushed — they require the narrative context to land

**What changes as relationships deepen:**

| Relationship Stage | Effects |
|-------------------|---------|
| Acquaintance | Surface dialogue; limited topics; NPC is polite but guarded |
| Friendly | Expanded dialogue options; NPC volunteers information; may ask for small favours |
| Close | Personal topics unlocked; NPC shares vulnerabilities; available as mission support; cooperative training |
| Intimate | Deep emotional content; content-tier-dependent relationship scenes; NPC prioritises Meghan; significant story gating |

**No grinding.** Relationships cannot be maxed by repeating the same conversation. They require breadth of interaction — different contexts, different emotional registers, different activities. The system tracks variety, not volume.

---

### UC4 — Shared Activities

**Actor:** The player
**Goal:** Build relationship through doing things together, not just talking
**Trigger:** Player invites an NPC to an activity or an NPC proposes one

**Activities that build relationships:**

- **Studying together** — study group sessions with Celeste, Daisy (post-Ch.3), or other MCI students. Surfaces academic information solo study misses. Social skill training as a secondary benefit
- **Training together** — sparring with Terry (Martial Arts, playful dynamic), teaching Celeste self-defence (reinforces Meghan's own skill), elemental practice with team members. Documented in [CB-training](CB-training.md)
- **Eating together** — sharing meals at the cafeteria, cooking at the apartment, eating at Burton's. Low time cost, steady relationship maintenance
- **Exploration** — visiting city locations together. NPCs comment on the environment, share memories, point out things Meghan would miss alone. Builds both relationship and city knowledge
- **Missions together** — bounty hunting or hacking jobs with a partner (when available). High-stress shared experience builds relationships faster than low-stakes activities. Also trains cooperative combat skills

**NPC preferences.** Not every NPC enjoys every activity. Terry prefers physical activities and evening hangouts. Celeste prefers cooking, studying, and quiet conversation. Daisy prefers exploration and competitive challenges. The player who pays attention to preferences gets more relationship value per activity.

---

### UC5 — Faction and Reputation Interactions

**Actor:** The player
**Goal:** Navigate the social consequences of job choices and faction alignment
**Trigger:** Meghan's job reputation and faction relationships affect NPC interactions

**How factions affect social encounters:**

The five jobs (waitress, courier, mechanic, hacker, bounty hunter) each have clients whose interests conflict. Working for activist hacker groups reduces corporate trust. Working for one corporation may alienate a rival. Working for gang leaders changes how law enforcement NPCs respond.

1. **NPC reactions reflect faction awareness.** An NPC affiliated with a corporation Meghan has wronged may refuse to help, demand favours, or report her presence. An NPC aligned with a faction Meghan has supported may offer unsolicited assistance, tips, or discounts
2. **Dialogue options shift.** High reputation with a faction opens dialogue branches with affiliated NPCs that low reputation locks out. These are not just transactional — they include lore, story leads, and relationship opportunities
3. **Reputation is a portfolio.** The player manages multiple faction relationships simultaneously. Maximising one faction's trust means trading trust with others. Some players will specialise; others will balance. Both approaches create different social landscapes

**Social consequences of the pipeline:** If Meghan was captured and escaped from the failure pipeline, the pipeline operators and their corporate clients know about her. Subsequent interactions with affiliated NPCs may reference this — increased wariness, targeted offers, or outright hostility depending on the circumstances of the escape.

---

### UC6 — Inhibition and Content Gating

**Actor:** The system (passive)
**Goal:** Track Meghan's evolving openness and gate content accordingly
**Trigger:** Automatic; driven by relationship depth, story progression, and sustained exposure to Terry's influence

**How inhibition works:**

Inhibition is a hidden stat that represents Meghan's internal resistance to vulnerability, intimacy, and self-expression. It starts high — Meghan is guarded, self-contained, and uncomfortable with closeness. Over the course of the game, inhibition lowers through genuine experiences:

- **Terry's consistent warmth.** Terry's personality (and Tierney's ambient succubus influence) gradually reduces Meghan's defensiveness. This is not manipulation in the player's experience — it reads as a person learning to trust someone who is persistently kind
- **Ava's return.** Reclaiming her magical girl identity in Chapter 2 forces Meghan to confront a part of herself she suppressed. This is a major inhibition reduction event
- **Team formation.** Building a team of people who rely on her and whom she relies on reduces isolation-based defensiveness
- **Story milestones.** Key emotional beats — reconciliation with Milo, honest conversations with Celeste, moments of vulnerability with Terry — each reduce inhibition by specific amounts

**What inhibition gates:**

| Inhibition Level | Content Available |
|-----------------|-------------------|
| High | Base game social interactions; surface-level relationships; guarded dialogue |
| Moderate | Deeper emotional conversations; willingness to accept help; some Spicy content opportunities |
| Low | Full relationship intimacy; content-tier-dependent scenes; additional job opportunities; Meghan's dialogue becomes warmer and more honest |

**Design intent:** Inhibition reduction represents genuine character growth, not corruption. Meghan becoming more open is a positive arc. The gating exists to ensure that intimate content arrives at narratively appropriate moments — not because the player found the right dialogue combination, but because Meghan has genuinely changed through sustained experience.

---

### UC7 — The Tierney Reveal and Relationship Recontextualisation

**Actor:** The narrative
**Goal:** Reframe every relationship interaction the player has experienced when the truth about Terry/Tierney is revealed
**Trigger:** Chapter 6 climax — the reveal that Terry, Tabithia, and Teressa are all Tierney

**What changes:**

Every interaction the player had with Terry, Tabithia, and Teressa is retroactively recontextualised. The debt, the warmth, the rescues, the playful training sessions, the job offers through Machina Dynamics — all of it was one person orchestrating Meghan's life.

This is not a mechanical change. No stats are altered. No dialogue is unlocked that wasn't already available. The recontextualisation is entirely in the player's understanding — and in Meghan's.

**Post-reveal social landscape:** Meghan's interactions with Tierney shift. The player has already built a relationship with Terry — the question is whether that relationship survives the reveal. Dialogue options post-reveal reflect this tension. The player can push Meghan toward forgiveness, anger, pragmatic acceptance, or emotional withdrawal. Each choice affects the endgame relationship state and the available content in later chapters.

**NG+ perspective.** On a second playthrough, every interaction with Terry/Tabithia/Teressa carries additional weight. The player sees the manipulation happening in real time. Dialogue that seemed warm now reads as calculated — or perhaps the player sees genuine feeling underneath the calculation. The social system supports both readings.

---

## Open Items

- Whether relationships have any visible metric (subtle UI indicator) or are purely read through NPC behaviour and dialogue
- Exact dialogue system architecture — branching trees, hub-and-spoke, or a hybrid?
- How NPC schedules are implemented — fixed timetables, dynamic based on story state, or a combination?
- Whether the player can actively damage a relationship (insult, betray, neglect) to the point of permanent closure
- How relationship state transfers between Terry/Tabithia/Teressa pre-reveal and Tierney post-reveal — are they averaged, does the strongest survive, or does each persona retain its own track?
- Date activities — what specific activities qualify as dates? Are they mechanically distinct from shared activities?
- Whether NPCs can initiate social encounters (seeking Meghan out) or if all interaction is player-initiated
- Phone communication — can relationships be maintained through phone calls/texts during periods when in-person contact is impractical?
- How the debt repayment through companionship mechanic works mechanically — is time spent with Terry literally currency against the debt, or is it more abstract?
- Whether NPC-to-NPC relationships develop independently of Meghan (e.g., Celeste and Terry becoming closer on their own)
- Group social dynamics — how do conversations change when multiple core cast members are present?
- Whether social encounters have CP cost (emotionally draining conversations) or only time cost
