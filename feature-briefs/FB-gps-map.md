# Feature Brief — GPS Map (Phone Map App)

---

## Overview

The Map app is the player's in-world GPS — accessed through the phone, rendered as a single zoomable vector map of Terridyn City. It is the player's primary spatial reference: where Meghan is, where she's going, where she's been, what's nearby, which districts belong to whom.

Underlying data is procedurally generated from the same source-of-truth used to lay out the city in the engine (road network, river geometry, block addressing, district assignments). The map is **one big SVG**, designed for arbitrary zoom from "whole downtown" down to "individual block face." The same SVG drives both in-game rendering and external uses (development tools, marketing material, technical briefs).

This brief defines the **player-facing design** of the map. The data pipeline and rendering implementation live in [TB-gps-map-pipeline](../technical-briefs/TB-gps-map-pipeline.md).

**Related documents:**

- [TB-gps-map-pipeline](../technical-briefs/TB-gps-map-pipeline.md) — data pipeline, SVG generation, UE integration
- [UI/UX](../gdd/ui-ux.md) — phone-as-menu-hub; Map is one of the phone sections
- [CB-ingame-menus](../creative-briefs/CB-ingame-menus.md) — phone menu container that hosts the Map app
- [CB-notes-and-discovery](../creative-briefs/CB-notes-and-discovery.md) — Notes → map-pin pipeline (leads create pins)
- [CB-traversal](../creative-briefs/CB-traversal.md) — navigation context the map supports

---

## Diegetic Framing

The map is a real consumer GPS app on Meghan's phone, not a privileged-access surveillance overlay. What it shows is what a civilian app would show — publicly available street data, public landmarks, transit information, public-facing district names. **Private information is not on the map by default** — corporate facility internals, gang territory boundaries, mission-specific objectives are added only when Meghan acquires them (intel pickups, quest completions, hacking outcomes).

Time does not pause while the map is open. Reading the map in a dangerous situation is a player choice with consequences (see [CB-ingame-menus](../creative-briefs/CB-ingame-menus.md)).

---

## Visual Layers

The map presents data in stacked layers, with visibility tied to zoom level. **Layers turn on as the player zooms in** so a fully-zoomed-out view shows only district shapes and L3 arterials, while a maximum-zoomed-in view shows individual block addresses and pedestrian-scale detail.

| Layer | First visible at zoom | Notes |
|------|----------------------|-------|
| **Base / water** | Always visible | River and pools rendered as filled shapes; ocean (outer belt) implied at city edges |
| **Districts** | Always visible | Colour-coded zones by [district](../creative-briefs/CB-jobs.md) — corporate, civic, residential, entertainment, industrial; subtle fill so it doesn't compete with structure lines |
| **L3 arterials + bridges** | Always visible | Heaviest line weight; the 12 S drawbridge is shown as a labelled crossing |
| **L2 secondary roads** | Mid zoom | Medium weight |
| **L1 local roads** | Close zoom | Thin weight |
| **Block boundaries** | Closest zoom | Ghosted divider lines between buildings |
| **Major landmark labels** | Mid+ zoom | Central Park, Lock 4, Government Tower, TMC docks, MCI, etc. |
| **Street name labels** | Close zoom | Avenues/Streets/Meridian/Main; rendered along the road centreline |
| **Block address labels** | Closest zoom only | `db1A` / `1A` short form; suppressed at lower zooms to avoid clutter |
| **Player marker** | Always visible | "You are here" pin with facing direction; tracks Meghan in real time |
| **Map pins** | Always visible | Player-set and system-set markers (mission objectives, contacts, lead targets); see *Pin types* below |
| **Discovered POIs** | Always visible (post-discovery) | Tavern, MCI dorm, shops the player has visited |
| **Transit overlay** | Toggleable | Monorail line + stations; bridge crossings; ferry routes if applicable |
| **Private/unlocked overlays** | Once acquired | Corporate facility internals, secret routes, gang territory — appears only after Meghan obtains the intel |

---

## Use Cases

### UC1 — Opening the Map

**Actor:** The player
**Goal:** See where Meghan is and what's around her
**Trigger:** Open phone (CB-ingame-menus UC1) → tap Map section

The map opens to a default zoom that frames Meghan plus a few hundred metres of surroundings. Districts, L3/L2 roads, the river, and recently visited POIs are visible at this zoom. The player marker indicates Meghan's position and facing direction.

**Default zoom level:** "neighbourhood" — roughly one megablock visible.

### UC2 — Panning and Zooming

**Actor:** The player
**Goal:** Look at somewhere other than Meghan's current location
**Trigger:** Drag to pan; pinch / scroll-wheel / controller-trigger to zoom

The single SVG supports continuous zoom. The visual layers (above) come and go at threshold zoom levels — no discrete zoom "levels" the player can feel boundaries on, just smoother detail as they zoom in.

Three labelled zoom presets are available for quick navigation:
- **City** — entire downtown core in view; districts, L3s, river only
- **District** — single megablock; L1/L2 detail visible, street names appear
- **Block** — single superblock or smaller; block addresses visible, fine detail

Returning the focus to Meghan: tap the player marker or use a dedicated "centre on me" control.

### UC3 — Searching for a Location

**Actor:** The player
**Goal:** Find a specific place by name
**Trigger:** Tap the search bar; type a query

Search matches against:
- Street names (localised) — "12 W", "Main Avenue", "Meridian"
- Landmark names — "Central Park", "Lock 4", "Government Tower"
- District names — "Liza Shipwright Mall", "Neon Strips"
- Block addresses — `db17AJ`, `1A`
- POI names (post-discovery) — "Burton's Tavern"
- Contact-associated locations (post-introduction) — "Celeste's apartment"

Search results are ranked by proximity to Meghan and recency of interaction. Selecting a result pans/zooms the map to that location and drops a temporary search marker.

### UC4 — Setting a Personal Pin

**Actor:** The player
**Goal:** Mark a location to return to later
**Trigger:** Long-press / right-click / dedicated button on a map location

Personal pins are player-set markers — colour-coded, optionally named ("crystal vendor", "saw a body"), persist until removed. Distinct visual style from system pins so the player can tell their own pins from quest objectives at a glance.

### UC5 — Following a System Pin

**Actor:** The player
**Goal:** Reach a mission objective or follow up on a discovered lead
**Trigger:** Pin appears on the map automatically (from missions, leads, contacts)

System pins are placed by:
- **Mission objectives** — quest waypoints, drop-off targets
- **Leads from Notes** — see [CB-notes-and-discovery](../creative-briefs/CB-notes-and-discovery.md); leads with location data create map pins
- **Contact-related** — meeting points, time-sensitive opportunities

Tapping a system pin shows its context (which mission, which lead, time window if applicable). Pins automatically clear when their context resolves.

### UC6 — Inspecting a Block or District

**Actor:** The player
**Goal:** See what a specific location is / belongs to
**Trigger:** Tap any block or district region on the map (at sufficient zoom)

Returns a short info card:
- District name and owner (e.g. "Liza Shipwright Mall · Owned by Q.N.S.")
- Public function — residential / commercial / industrial / civic / parking / parkland
- Block address (`db17AJ`)
- Notable POIs on the block (if any have been discovered)

What is NOT shown without prior intel: detailed corporate facility layouts, gang affiliations, secret routes.

### UC7 — Fast Travel (Once Unlocked)

**Actor:** The player
**Goal:** Move quickly between known locations
**Trigger:** Tap a fast-travel-enabled POI on the map (or via search), confirm

Fast travel is unlocked progressively (mechanic specifics TBD — likely via monorail stations, taxi service, or contact-enabled rides). Not every POI is fast-travelable. The map's transit overlay communicates the network.

### UC8 — Map During Magical Girl Form (Ava)

**Actor:** The player as Ava
**Goal:** Use the map while transformed
**Trigger:** Attempt to access phone while transformed

Behaviour depends on phone-and-transformation rules (see [UI/UX](../gdd/ui-ux.md#phone-and-transformation)). Default: the phone is in the pocket dimension during transformation, so the map is unavailable. Workarounds (the toss, the bag, etc.) allow retaining the phone but with their own trade-offs. The map UI itself doesn't change when accessed as Ava — the phone just becomes available.

---

## Localisation

All player-visible text on the map is **localised**. The map SVG is authored with **localisation tags as placeholders**, not literal strings. At runtime (or at SVG-import time in UE), the tags are replaced with localised strings from the project string tables.

Text categories that get localised:
- Street names (Meridian, Main, 1st Avenue North, 12th Street East, etc.)
- District names (Liza Shipwright Mall, Neon Strips, etc.)
- Landmark names (Central Park, Lock 4, Government Tower, etc.)
- POI names (Burton's Tavern, etc.)
- District-info cards (residential / commercial / industrial / civic / parkland labels)

Text categories that do NOT get localised:
- Block addresses (`db17AJ`) — always the same in every locale; an internal coordinate, not a name
- Compass directions (N/S/E/W) — already locale-handled at the UI shell level

Tag format and harvesting mechanics are specified in [TB-gps-map-pipeline](../technical-briefs/TB-gps-map-pipeline.md#localisation-tags).

---

## Development vs Production Output

The same SVG source produces different outputs for different audiences.

| Output | Audience | Differences from production |
|--------|----------|----------------------------|
| **In-game GPS** (production) | Player | Localised labels, styled visual treatment, runtime overlays (player marker, pins), search index, zoom UI |
| **Annotated dev map** (development) | Designer / author | All block `db` addresses labelled at all zooms, technical annotations (sb-row / sb-col grid optional), unlocalised English labels for fast iteration, no player marker or runtime pins. Used to author district assignments and to identify blocks for design discussion. |
| **Marketing renders** | External | Cleaner styling, optionally cropped to specific regions, optional artistic treatment (colour boost, atmosphere, etc.) |

The annotated dev map is what gets handed to the author during city design to ask "what is block `db17AJ` meant to be?" The list of answers feeds back into the district-assignment pipeline.

---

## Open Items

- **Specific zoom thresholds** — at what zoom does each layer turn on? Will be tuned during implementation; the layer-ordering above is the design intent.
- **Fast travel mechanic** — what unlocks it, where it's available, in-fiction cost. Likely tied to monorail/taxi/contacts; TBD.
- **Live traffic / state overlays** — should the map show real-time barge transits at locks, drawbridge open/closed state, road closures from quest events? Could be powerful for navigation; could also be over-engineering. Defer until traffic systems exist.
- **Magical-girl combat state vs phone access** — once Ava-with-phone scenarios are gameplay-implemented, whether the map is usable during combat (and at what cost) needs a pass.
- **Search input on controller** — text search by gamepad is awkward; need a streamlined input scheme (recently-viewed list, voice input, contact-based shortcuts).
- **Private overlays' acquisition pacing** — how often does Meghan acquire new map overlays? Each one is a small reward; the rate needs to feel earned not paced-out.
- **Map persistence across save** — does the player's pin set persist across saves? (Almost certainly yes, but worth confirming.)
- **MGA1 scope** — downtown only? Or downtown + outer belt? Outer belt is largely outside player movement at MGA1 launch but visible at the city zoom. Probably show outer belt as low-detail context.
