# Creative Brief — Dialogue System

---

## Overview

MGA is a story-heavy game where dialogue carries character development, plot progression, skill training, faction management, and worldbuilding. The dialogue system is not a subsystem — it is the connective tissue between every other system. A conversation at Burton's Bar trains Small Talk and builds a relationship. A negotiation during a bounty job determines the contract terms. An argument with Terry after a failed mission shifts the emotional landscape for the next three chapters.

The system must support three very different conversation types without feeling like three separate systems. Scripted story conversations with full voice acting and careful emotional beats. Functional gameplay conversations (job negotiations, shop transactions, NPC information gathering) that are efficient and repeatable. Ambient background dialogue that makes the city feel populated and alive without demanding the player's attention.

All dialogue is voiced. The dialogue document — a single source of truth containing every line, its emotional context, delivery intent, and scene context — feeds the AI voice generation pipeline during development and serves as the director's brief for human voice replacement when the script is locked. See [Audio Direction](../gdd/audio-direction.md) for the full VO pipeline.

**Related documents:**

- [CB-social](CB-social.md) — relationship system, skill-gated encounters, inhibition
- [Audio Direction](../gdd/audio-direction.md) — dialogue document, VO pipeline, AI/human replacement plan
- [Cutscenes](../gdd/cutscenes.md) — cutscene dialogue, content tier variants
- [CB-combat-feedback](CB-combat-feedback.md) — combat vocal responses, audio barks
- [CB-studying](CB-studying.md) — classroom dialogue, instructor voiceover (DLC)
- [CB-training](CB-training.md) — instructor coaching dialogue during drills
- [UI/UX](../gdd/ui-ux.md) — diegetic UI philosophy, phone interface
- [Prologue](../../mgaPriv/chapters/prologue.md) — immigration dialogue draft (example of authored dialogue)

---

## Use Cases

### UC1 — Story Conversation

**Actor:** The player
**Goal:** Engage in a scripted narrative conversation with a named NPC
**Trigger:** Player initiates dialogue with a story NPC, or a story event triggers a conversation

**Step by step:**

1. **Conversation initiates.** The camera adjusts to frame the participants — a soft pull toward a two-shot or over-the-shoulder angle. Gameplay does not pause; the world continues around the conversation. If the conversation is critical (story-gated), it transitions into a cutscene framing with cinematic camera work. If casual, the camera adjusts but the player retains movement
2. **Dialogue plays.** The NPC speaks with full voice acting. Subtitles display per the player's language and size settings. The speaker is identified by name and portrait. Dialogue lines deliver at natural conversational speed — the player can advance early (skip to next line) or let lines play out
3. **Player choice appears.** At branching points, Meghan's response options appear. Options are brief summaries of intent, not full scripts — the player selects a direction and Meghan delivers the actual line. The number of options varies by context (typically 2–4 plus a leave/deflect option where appropriate)
4. **NPC responds.** The conversation branches based on the player's choice. The NPC's tone, body language, and content shift to reflect the direction taken. Subsequent choices may be affected by earlier ones within the same conversation
5. **Conversation resolves.** The exchange concludes. Relationship state, story flags, and any mechanical outcomes (faction shift, quest update, information gained) are applied. The camera returns to gameplay framing

**Skip and fast-forward:** Available at all times, taught in the prologue. Skip advances to the next player choice point or to the end of the conversation. Fast-forward accelerates dialogue delivery speed. Neither is hold-to-activate — they respond immediately. No story conversation is unskippable; the game can always proceed with default assumptions if the player skips everything.

**Manifesto alignment:** Per Manifesto Principle 5, all tutorials and story content are skippable. The dialogue system respects the player's time without penalising engagement.

---

### UC2 — Player Choice Presentation

**Actor:** The player
**Goal:** Understand the available dialogue options and make a choice that reflects their intent
**Trigger:** A branching point in any conversation

**How choices are presented:**

1. **Options appear as short intent labels.** Each option is a brief phrase (5–15 words) that communicates the direction of Meghan's response — not the exact words she will say. Example: "Press for details" rather than "Tell me exactly what happened on the third floor." This prevents the common RPG problem where the selected line says something the player did not expect
2. **Skill-gated options are visible but marked.** An option unlocked by a social skill shows a small skill tag (e.g., [Negotiation 35]). An option Meghan's current skill cannot support is displayed but greyed out with the skill requirement visible. The player always sees what they are missing — hidden options create frustration; visible-but-locked options create aspiration
3. **Relationship-gated options are visible when close.** Options requiring a relationship threshold appear when the player is close to qualifying. Options far beyond current relationship state are hidden — the player does not see intimate dialogue options with an acquaintance
4. **No timer by default.** Story conversations allow the player to consider their options without time pressure. The NPC waits. The world may continue (ambient activity, background noise) but the conversation does not auto-advance or punish hesitation
5. **Timed choices in specific contexts.** Some conversations — mid-crisis, during confrontation, under external pressure — have a visible countdown. If the timer expires, Meghan defaults to a neutral response (silence, deflection, or the least committal option). Timed choices are the exception, not the rule, and are always contextually justified

**Tone indicators:** Each option carries a tone tag visible to the player — Warm, Neutral, Blunt, Curious, Deflective, Aggressive, Honest. The tag helps the player predict how the NPC will receive the response. Tone tags are brief and unobtrusive — a single word or icon adjacent to the option text.

**What the player cannot do:** The player cannot unsay something. Once a choice is made and Meghan delivers the line, it is part of the conversation history. There is no "go back." The auto-save handles conversation state — reloading to a pre-conversation save is always possible, but the game does not offer mid-conversation undo.

---

### UC3 — Skill-Gated Dialogue

**Actor:** The player
**Goal:** Use Meghan's social skills to unlock better outcomes in conversation
**Trigger:** A conversation contains options gated by skill level

**How skill gating works:**

Social skills open dialogue options that would otherwise be unavailable. The options are not automatic successes — they unlock the attempt. The outcome still depends on a skill check against the NPC's resistance.

| Skill | What it unlocks in dialogue |
|-------|---------------------------|
| **Small Talk** | Warmer openings; rapport-building tangents; NPC relaxation; extracting casual information the NPC wouldn't volunteer to a stranger |
| **Negotiation** | Better deal terms; leverage exploitation; compromise proposals; "what if" framings that shift the NPC's position |
| **Conflict Resolution** | De-escalation paths; mediation between NPCs; redirecting anger; finding common ground where both parties save face |
| **Seduction/Charm** | Influence through presence and delivery; favourable impression; distraction; the overflow mechanic (very high roll exceeds intent) |
| **Social Adaptability** | Recovery from a bad choice earlier in the conversation; matching unexpected tone shifts; navigating unfamiliar social contexts |
| **Perception** | Subtext cues — the player sees additional information about the NPC's emotional state, honesty, and hidden agenda. Perception does not open dialogue options; it annotates existing ones |

**Skill check resolution:** When the player selects a skill-gated option, Meghan delivers the line and the system resolves the check in the background. The NPC's response communicates the result — a successful Negotiation gets a concession; a failed one gets a firm refusal. The player does not see dice rolls or percentage chances. The outcome is delivered through the NPC's words and body language.

**Failure is not disaster.** A failed skill check within dialogue does not end the conversation or lock the player out of the content. It closes that specific approach — the player can try a different angle, accept the worse outcome, or leave and return later (some NPCs cool down over time). The only skill-gated options that permanently disappear on failure are those where the NPC's trust is damaged by the attempt itself (a failed Seduction/Charm on the wrong person, a transparent manipulation attempt caught by a perceptive NPC).

---

### UC4 — Functional Conversation

**Actor:** The player
**Goal:** Complete a gameplay-necessary exchange efficiently — job negotiation, shop transaction, information request
**Trigger:** Player interacts with a functional NPC (shopkeeper, job dispatcher, quest giver, informant)

**Step by step:**

1. **Conversation opens with a functional menu.** Unlike story conversations, functional conversations lead with purpose. A shopkeeper opens with their inventory. A job dispatcher opens with available contracts. An informant opens with "What do you want to know?"
2. **Dialogue wraps the function.** The NPC's personality comes through in how they present the function — a gruff mechanic describes repair options differently from a chatty dispatcher listing deliveries. But the player is never forced through dialogue to reach the functional menu. First visit to a new NPC may include a brief introduction; subsequent visits go straight to business
3. **Negotiation layer (optional).** Functional conversations with Negotiation-eligible NPCs allow the player to push for better terms — higher pay, lower price, additional information. This is a single skill-gated exchange, not a dialogue tree. Accept the offered terms or attempt Negotiation; the result plays out and the transaction proceeds
4. **Reputation affects baseline.** An NPC who respects Meghan (high job reputation, positive faction standing) offers better default terms. Negotiation on top of a good baseline yields excellent terms. Negotiation on top of a poor baseline yields merely acceptable ones. The functional conversation reflects the relationship portfolio without requiring explicit dialogue about it

**Repeat interactions:** Functional NPCs do not repeat their introduction every visit. They greet Meghan with brief, varied lines that reflect relationship state and story context (a shopkeeper who saw Meghan in the news comments on it; a dispatcher who had a bad experience last time is curt). These one-liners are flavour, not interactive — the player is not forced to engage with them before reaching the menu.

---

### UC5 — Ambient Dialogue

**Actor:** The system / background NPCs
**Goal:** Make the city feel populated and alive through non-interactive dialogue
**Trigger:** Meghan passes through populated areas

**Types of ambient dialogue:**

1. **Crowd chatter.** Background conversation fragments from NPCs in public spaces. Not directed at Meghan; she overhears as she passes. Content reflects the current story state — after a major corporate event, crowd chatter references it. During exam season, MCI students discuss studying. These are environmental storytelling snippets, not interactive conversations
2. **NPC barks.** Brief directed lines from NPCs reacting to Meghan's presence or actions. A shopkeeper calls out as she passes ("Hey, courier — got a rush order if you're interested"). A student comments on her appearance. A guard warns her about a restricted area. Barks are one-liners with no response required — they flavour the world without demanding engagement
3. **Contextual reactions.** NPCs react to Meghan's visible state. If she is injured (low CP, visible clothing damage), passersby comment. If she is transformed (magical girl state), reactions range from awe to fear to confusion depending on the NPC and the story stage. If she is restrained or in pipeline-recovery state, reactions are different again. The world notices Meghan
4. **Named NPC ambient lines.** Core cast members who are present but not in conversation produce ambient dialogue — Celeste humming while cooking, Terry making a comment about the weather as they walk across campus, a teammate calling out a tactical observation during combat. These lines are not interactive but they reinforce character presence

**Ambient dialogue does not interrupt.** Barks and chatter play as audio in the environment. They do not pause gameplay, open dialogue boxes, or require player response. The player who is paying attention hears worldbuilding. The player who is focused on their objective passes through without engagement. Both experiences are valid.

**Combat callouts:** During combat, teammates and enemies produce contextual voice lines — warnings about incoming attacks, callouts on enemy positions, reactions to hits taken or dealt, taunts, encouragement. These use the same ambient dialogue system but with higher priority and distinct audio mixing (clearer, louder, directional). See [CB-combat-feedback](CB-combat-feedback.md) for vocal reactions to specific combat events.

---

### UC6 — Phone Communication

**Actor:** The player
**Goal:** Maintain relationships and receive information remotely through calls and texts
**Trigger:** Player uses the phone to contact an NPC, or an NPC contacts Meghan

**Step by step:**

1. **Outgoing contact.** The player opens the phone contacts menu and selects an NPC. Options: Call (voice conversation) or Message (text exchange). Availability depends on the NPC's schedule and story state — calling someone at 3am may get no answer; messaging always delivers but response time varies
2. **Phone conversation plays.** Voice calls use the same dialogue system as in-person conversation but with a different presentation — Meghan's side is heard normally; the NPC's voice has phone audio processing. The camera stays on Meghan (or pulls to a split view showing the NPC's location if story-relevant). Player choices function identically to in-person dialogue
3. **Text exchanges.** Text conversations appear on Meghan's phone screen within the game's diegetic UI. The player selects responses from short options. NPCs respond with variable delay based on personality and urgency. Text conversations are asynchronous — the player can start a text exchange, do something else, and return to check the response later

**Incoming communication:** NPCs can contact Meghan. An incoming call produces a phone ring; the player can answer or ignore. An incoming text produces a notification; the player can read immediately or check later. Urgent story-relevant messages may persist as notifications until acknowledged. Ignoring communication has social consequences — an NPC who was ignored may be less available next time.

**Limitations vs in-person:** Phone communication maintains relationships but does not deepen them as effectively as in-person interaction. Text exchanges are efficient for information transfer and scheduling but lack the emotional weight of face-to-face conversation. Voice calls are closer to in-person but still lack physical presence cues (body language, proximity, shared environment). The system encourages in-person interaction by making phone communication a functional maintenance tool rather than a full substitute.

---

### UC7 — Group Conversation

**Actor:** The player
**Goal:** Participate in a conversation involving multiple NPCs
**Trigger:** A story scene or social encounter involves more than one NPC speaking

**How group conversations work:**

1. **Multiple speakers rotate.** The conversation flows between participants. Each NPC's portrait and name display when they speak. NPCs may address Meghan directly, address each other, or react to what another NPC said. The player sees a multi-person exchange, not a series of one-on-one dialogues
2. **Player choices address the group or individuals.** At branching points, the player's options may target the group ("We need to focus") or a specific participant ("Terry, what do you think?"). Addressing a specific NPC while others are present affects all relationships in the room — siding with one person in a group argument costs standing with the other
3. **NPCs react to each other.** In group scenes, NPCs have opinions about what other NPCs say. Celeste may visibly react to Terry's suggestion. Daisy may interject without Meghan prompting. These inter-NPC reactions are authored as part of the scene and give the player information about NPC relationships independent of Meghan
4. **Conversation can split.** A group conversation may break into sub-conversations — two NPCs continue talking to each other while Meghan engages a third. The player chooses which thread to follow. Missed threads may surface as referenced-but-unheard content later ("Terry told me you all decided..." when Meghan was talking to Celeste during the decision)

**Party tactical conversations.** During missions, group dialogue becomes tactical. NPCs suggest approaches, argue about priorities, and react to the player's plan. Leadership skill affects how readily the team accepts Meghan's direction. At low Leadership, suggestions may be questioned or ignored; at high Leadership, the team follows more readily and offers more constructive input.

---

### UC8 — NPC-Initiated Dialogue

**Actor:** NPCs
**Goal:** NPCs approach Meghan to start conversations based on their own needs, schedules, and emotional states
**Trigger:** An NPC has something to say and Meghan is available

**How NPC initiation works:**

1. **NPC approaches.** The NPC moves toward Meghan and uses a greeting or attention-getting line. A visual indicator (the NPC turning toward Meghan, waving, or simply standing in her path) signals that the NPC wants to talk
2. **Player accepts or declines.** The player can engage (approach, make eye contact, respond to the greeting) or decline (walk past, ignore the attempt). Declining is not rude in most cases — Meghan is busy, and NPCs understand. But repeatedly ignoring the same NPC has relationship consequences. A critical story NPC may be more persistent
3. **Conversation proceeds normally.** Once engaged, NPC-initiated dialogue uses the same branching and choice systems as player-initiated dialogue

**NPC motivations for initiating:**

| Motivation | Example |
|-----------|---------|
| **Story progression** | An NPC has new information, a quest update, or a time-sensitive request |
| **Relationship maintenance** | An NPC Meghan has not spoken to recently reaches out; the frequency reflects the NPC's personality (Celeste checks in often; Terry is more independent) |
| **Reaction to events** | An NPC saw Meghan do something (succeed at a bounty, get captured, transform publicly) and wants to discuss it |
| **Emotional need** | An NPC is dealing with something and seeks Meghan's support — only occurs at sufficient relationship depth |
| **Practical request** | An NPC needs help with something within Meghan's capability (deliver something, fix something, accompany them somewhere) |

**Interruption etiquette.** NPC-initiated dialogue during active gameplay (mid-delivery, mid-fight, mid-study) does not interrupt. The NPC waits, or their attempt is queued and surfaces when Meghan enters a neutral state. Critical story exceptions exist — a teammate shouting a warning mid-combat is immediate, but this is a bark (UC5), not a branching conversation.

---

## Open Items

- Dialogue tree authoring format — what tool or format is used to write branching dialogue? (Unreal's built-in dialogue system, a custom graph tool, an external tool like Yarn Spinner or Ink?)
- Whether dialogue options show predicted outcomes (e.g., "[This will upset Celeste]") or if the player must read the situation
- Exact number of dialogue options per branch point — is there a cap (e.g., max 4 + leave)?
- Whether the player sees Meghan's exact line before committing or only after selection (intent label vs preview)
- Subtitle presentation — bottom-of-screen bar, near-speaker floating text, or phone-style text bubble?
- Portrait style — illustrated character portraits, 3D model close-ups, or no portraits (speaker identified by camera framing only)?
- Whether dialogue can be replayed (log/history accessible during or after conversation)
- Auto-advance vs manual advance — does dialogue advance automatically after the voice line finishes, or does the player press to continue?
- How dialogue handles language — does Meghan speak the local language from the start, or is there a language barrier mechanic?
- Whether background gameplay events can interrupt a conversation (e.g., a robot patrol reaching Meghan mid-dialogue on campus)
- Text message UI design — phone screen mockup needed
- How many ambient bark variants per NPC per context to prevent repetition
- Whether ambient crowd dialogue is procedurally assembled from fragments or fully authored
- Group conversation camera system — how does the camera frame 3+ participants?
- Whether the dialogue document has a standardised template or is freeform
- Modding support — can modders add new dialogue trees, NPCs, and voice lines? What format do they need to provide?
- Whether Meghan's tone of voice in delivered lines reflects the selected option's tone tag or is always neutral (AI VO generation question)
