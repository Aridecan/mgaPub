# Creative Brief — Notes & Discovery

---

## Overview

MGA does not have a quest system for the main story. There are no waypoints, no objective checklists, no "3/7 clues found." The player discovers the story by reading notes that Meghan writes on her phone — her observations, conversations recalled, leads discovered, and lore collected. The player reads, connects the dots, and decides what to pursue. The game recognises their actions and advances the story. There is no "accept quest" step.

Jobs (bounty hunting, courier, mechanic, hacker, waitress) have their own tracking in the Missions section — concrete contracts with clear deliverables, completion conditions, and pay. Those stay. Everything else — the main story, world discovery, character relationships, faction politics — lives in Notes.

This is the Elden Ring approach to story discovery: the game tells the player what happened, what someone said, what Meghan noticed. It never tells them what to do next.

**Related documents:**

- [CB-ingame-menus](CB-ingame-menus.md) — phone menu structure, Notes and Missions sections
- [CB-exploration](CB-exploration.md) — discovery mechanics, map knowledge accumulation
- [CB-jobs](CB-jobs.md) — job system, investigation phase, invisible unlocks
- [CB-dialogue](CB-dialogue.md) — NPC-initiated contact, conversation triggers, phone communication
- [CB-hud-modification](CB-hud-modification.md) — anti-waypoint philosophy, phone as HUD hub
- [CB-tutorials](CB-tutorials.md) — App Guides category, tutorial/encyclopedia
- [Core Loop](../gdd/core-loop.md) — time as primary constraint, school calendar pressure
- [UI/UX](../gdd/ui-ux.md) — phone as menu hub, diegetic interface

---

## Use Cases

### UC1 — Note Generation (Automated Entry Creation)

**Actor:** The system
**Goal:** Create notes in Meghan's phone when she experiences, observes, or is told something noteworthy
**Trigger:** A game event matches one of the defined trigger types

Notes are generated automatically — the player never manually creates them. Meghan "writes" them diegetically (she is keeping notes on her phone, as a person would).

**Trigger types:**

| Trigger | What fires it | Example |
|---------|--------------|---------|
| **Conversation** | An NPC says something significant during dialogue | Celeste mentions a warehouse near the port that has been unusually busy at night |
| **Observation** | Meghan's Perception fires on something unusual | Meghan notices the same unmarked van parked outside the MCI campus three days running |
| **Discovery** | A lore object, terminal, document, or environmental detail is examined | Meghan reads a pre-Exodus corporate memo on a terminal in an abandoned office building |
| **Event** | Something happens in the world — explosion, public announcement, NPC death | An emergency broadcast announces a district lockdown; Meghan records what she heard |
| **Job side-effect** | During a delivery/repair/bounty, Meghan notices something unrelated to the job | A courier delivery to a warehouse reveals military-grade security for a parts depot |
| **NPC contact** | Someone texts or calls Meghan with information | Jennifer sends a message about a rumour she heard at the clinic |
| **Time-based** | Meghan reflects on accumulated experience | After three encounters with the same gang, Meghan writes a note connecting them |

Notes appear with a brief phone notification (same as receiving a text message). The player can read immediately or later.

---

### UC2 — Note Content and Structure

**Actor:** The player (reading) / the system (writing)
**Goal:** Each note provides useful information in Meghan's voice without dictating player action

**Every note contains:**

| Field | Description |
|-------|-------------|
| **Title** | Short, descriptive — Meghan's phrasing, not objective labels. "That warehouse near Dock 7" not "Investigate suspicious warehouse" |
| **Body text** | Meghan's write-up: what she saw, heard, or experienced, in her voice. Tone varies with her personality — wry observations, cautious analysis, blunt assessments |
| **Date/time stamp** | When the note was created, in the in-game calendar (T3PC date format) |
| **Source tag** | Where the information came from — conversation with [NPC], observed at [location], found in [object], received from [NPC] via text |

**Some notes also contain:**

| Field | When it appears | Effect |
|-------|----------------|--------|
| **Location reference** | A specific place was mentioned or observed | Creates an optional map pin (see UC4) |
| **Time window** | A time-sensitive event was mentioned | Recorded in the note; appears on calendar view if enabled (see UC5) |
| **NPC reference** | The person is known to Meghan | Links to the NPC's Contact entry |
| **Cross-reference** | Meghan notices a connection to an existing note | Links to the related note with Meghan's commentary on the connection |

**Notes do NOT contain:**

- Objective checklists
- Progress indicators
- "Go here" instructions
- Completion status
- Reward previews

---

### UC3 — Note Categories

**Actor:** The player
**Goal:** Browse and search notes by category

The Notes section contains five categories:

| Category | What it holds | Primary sources |
|----------|--------------|-----------------|
| **Leads** | Actionable information — things Meghan could investigate or act on. Includes location references and time windows where applicable | Conversations, observations, job side-effects, NPC contact |
| **People** | What Meghan knows about specific NPCs — personality notes, reliability assessments, observed connections to other people, intel gathered through investigation | Conversations, observations, investigation, job side-effects |
| **World** | Background information — history, geography, factions, technology, corporate structure. Encyclopaedia-style reference material | Lore objects, terminals, documents, conversations |
| **Field Notes** | Meghan's personal observations and reflections. Diary-style entries — her read on a situation, emotional reactions, gut feelings, pattern recognition | Observation triggers, time-based reflections, event triggers |
| **App Guides** | Phone app documentation — diegetic help files for each app. See [CB-tutorials](CB-tutorials.md) | System unlocks |

**Browsing and search:**

- Each category displays notes in reverse chronological order (newest first)
- **Keyword search** spans all categories simultaneously — results grouped by category
- A note belongs to exactly one category, but cross-references link between categories
- Unread notes display an unread dot; the category header shows an unread count badge

---

### UC4 — Map Integration

**Actor:** The player
**Goal:** Notes with location data create pins on the city map

When a note references a specific location, a map pin is created:

**Pin behaviour:**

- Pin appears on the map at the referenced location
- Pin shows the note title on hover/select
- Selecting the pin opens the full note
- Pins are **informational, not directive** — no pathfinding line, no distance indicator, no "go here" arrow
- Pins persist until the player dismisses them or the note's relevance expires naturally
- Player can manually dismiss any pin (hides it without deleting the note)
- Pins are icon-coded by note category (Lead, People, World, Field Note)

**Accuracy reflects information quality:**

| Information quality | Pin behaviour |
|--------------------|---------------|
| Specific address or known building | Precise pin at the location |
| General area ("somewhere near the docks") | Area highlight covering the approximate zone |
| District-level ("happens in the Market District") | District-level marker, no precise position |
| No location data | No pin created |

This is consistent with the anti-waypoint philosophy in [CB-hud-modification](CB-hud-modification.md) — if Meghan does not know where something is, the map does not know either.

---

### UC5 — Time-Sensitive Notes

**Actor:** The system / the player
**Goal:** Some notes include deadlines; the player manages their own schedule

When a note references a specific time ("the auction is Saturday," "meet me at Burton's at 9pm"), the note records that time window.

**Behaviour:**

- **No countdown timer on screen.** The player reads the note and remembers (or does not)
- **No reminder notification.** Meghan does not nag the player
- **Calendar view** (optional phone feature): time-windowed notes appear on a simple calendar if the player checks it. The calendar is a passive reference tool, not an alert system
- **Time-skip compatibility:** If the player time-skips past a deadline (via studying, training, meditation, or sleep), the expiry triggers as part of the time-skip resolution. The player is not warned before skipping

**Expiry handling:**

When a time window passes without the player acting:

- The note updates with Meghan's reaction ("Missed the auction. Damn." / "Never made it to Burton's — hope it wasn't important.")
- The opportunity is gone — some events have fallback paths, some do not
- The note is NOT deleted — it remains as a record of what was missed
- Story-critical content always has redundant access paths (multiple NPCs, locations, or events that deliver the same information). Missing one time window does not permanently lock the player out of the main story

**Recurring time windows:** Some notes describe repeating events ("Burton's is busiest on Saturday nights," "the night market opens at sundown"). These do not expire — they are ongoing reference information.

---

### UC6 — Story Thread Discovery

**Actor:** The player
**Goal:** The main story emerges from accumulated notes, not a quest log

The main story has no Missions entry. It exists entirely in Notes:

**How story threads build:**

1. **Story beats generate notes.** Each story event — a conversation, an observation, a discovery — creates one or more notes. The note captures what happened, not what to do about it
2. **Cross-references build threads.** When a new note relates to an existing one, Meghan adds a cross-reference ("This connects to what Celeste told me about the port — see [earlier note title]"). The player follows these connections to build the picture
3. **No thread labelling.** The game does not group notes into "Chapter 1 thread" or "Tierney investigation." The player identifies patterns through reading and attention
4. **Multiple entry points.** Critical story discoveries can be reached through multiple paths — different NPCs, different locations, different jobs. If the player misses one path, another exists. This mirrors the existing pipeline design philosophy: important content has redundant access paths
5. **No progress tracking.** The game never says "3/7 clues found." The player reads what they have and decides if they know enough to act
6. **Acting on notes.** The player goes to a location, talks to a person, or waits for a time based on what they have read. The game recognises their presence or action and advances the story. There is no "accept quest" step

**The conspiracy as emergent discovery:** The overarching conspiracy is never presented as a single quest line. Individual notes — a conversation with Celeste about a corporate merger, a job side-effect revealing unusual security, an NPC mentioning a name Meghan has heard before — accumulate. The player who reads carefully and connects cross-references begins to see the shape of something larger. The player who skims through notes and focuses on jobs may not see it until a story event forces the connection.

---

### UC7 — Job-Notes Separation

**Actor:** The system
**Goal:** Jobs use Missions tracking; everything else uses Notes

Clear separation between the two systems:

| | **Missions** | **Notes** |
|---|---|---|
| **Tracks** | Active job contracts — bounties, deliveries, repairs, hacking jobs, waitressing shifts | Everything else — story leads, world knowledge, personal observations, NPC intel |
| **UI location** | Phone → Missions | Phone → Notes |
| **Has objectives** | Yes — concrete deliverables | No |
| **Has completion** | Yes — success/fail/abandon | No |
| **Has pay** | Yes — credits, reputation, items | No |
| **Has rank** | Yes — F through SSS (see UC9) | No |

**Jobs generate notes as side-effects:**

Jobs are the primary source of story-relevant Notes. The job system does not know or care about the main plot — it tracks deliveries, bounties, and repair contracts. But the player's engagement with jobs produces observations that feed the story:

| Job type | Example side-effect note |
|----------|------------------------|
| Courier delivery | "The warehouse had way more security than a parts depot should need" |
| Bounty investigation | "Three of my last five targets all had ties to the same shell company" |
| Mechanic repair | "This vehicle had military-grade modifications. The client said it was 'just a commuter'" |
| Hacking job | "The data I was supposed to wipe referenced a project that doesn't appear in any public record" |
| Waitressing | "Overheard two suits at table 6 talking about a shipment arriving Thursday. They stopped when they noticed me" |

The bounty investigation phase (see [CB-jobs](CB-jobs.md) UC5) is particularly rich — the district→neighbourhood→building→room narrowing process generates Leads notes, People notes, and Field Notes as Meghan gathers intelligence.

---

### UC8 — Mission Ranking and Chain Missions

**Actor:** The system / the player
**Goal:** Missions are ranked by difficulty; some missions form chains where completing one unlocks the next at a higher rank

**Rank scale:**

| Rank | Difficulty | Typical content |
|------|-----------|-----------------|
| **F** | Trivial | Routine deliveries, simple repairs, low-risk targets with no combat capability |
| **E** | Easy | Standard jobs — moderate time pressure, minor complications, low-level opposition |
| **D** | Moderate | Requires solid skill investment — tighter deadlines, competent opposition, multi-step objectives |
| **C** | Challenging | Skilled opposition, complex infiltration, high-value targets, demanding repair work |
| **B** | Hard | Dangerous targets, restricted locations, tight time windows, faction-sensitive contracts |
| **A** | Very hard | Elite opposition, multi-phase operations, significant combat capability required |
| **S** | Extreme | Top-tier contracts — the hardest content available through normal play |
| **SS** | Exceptional | Rare contracts requiring mastery across multiple skill sets |
| **SSS** | Legendary | The most dangerous missions in the game — reserved for endgame-level capability |

Every mission in the Missions log displays its rank. The rank communicates difficulty honestly — the player can judge whether they are ready before committing. Rank is fixed per mission; it does not scale with the player.

**Rank and reputation:** Higher-rank missions require higher job reputation to access. A bounty hunter with low reputation sees F and E contracts. Building reputation unlocks access to higher ranks. The reputation tiers in [CB-jobs](CB-jobs.md) UC7 map onto the rank scale — low reputation sees low-rank missions, high reputation sees the full range.

**Chain missions:**

Some missions are linked into chains — completing one mission unlocks the next in the sequence, with each successive mission ranked higher than the last. Chains represent escalating engagement with a target, faction, or situation.

**How chains work:**

1. **The first mission in a chain appears as a standalone contract.** The player does not know it is part of a chain — it looks like any other mission at its rank
2. **Completing the mission generates two results:**
   - Standard mission completion (pay, reputation)
   - A new mission appears in the available contracts, ranked one or more grades higher, with a briefing that references the previous mission's outcome
3. **Each link in the chain uses intelligence from the previous mission.** The briefing explicitly connects back — "Thanks to your capture of [target], we now know where [next target] operates." The chain feels like an investigation that builds momentum, not an arbitrary sequence
4. **Chain missions are not mandatory.** The player can complete the first mission and never take the second. The chain waits. Higher-rank chain missions remain available until accepted or until story events make them irrelevant
5. **Chains can branch.** A mid-chain mission might produce intelligence that opens two parallel follow-up missions targeting different aspects of the same organisation. The player chooses which to pursue first, or does both

**Example — Red Skulls bounty chain:**

| Chain position | Rank | Mission |
|---------------|------|---------|
| 1 | F | Grab any low-level Red Skulls member. Target is a street-level dealer with minimal combat ability |
| 2 | E | "Thanks to your capture of [name], we know where one of the Red Skulls enforcers operates. Bring him in." Target is tougher, better armed, may have backup |
| 3 | D | The enforcer's contacts reveal a Red Skulls lieutenant running operations out of a warehouse. Infiltration + combat required |
| 4 | C | The lieutenant was carrying encrypted communications. A hacking job decrypts them, revealing the location of a Red Skulls safehouse. Raid the safehouse, capture the captain |
| 5 | B | The captain talks. The Red Skulls' second-in-command has been identified. Target has personal security, operates from a fortified location, and knows Meghan is coming |
| 6 | A | The Red Skulls leadership is exposed. Take down the boss — maximum security, full gang mobilisation, combat gauntlet |

**Chain properties:**

- **Intelligence carries forward.** Each mission's briefing references specific information obtained from the previous target — names, locations, connections. This is not generic escalation; it is a coherent investigation
- **Side-effect notes accumulate.** Each chain mission generates Notes (per UC7) that build a picture of the target organisation. A player who reads their notes across a full chain has a detailed understanding of the gang's structure, territory, and operations
- **Not all chains are bounty chains.** Courier chains might escalate from routine deliveries to increasingly sensitive cargo through increasingly dangerous territory. Mechanic chains might progress from civilian vehicles to gang vehicles to military-grade hardware. Hacking chains might penetrate deeper into a corporate network across multiple contracts
- **Chain length varies.** Some chains are 3 missions. Some are 6+. The Red Skulls example is long because dismantling a gang is a major undertaking. A corporate espionage hacking chain might be 3–4 missions
- **Chains can intersect.** A bounty chain targeting one gang might produce intelligence relevant to a hacking chain targeting a corporation that funds them. Cross-chain connections appear as Notes cross-references, not as mission log links — the player notices the connection through reading, not through the UI drawing a line

**Standalone missions:** Not all missions are part of chains. One-off contracts at any rank exist — a single bounty, a single delivery, a single repair job. Chains are a subset of the mission pool, not the entire structure.

---

### UC9 — Note Notification and Presentation

**Actor:** The player
**Goal:** Know when new notes arrive without being pulled out of gameplay

**Notification:**

- **New note:** Brief phone vibration + small icon in the corner of the screen — identical to receiving a text message. Not intrusive
- **Batch arrival:** If multiple notes trigger from a single event (e.g. a long conversation), they arrive as a batch notification, not one at a time
- **No pop-up interruption.** Notes never force the player to read them. They accumulate silently if ignored

**Unread tracking:**

- Notes section shows an unread count badge on the phone menu
- Each category shows its own unread count
- Individual notes display an unread dot until opened

**Reading experience:**

- Player opens phone → Notes → selects category → selects note
- Full-screen readable text with date, source tag, and any metadata (location, time window, NPC reference, cross-references) displayed
- Cross-reference links are tappable — selecting one opens the linked note directly
- Map pins (if present) can be viewed or dismissed from within the note

**No forced reading.** The game never requires the player to open a specific note to proceed. Story advancement is triggered by the player's actions in the world (going to a place, talking to a person), not by reading a note. Notes provide the information the player needs to know where to go and who to talk to — but the game does not check whether the note was read, only whether the player took the action.

---

## Open Items

1. Whether "People" notes merge with the existing Contacts section or remain separate (Contacts tracks relationship level and communication; People notes are Meghan's observations and intel about that person)
2. Whether notes can be pinned or favourited for quick access
3. Whether the calendar view for time-sensitive notes is a separate phone section or a filter within Notes
4. Maximum note volume — does the system ever archive old notes, or do they accumulate indefinitely?
5. Whether Meghan's note-writing has personality variation (mood, tiredness, post-combat stress affecting tone)
6. Whether the CAMERA drone's observations generate notes (aerial recon → "I spotted movement on the roof of building X")
7. How notes interact with the failure pipeline — does Meghan lose access to notes when the phone is taken? (Probably yes — consistent with existing phone-loss design in [CB-ingame-menus](CB-ingame-menus.md))
8. Whether other party members (post-Ch.3) contribute notes from their own observations
9. Audio/visual treatment of the note-writing moment — does the player see Meghan type briefly, or is it a silent background process?
10. Whether some notes are deliberately misleading (unreliable NPC information that Meghan records faithfully)
11. Exact mapping between job reputation tiers and mission rank access — at what reputation level does each rank become available?
12. Whether chain missions have a cooldown between links (e.g. "intelligence takes time to process") or if the next link appears immediately on completion
13. Whether failed chain missions can be retried, or if failure breaks the chain (target escapes, intelligence goes cold)
14. Whether the Missions log visually indicates chain membership — does the player see "Red Skulls 3/6" or just the individual mission with its briefing referencing prior work?
15. Maximum number of concurrent active chains per job type
