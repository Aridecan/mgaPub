# Creative Brief — Cutscenes

---

## Overview

MGA uses three tiers of cutscene, each offering a different balance of authorial control and player agency. At one end, Non-Interactive Full cutscenes are directed cinematic sequences where the player watches a major story moment. At the other end, Ambient World Scenes are scripted NPC exchanges that play in the environment while the player retains full control — and can interrupt them. In between, Interactive cutscenes structure a conversation with authored staging while the player keeps camera control and makes dialogue choices.

All three tiers are rendered in-engine using live game actors wearing their actual equipped clothing and gear. All are skippable. All respect content tier settings (Base/Spicy/Super Spicy). The technical architecture — Level Sequencer, state snapshots, skip mechanics, Memories integration — is documented in [Cutscenes GDD](../gdd/cutscenes.md). This brief defines the **player experience** of each tier.

**Related documents:**

- [Cutscenes GDD](../gdd/cutscenes.md) — technical architecture (Level Sequencer, state snapshots, skip, content tiers)
- [CB-dialogue](CB-dialogue.md) — dialogue system (story conversations, ambient dialogue, NPC-initiated contact)
- [CB-ingame-menus](CB-ingame-menus.md) — Memories replay menu
- [CB-notes-and-discovery](CB-notes-and-discovery.md) — note generation from overheard ambient scenes
- [CB-camera](CB-camera.md) — CAMERA drone behaviour, gameplay camera
- [CB-combat-feedback](CB-combat-feedback.md) — combat vocal responses (distinct from cutscene dialogue)
- [Audio Direction](../gdd/audio-direction.md) — VO pipeline, dialogue document
- [Animation Architecture Notes](../../mgaPriv/mechanics/animation-architecture-notes.md) — cutscene animation subsection

---

## Use Cases

### UC1 — Non-Interactive Full Cutscene

**Actor:** The player (watching/skipping)
**Goal:** Experience a major story moment presented as a directed cinematic sequence
**Trigger:** Story event, chapter transition, or scripted encounter reaches a cutscene-flagged moment

The biggest, most authored scenes in the game. Meghan is present and performing scripted actions — fighting, transforming, reacting, moving through a space. The camera is fully sequence-controlled with cinematic angles, cuts, dollys, and focus pulls. Full voice acting with authored choreography.

**What the player experiences:**

1. **Scene triggers.** Gameplay yields to the cutscene. A brief transition (camera blend, possible fade) moves from the gameplay camera to the sequence's opening shot
2. **The scene plays.** The player watches. Camera angles shift per the authored sequence. Characters perform scripted animations — custom actions, reactions, combat choreography. Dialogue plays with full voice acting and subtitles per player settings
3. **The player has no control beyond skip.** No camera control, no movement, no dialogue choices. The skip prompt is visible throughout (small, unobtrusive, one-button press)
4. **Scene concludes.** The sequence ends. Camera blends back to the gameplay camera. The CAMERA drone, inactive during the cutscene, repositions to a valid orbit behind Meghan. The player regains full control

**Exit state specification:**

Every Non-Interactive Full cutscene has an **authored exit state** — a defined snapshot of where the world should be when the scene ends. This specification includes:

- Actor positions and rotations (where is everyone standing?)
- Animation states (what pose are they in?)
- Equipment states (what is held, holstered, drawn?)
- Active effects (ice coating, transformation state, environmental changes)
- World state changes (doors opened, objects moved, lighting shifts)

The exit state serves two purposes: it is the natural end-of-scene state, and it is the **skip target**. When the player skips, the world snaps to this state with a brief blend transition (target: 0.3–0.5 seconds). The player sees actors smoothly settle into their post-scene positions rather than a hard cut.

**Front-loaded consequences:**

Gameplay consequences — items granted, flags set, relationship changes — are applied at scene **start**, not gated by watching duration. Skipping at two seconds gives the same gameplay result as watching the full five-minute scene. This is a locked design decision from [Cutscenes GDD](../gdd/cutscenes.md).

**Examples:** Chapter-ending reveals, major confrontations, transformation sequences, first meetings with key characters, major identity reveals, combat set-pieces that require choreography beyond the normal combat system.

<details>
<summary>⚠️ Story Spoiler — Identity reveal example</summary>

The identity reveal referenced is the Tierney identity reveal.

</details>

---

### UC2 — Interactive Cutscene

**Actor:** The player
**Goal:** Participate in a structured conversation with authored staging, movement, and dialogue choices
**Trigger:** Story event or NPC interaction reaches a scene that requires staging beyond normal dialogue

A middle ground between a full cinematic and normal gameplay conversation. NPCs are staged — positioned where the scene needs them (sitting at a table, standing in a doorway, leaning against a wall). There may be scripted movement between dialogue beats (an NPC stands up, walks to the window, turns back). But the player retains camera control and makes choices that branch the conversation.

**What the player experiences:**

1. **Scene initiates.** NPCs move to their staged positions (or are already there). A brief settling moment as the scene establishes itself. No camera takeover — the player keeps their camera
2. **Dialogue plays.** NPCs speak with full voice acting. The standard dialogue system runs — intent labels, tone tags, skill-gated options appear at branching points per [CB-dialogue](CB-dialogue.md) UC1/UC2. The player makes choices that affect the conversation's direction
3. **The player controls the camera.** They can look around freely — at the NPC speaking, at another NPC's reaction, at the environment, at whatever they want. The game does not force framing
4. **Scripted movement occurs between beats.** An NPC might stand, pace, gesture, sit down. These movements are authored per-scene but happen around the player's camera, not through it
5. **The conversation resolves.** NPCs return to their schedules. Meghan retains her position. The scene dissolves without a hard transition — it was always happening in the live world

**Skip behaviour:**

- **Per-line skip** is available — advance to the next line or the next choice point, same as normal dialogue (CB-dialogue UC1)
- **Skip-all** exits the entire conversation. Unresolved choice points default to the first option. NPCs return to schedules
- Less rigid exit state than Non-Interactive — the conversation was happening in the live world, not on a controlled stage. NPCs were already present; they simply resume their routines

**Examples:** Apartment conversations with Terry and Celeste, job briefings from contacts, confrontations that branch based on player choices, group discussions where the player addresses different characters (CB-dialogue UC7), emotionally significant conversations where staging adds weight.

---

### UC3 — Ambient World Scene

**Actor:** NPCs (performing) / the player (observing or not)
**Goal:** A scripted multi-NPC exchange plays in the world while the player retains full gameplay control
**Trigger:** Proximity-based (Meghan enters the area) or time-based (event occurs at a scheduled moment). Never during combat

The world having its own moments. Two guards discuss a shipment schedule. Students argue about an exam on the campus quad. A mechanic complains to a customer about parts shortages. These scenes play in the environment as world events — the player may observe from a distance, walk past without noticing, or stumble into the scene and interrupt it.

**What the player experiences:**

1. **Scene triggers.** NPCs begin their scripted exchange at their staged locations. There is no transition, no camera change, no notification. The scene simply starts in the world
2. **Audio is spatial.** Dialogue gets louder as Meghan approaches and quieter as she moves away. The player hears the scene as naturally as they would hear any conversation in the environment
3. **The player retains full control.** Movement, camera, all actions available. The player is not locked into watching — they can approach, eavesdrop, walk past, or ignore the scene entirely
4. **NPCs have awareness.** If Meghan enters their detection range, normal Perception/Stealth rules apply. The guards might notice Meghan and stop talking. The scripted exchange ends and normal AI behaviour takes over — they react to the intruder, challenge her, or simply go quiet. The scene is interrupted and cannot be resumed
5. **If uninterrupted, the scene concludes naturally.** NPCs finish their exchange and return to their normal schedules and AI routines

**Key narrative points and Memories recording:**

Each Ambient World Scene can define **key narrative points** — specific lines or moments in the exchange that carry important information. Recording to the Memories catalogue is conditional:

| Scene type | When it records |
|-----------|----------------|
| **Story-relevant** (guards mention the macguffin's location) | Records when the scene reaches its key narrative point, regardless of whether Meghan is close enough to hear. The player might not know it was recorded until they check Memories |
| **Flavour/world-building** (students arguing about exams) | Records at author's discretion — may record on natural completion, may not record at all |

If the scene is interrupted before reaching its key narrative point, it does not record to Memories.

**Notes integration:**

If Meghan is close enough to hear key dialogue (within audio range and not obstructed), the conversation can trigger note generation per [CB-notes-and-discovery](CB-notes-and-discovery.md) UC1. Overhearing the guards mention a location creates a Lead note. Catching a fragment of student gossip about a professor creates a People note. The Notes system does not care whether the scene completed — only whether Meghan heard the relevant line.

**Interruption and consequences:**

- Interrupting an Ambient World Scene is not punished — the player was never asked to watch
- The interrupted information may be available through other paths (redundant access, per the Notes & Discovery design philosophy)
- Some scenes are one-time events; others can recur (same guards, different conversation, different day) — this is an authoring decision per scene
- If the player interrupts a scene that contained a story-critical clue, the clue is available elsewhere. The game does not require the player to have eavesdropped successfully

**Examples:** Guard conversations revealing patrol schedules or shipment intel, student gossip providing NPC background, NPC arguments that establish faction tensions, a public announcement in a plaza, a mechanic venting about a suspicious client, two executives discussing a merger at a café.

---

### UC4 — Skip and Exit State Management

**Actor:** The system
**Goal:** Every cutscene can be skipped instantly with correct world state on exit

Skip behaviour differs by tier because player agency differs by tier:

**Non-Interactive Full:**

| Element | Behaviour |
|---------|-----------|
| Skip input | One button press, no hold, no confirmation dialog |
| Skip prompt | Visible throughout (small, unobtrusive) |
| Transition | Blend from current state to authored exit state (0.3–0.5s) |
| Consequences | Already applied at scene start — skip changes nothing mechanically |
| Branching | If skipped before a choice point, first/default option selected |
| Camera | Returns to gameplay camera; CAMERA drone repositions to valid orbit |
| Exception | One joke scene (~30–45s) where a character playfully obscures the skip prompt |

**Interactive:**

| Element | Behaviour |
|---------|-----------|
| Per-line skip | Advance to next line or next choice point (same as normal dialogue) |
| Skip-all | Exits entire conversation; unresolved choices default to first option |
| Transition | NPCs return to schedules; no blend needed (scene was in the live world) |
| Consequences | Dialogue consequences applied per choice made; unresolved choices use default |
| Camera | Player already had control; no handoff needed |

**Ambient World:**

| Element | Behaviour |
|---------|-----------|
| Skip mechanism | None needed — player was never locked in |
| Walk away | Scene continues playing; audio fades with distance |
| Interrupt | NPCs detect Meghan; scene ends; normal AI takes over |
| Natural end | NPCs finish and return to routines |

---

### UC5 — Content Tier Variants

**Actor:** The system
**Goal:** Cutscenes respect Base/Spicy/Super Spicy content tiers

All three cutscene tiers can have content that varies by the active content tier setting:

- **Non-Interactive Full** — different dialogue, camera framing, or character actions per tier. A scene might show more or less depending on tier
- **Interactive** — dialogue options or NPC responses may shift per tier. Skill-gated options may appear only at certain tiers
- **Ambient World** — a conversation's content may shift based on tier (what the guards are discussing, how explicit the detail is)

**Tier rules:**

- Tier is determined by the player's current setting at the time of first viewing
- Not every scene needs variants — many are tier-neutral. Tier-variant authoring is a per-scene decision
- Memories records which tier was active at first viewing; replays use recorded tier, not current setting
- If the player changes their tier mid-playthrough, future cutscenes use the new setting; past Memories entries are unchanged

See [Cutscenes GDD](../gdd/cutscenes.md) and [Content & DLC](../gdd/content-and-dlc.md) for the full tier system.

---

### UC6 — Memories Integration

**Actor:** The player
**Goal:** Replay previously-seen cutscenes from the Memories menu

Builds on the existing Memories design in [CB-ingame-menus](CB-ingame-menus.md). This UC specifies when each cutscene tier records and how each replays.

**When each tier records:**

| Tier | Recording rule |
|------|---------------|
| **Non-Interactive Full** | Always records on first trigger — whether the player watched in full or skipped immediately |
| **Interactive** | Always records on first trigger. Records the **choices the player made** as part of the entry |
| **Ambient World** | Conditional — records when the scene reaches a **key narrative point** before interruption. Story-relevant scenes always record on reaching their key point. Flavour scenes record at author's discretion |

**How each tier replays:**

| Tier | Replay behaviour |
|------|-----------------|
| **Non-Interactive Full** | Replays identically using the recorded state snapshot (characters wear what they wore at first viewing) and recorded content tier. Skip available |
| **Interactive** | Replays with the **choices the player originally made** — the replay is not re-interactive. The player watches their version of the conversation. Skip available |
| **Ambient World** | Replays from a **neutral observer camera position** — since the player's original position during the scene was arbitrary, the replay uses an authored camera that frames the NPCs clearly. Skip available |

**All replays:**

- No gameplay consequences fire — items not re-granted, flags not re-set, relationships not re-triggered
- Purely presentational
- Same skip controls as live viewing
- Listed in story progression order in the Memories catalogue, grouped by chapter
- If a cutscene asset is missing (removed or replaced in a future build), the entry shows a "content unavailable" placeholder

---

## Open Items

1. Whether Interactive cutscenes can transition into Non-Interactive mid-scene (a conversation escalates into a choreographed action sequence)
2. Maximum duration guideline for each tier (Non-Interactive Full should probably have a soft cap to respect player time)
3. Whether Ambient World scenes can chain (one scene ending triggers a follow-up scene nearby)
4. How Interactive cutscene replays handle skill-gated dialogue that the player unlocked — does the replay show the option as it appeared, or does it show the delivered line?
5. Whether Ambient World scenes have a minimum observation distance (Meghan must be within X metres to trigger Memories/Notes recording)
6. How cutscenes interact with weather/time-of-day — do Non-Interactive Full scenes lock lighting, or do they render with current conditions?
7. Whether the player can trigger an Ambient World scene to replay by revisiting the location (same guards, different day) or if it is strictly one-time
8. How many Ambient World scenes can be active simultaneously in a district
9. Whether Non-Interactive Full cutscenes pause the game clock or if time advances during the scene
10. Audio treatment for Ambient World scenes — spatial audio with distance falloff, or mixed differently from normal NPC dialogue?
