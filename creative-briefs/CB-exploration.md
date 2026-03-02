# Creative Brief — Exploration & Discovery

---

## Overview

Exploration in MGA is not a separate mode — it is an emergent consequence of doing everything else. Meghan explores the city because she has a delivery to make, a bounty to track, a shortcut to find, or a shrine she heard about. The player's mental map of Terridyn builds through repetition and curiosity, not through a fog-of-war reveal or a checklist of map markers.

The city is always traversable at any skill level. Higher skill investment does not unlock locked areas — it opens new routes through the same space. A player with Parkour 30 reaches a rooftop the player with Parkour 5 walks past. A player with Hacking 20 bypasses an electronic gate the player without it negotiates past or finds another way around. Exploration rewards breadth of capability, not a single progression track.

Two skills directly support exploration. **Exploration** (INT/AGI) governs pathfinding — finding alternate routes, hidden shortcuts, and environmental secrets. **Search** (INT/WIS) governs active investigation — examining objects, sweeping rooms, and searching targets. **Perception** (WIS/INT) runs passively in the background, flagging things the player would otherwise miss. Together, these three skills form a layered discovery system: Perception notices, Exploration navigates, Search investigates.

**Related documents:**

- [Core Loop](../gdd/core-loop.md) — traversal as core loop activity
- [Traversal System](../gdd/traversal.md) — locomotion modes, skill-gated routes
- [CB-traversal](CB-traversal.md) — movement creative brief
- [Exploration Skill](../../mgaPriv/mechanics/skills/exploration.md) — pathfinding, scavenging, environmental puzzles
- [Search Skill](../../mgaPriv/mechanics/skills/search.md) — active investigation, crosshair hover, room sweeps
- [Perception Skill](../../mgaPriv/mechanics/skills/perception.md) — passive detection, environmental awareness
- [CB-jobs](CB-jobs.md) — courier job as city exploration engine, bounty investigation phase
- [Terridyn City](../../mgaPriv/world/terridyn-city.md) — city layout, districts, infrastructure

---

## Use Cases

### UC1 — City Navigation and Route Discovery

**Actor:** The player
**Goal:** Move through the city and discover new routes, shortcuts, and access points
**Trigger:** Any traversal activity — commuting, delivering, pursuing a target, wandering

**Step by step:**

1. **Player has a destination.** A job, a class, a social engagement, or simple curiosity. The player chooses a route — follow the known path or deviate to explore
2. **Exploration skill reveals options.** As Meghan moves through the city, the Exploration skill passively identifies alternate routes. At low levels, only obvious alternatives appear (a clearly visible alley, a marked staircase). At higher levels, hidden paths surface — a rooftop crossing between buildings, a maintenance tunnel entrance, a gap in a fence that leads to a shortcut
3. **Skill checks gate access.** Reaching a discovered route may require a skill check: Parkour for a wall run, Athletics for a climb, Hacking for an electronic lock, Negotiation to talk past a checkpoint guard. The route exists regardless of skill — the player sees it, knows it's there, but may not be able to use it yet
4. **Routes persist.** Once discovered, routes remain on Meghan's mental map. The player does not re-discover the same shortcut. Repeated use of a route makes it familiar — traversal time decreases with familiarity

**City knowledge as progression:** A player who couriers extensively during Chapters 1 and 2 knows the city's geography before they need it for anything dangerous. Rooftop routes discovered during package delivery become escape paths during bounty hunts. Maintenance tunnels found while exploring become infiltration routes. The city rewards the player who looked around.

**Verticality:** Terridyn is built for a million people and houses eighty-five thousand. The city has vertical depth — rooftops connected by walkways, mid-rise terraces, underground subway infrastructure, sewer systems, maintenance levels. Ground-level navigation is always possible; vertical navigation opens with Parkour, Athletics, and Exploration investment.

---

### UC2 — Active Search

**Actor:** The player
**Goal:** Deliberately investigate a location, object, or person for hidden information or items
**Trigger:** Player initiates a search action on an interactive element

**Step by step:**

1. **Player targets something to search.** The crosshair hovers over an object, container, room feature, or restrained NPC. A search prompt appears after a delay that scales with Search skill (2 seconds at beginner, near-instant at expert)
2. **Search check resolves.** The Search skill determines what is found. Every searchable element has a concealment rating. Items with concealment at or below the player's Search threshold are found; items above it are missed
3. **Results delivered.** Found items are added to inventory or flagged as environmental discoveries. Information items (documents, data chips, correspondence) are readable. Environmental discoveries (a hidden passage, a structural weakness, evidence of activity) update Meghan's awareness and may unlock dialogue options or quest progression

**Search types:**

| Type | What it finds | Context |
|------|--------------|---------|
| **Crosshair hover** | Specific object examination | Quick targeted search; best for single items |
| **Room sweep** | Everything searchable in the space | Thorough area investigation; time cost scales inversely with skill |
| **Target search** | Items on a restrained or unconscious NPC | Post-takedown during bounty hunting; finds weapons, documents, keys, hidden items |

**What Search finds that Perception does not:** Perception detects presence — something is there, something is off. Search identifies specifics — what the hidden object is, what the document says, where the access point leads. A player with high Perception and low Search notices many things but understands few of them. A player with high Search and low Perception finds everything they look at but doesn't know where to look.

---

### UC3 — Bounty Investigation

**Actor:** The player
**Goal:** Locate a bounty target through investigation before engaging
**Trigger:** Player accepts a bounty contract and enters the investigation phase

**Step by step:**

1. **Contract provides initial intelligence.** The quality of starting information depends on the contracting client and Meghan's reputation. Low reputation = a name and a rough district. High reputation = last known location, known associates, daily routine, combat capability assessment
2. **Player gathers information through multiple channels:**
   - **NPC interviews** — Small Talk and Negotiation extract leads from informants, associates, witnesses, locals. Different NPCs know different things. Some lie
   - **Record access** — Hacking into databases, accessing public records, purchasing STALKER data (illegal but available). Digital investigation narrows location and reveals patterns
   - **Physical observation** — Stake out likely locations. Perception spots the target or their associates. Search examines a location the target used. Exploration identifies approach routes to the target's likely position
3. **Investigation narrows the search.** Each successful information source reduces the search area — from district to neighbourhood to building to room. Higher skill investment compresses investigation time. A skilled investigator can locate a target in an hour; a novice might spend half a day
4. **Transition to engagement.** Once the target is located, the bounty shifts from investigation to approach and combat. The investigation phase determines the conditions of engagement — a well-investigated target is approached on the player's terms; a poorly-investigated target is found by accident or surprises the player

**Investigation as exploration training:** The bounty investigation phase trains Exploration, Search, and Perception simultaneously. A player who hunts bounties extensively develops a detective's eye for the city — they know where people hide, which databases are useful, and how to read a location for signs of recent activity.

---

### UC4 — Environmental Discovery

**Actor:** The player / the system
**Goal:** Find things in the world that reward curiosity — lore, items, locations, narrative details
**Trigger:** Player explores off the beaten path or investigates environmental details

**What the world contains:**

- **Lore objects.** Readable documents, data terminals, wall art, signage, and environmental storytelling scattered throughout the city. A derelict office building might contain corporate correspondence from before the Exodus. A subway station might have graffiti that references a current gang's history. None of this is required — it rewards the player who looks
- **Hidden caches.** Items stashed in concealed locations — maintenance closets, false walls, locked containers. Concealment rating determines whether Search can find them. Contents range from useful equipment to lore items to currency
- **Shortcuts and passages.** Routes between areas that bypass the standard path. Some are permanent (a tunnel between two buildings); some are temporary (a construction scaffolding that exists during a specific chapter). Discovery requires Exploration skill or organic stumbling
- **Shrines.** Meditation amplification points scattered across Terridyn. Some are visible and accessible; others are hidden, elevated, or gated behind traversal skill. Finding a new shrine is a minor exploration reward with mechanical benefit. See [CB-meditation](CB-meditation.md)
- **NPC encounters.** Some NPCs only appear in specific locations at specific times. A player who explores a district at night might find a character who is never present during the day. These encounters can unlock side content, faction information, or relationship opportunities

**Passive discovery vs active discovery:** Perception-flagged items appear as subtle indicators in the environment — a glint, a sound, a visual anomaly. The player who notices and investigates finds the discovery. The player who walks past misses it. Active exploration (deliberately searching off-path areas) finds things that passive perception would not flag at all — the concealment rating exceeds what Perception can detect, but Search and Exploration can overcome it when the player chooses to look.

---

### UC5 — Restricted Area Infiltration

**Actor:** The player
**Goal:** Enter and navigate a restricted area — corporate office, gang territory, secure facility
**Trigger:** A job, quest, or curiosity takes Meghan somewhere she is not supposed to be

**Step by step:**

1. **Identify the restriction.** The area is gated — locked doors, guard patrols, surveillance systems, ID checkpoints, or simply territorial NPCs who react to an outsider. Perception and Exploration identify the type and extent of the restriction
2. **Choose an approach:**
   - **Stealth** — bypass guards, avoid cameras, move through blind spots. Requires Stealth skill and environmental knowledge
   - **Hacking** — disable security systems, loop cameras, unlock electronic doors. Requires Hacking skill and possibly a terminal access point
   - **Social** — talk past guards, bluff a legitimate reason to be there, negotiate access. Requires Negotiation, Small Talk, or Social Adaptability
   - **Parkour/Athletics** — bypass the front entrance entirely. Reach the building through rooftops, vents, service tunnels. Requires traversal skills and Exploration to find the alternate route
   - **Force** — break in. Loud, fast, and draws attention. Viable when speed matters more than stealth
3. **Navigate the interior.** Inside a restricted area, the player moves through a space designed for security. Search and Exploration reveal interior routes, hidden rooms, and useful items. Stealth and Hacking manage ongoing security. The player's approach determines the experience — a stealth player sees the space from the shadows; a social player walks through the front as if they belong
4. **Achieve the objective and extract.** Getting out may be harder than getting in — especially if the alarm was raised during the incursion

**Multi-skill integration:** Restricted area infiltration is the purest expression of MGA's unified skill system. Every skill Meghan has trained is potentially useful. The courier who learned the building's delivery entrance. The hacker who can loop the camera feed. The martial artist who can subdue a guard silently. The negotiator who can talk her way out if caught. No single skill path dominates — the player uses whatever they have.

---

### UC6 — Map and Knowledge Accumulation

**Actor:** The system
**Goal:** Track and present the player's accumulated knowledge of Terridyn
**Trigger:** Ongoing; updated as the player explores

**How knowledge accumulates:**

- **Locations visited** are remembered. The player's map reflects where Meghan has been, not where the game has revealed. Areas she has not visited are blank — not fogged, simply absent
- **Routes discovered** are marked. Shortcuts, alternate paths, and traversal options that Meghan has found appear on her mental map. Routes she has seen but cannot access (skill too low) are marked differently — she knows they exist but cannot use them yet
- **Points of interest** are logged. Shops, training areas, shrines, NPC locations, job sites, and significant landmarks are recorded as Meghan encounters them
- **Investigation notes** persist. Information gathered during bounty investigations, hacking jobs, or conversations is stored. The player can review what they know about a location, faction, or NPC

**Map as earned knowledge:** The map is not a game UI imposed on the world — it is Meghan's understanding of the city, filtered through her phone's AR overlay. A player who has explored extensively has a rich, detailed map. A player who has stuck to the same route between the apartment and Burton's has a sparse one. The difference is visible and meaningful.

---

## Open Items

- Map UI design — how is the map displayed? Phone AR overlay, pause-menu map, or both?
- Whether discovered routes appear on the map automatically or require the player to manually note them
- Discovery feedback — what does the player see/hear when they find something? Subtle indicator, notification, or nothing until they search?
- Lore object density — how many environmental storytelling items exist per district? What ratio of exploration to discovery keeps the player rewarded without overwhelming?
- Whether exploration XP scales with novelty (first visit to a new area worth more than revisiting) or is flat
- Concealment rating distribution — what percentage of hidden items are findable at each Search tier?
- Whether the map reveals NPC schedules (shows where an NPC typically is at different times) or if the player must learn schedules through observation
- Fast travel — does Meghan gain fast travel to known locations (via monorail, taxi, or phone app) or must she always traverse manually?
- Whether exploration has a completionist layer (total discoveries per district, percentage explored) or if that contradicts the organic discovery philosophy
- District-specific exploration content — does each district have unique discovery types, or is the system uniform across the city?
- Underground exploration — how extensive is the subway/sewer network as an explorable space? Is it a full secondary traversal layer or limited to specific locations?
- Whether the CAMERA drone provides aerial reconnaissance that supplements ground-level exploration
