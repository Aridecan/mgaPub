# Cutscene System

## Design Philosophy

All cutscenes in MGA are **rendered in-engine** using live game actors. There are no pre-rendered cinematics. Every character appearing in a cutscene wears whatever clothing and equipment they have at that moment in the playthrough — cutscenes reflect the actual game state, not a fixed cinematic version of it.

All cutscenes are **skippable at any time** per [Manifesto Principle 5](manifesto.md). A single button press skips immediately. There is no hold-to-skip and no confirmation dialog. One planned exception exists: a single scene (~30–45 seconds) where a character playfully obscures the skip prompt. It happens once, it is a joke, and it is the only exception.

Cutscenes are not affected by the [difficulty system](difficulty.md) — they are not combat.

---

## Actor Lifecycle

Every actor participating in a cutscene has two defined states: an **enter state** and an **exit state**. These ensure the world is always in a valid configuration, whether the cutscene plays to completion or is cancelled mid-sequence.

### Enter State

The enter state is captured automatically when a cutscene triggers. It records each participating actor's:

- Position and rotation in the world
- Clothing (which garments are worn, degradation state, colour channels)
- Equipment (held items, visible weapons)
- Character visual state (hair configuration, any active temporary effects)
- Current animation state

This captured data forms the **state snapshot** that is stored for cutscene replay in the [Memories system](#memories-system-integration).

### Exit State

The exit state is a defined valid world configuration that each actor transitions to when the cutscene ends. Every cutscene must define exit states for all participating actors. The exit state is reached regardless of whether the cutscene completes naturally or is cancelled.

On **natural completion**, the cutscene's final frame is the exit state — actors are already where they need to be.

On **cancel**, actors blend from their current cutscene pose to their exit state over a brief transition. This is a smooth blend, not a hard snap. The transition duration should be short enough to feel responsive (under 1 second) but long enough to avoid jarring visual pops.

### Front-Loaded Consequences

Gameplay consequences are **applied at cutscene start**, not gated by watching.

If a cutscene grants Meghan an item, triggers a quest flag, or changes a relationship state, that consequence is applied when the cutscene begins — not at a specific point in the sequence timeline. A player who cancels two seconds in receives the same gameplay result as one who watches the full scene.

This prevents a class of bugs where skipping a cutscene at the wrong moment leaves the player in an inconsistent state. It also respects the player's choice: if they skip, they have chosen to move past the presentation but should not be penalised mechanically.

The one exception is **branching cutscenes** that include a player choice (dialogue option, action prompt). If the cutscene is cancelled before the choice point, the default/first option is selected automatically. The cutscene's exit state must account for this.

---

## State Snapshot

The state snapshot captured at cutscene start serves two purposes:

1. **Live rendering** — actors are dressed and equipped correctly for the duration of the cutscene
2. **Replay storage** — the snapshot is saved to the Memories catalogue so the cutscene can be rewatched later with the correct visual state

The snapshot contains, per actor:

| Data | Purpose |
|------|---------|
| Actor identity | Which NPC/character |
| Garment list | Each garment ID, degradation %, colour channel values |
| Equipment | Held items, holstered weapons, visible accessories |
| Visual state | Hair front/back selection, active temporary effects (e.g. ice coating) |

The snapshot does not record actor position or animation — those are driven by the cutscene sequence itself. It records only the visual configuration needed to dress the actors correctly for replay.

### Snapshot Size

Snapshots are lightweight. A typical cutscene involves 2–5 actors, each with a handful of garment and equipment entries. The per-cutscene storage cost is negligible relative to the save file as a whole.

---

## Cutscene Sequencing

Cutscenes are implemented using **Unreal's Level Sequencer**. Each cutscene is a Sequence asset containing:

- **Camera tracks** — cuts, pans, dollys, and focus pulls
- **Actor animation tracks** — character movement and performance within the scene
- **Audio tracks** — dialogue lines, ambient audio, music cues
- **Event tracks** — gameplay triggers that fire at specific timeline points (though major consequences are front-loaded per the rule above, minor visual/audio events like particle effects or ambient sounds may be timeline-driven)

The Level Sequencer is the standard Unreal tool for cinematics and provides the timeline, blending, and camera infrastructure needed without custom systems.

---

## Camera Control

During a cutscene, camera control transfers from the [CAMERA drone](../../mgaPub/creative-briefs/CB-camera.md) to the sequence's camera track. The drone continues to exist in the world but is not player-controllable while the sequence is active.

On cutscene exit (natural or cancel):

1. The sequence's camera track releases control
2. The CAMERA drone repositions to a valid orbit point behind Meghan
3. Camera control returns to the player

The drone reposition uses the same spring arm logic as normal gameplay — it finds a valid position that avoids geometry collision and snaps to orbit distance. If the cutscene's exit location places Meghan somewhere the drone cannot easily reach (e.g. tight interior), the drone uses its standard collision pull-in behaviour.

---

## Content Tier Interaction

Cutscenes respect the active content tier (Base / Spicy / Super Spicy).

Where cutscene variants exist for different content tiers, only the variant matching the active tier plays. The Memories system records which content tier was active when the cutscene was first seen. On replay, the cutscene plays at the **recorded tier**, not the player's current tier setting — this ensures the replay matches what the player originally experienced.

If a player changes content tier mid-playthrough:

- Future cutscenes play at the new tier
- Previously-seen cutscenes in Memories retain their original recorded tier
- A cutscene seen at Base tier and later re-encountered at Spicy tier is recorded again as a separate Memories entry if the content differs

---

## Dialogue and Audio

Cutscene dialogue uses the same VO pipeline as gameplay dialogue — AI-generated from the dialogue document, with emotional context and delivery intent embedded in the source material. See [Audio Direction](audio-direction.md) for the dialogue document architecture.

Subtitles display during cutscenes per the player's subtitle settings (language, size). Audio respects the player's volume configuration (master, voice, music, SFX, ambient channels).

---

## Skip Behaviour

| Input | Result |
|-------|--------|
| Single button press (configurable) | Cutscene ends immediately; actors transition to exit states; gameplay consequences already applied |
| During replay (Memories) | Same skip behaviour; returns to Memories menu |

There is no hold-to-skip timer. There is no "are you sure?" confirmation. The skip prompt is visible on screen throughout the cutscene — small, unobtrusive, but always present.

The one exception: a single joke scene where a character playfully covers or moves the skip prompt for approximately 30–45 seconds. This is the only deviation from immediate skippability in the entire game, per [Manifesto Principle 5](manifesto.md).

---

## Memories System Integration

The **Memories** menu is an out-of-character feature that allows players to rewatch cutscenes they have previously seen. It is accessible from the [OOC menu](../../mgaPub/creative-briefs/CB-ingame-menus.md) regardless of phone status.

### When Cutscenes Are Recorded

A cutscene is added to the Memories catalogue on **first completion** — whether the player watched it in full or skipped it. Skipping counts as completion. The player chose to move past the presentation; they can return to it at their leisure.

### Catalogue Structure

Memories are listed in **story progression order**, grouped by chapter. Each entry shows:

- Cutscene name (descriptive, spoiler-conscious for early entries)
- Chapter label
- First-seen timestamp (in-game date/time)

### Replay Behaviour

- Selecting an entry replays the cutscene using the recorded state snapshot — actors wear the clothing and equipment they had when the player first saw the scene
- The replay uses the content tier that was active at the time of recording
- During replay, the same skip controls are available
- **No gameplay consequences** fire during replay — items are not re-granted, flags are not re-set, relationship states are not re-triggered. The replay is purely presentational

### Save Data

The Memories catalogue is stored **per-playthrough** in the save file, using the existing [extension-safe persistence](save-system.md) architecture:

- Each entry is keyed by cutscene ID
- Entry data: cutscene ID, actor state snapshot, content tier at recording, first-seen timestamp
- The Memories system registers as a persistence consumer via GUID, using the same interface available to DLC tiers and mods
- If a cutscene asset is missing (removed or replaced in a future build), the Memories entry displays a "content unavailable" placeholder rather than crashing or being silently removed

---

## Open Items

- Skip prompt visual design — size, position, fade behaviour
- Transition blend duration on cancel — exact value (suggested: 0.3–0.5 seconds)
- Whether Memories entries include a screenshot thumbnail alongside the text listing
- Maximum number of actors supported per cutscene (practical limit, not artificial cap)
- Whether the Memories menu supports filtering or search once the catalogue grows large
- Branching cutscene default choice policy — always first option, or per-cutscene authoring?
