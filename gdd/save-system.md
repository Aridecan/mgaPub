# MGA — Save System

---

## Overview

The save system is designed around two principles: player control over when progress is saved, and per-playthrough content tier selection. The second principle enables a streamer workflow without requiring a separate streaming mode or global content switch.

---

## Autosave

Autosave frequency is player-configurable via a slider in the settings UI.

| Setting | Behaviour |
|---------|-----------|
| **Off** | No autosave; manual save only |
| **5 minutes** | Saves every 5 minutes of play time |
| **10 minutes** | Saves every 10 minutes |
| **15 minutes** | Saves every 15 minutes |
| **20 minutes** | Saves every 20 minutes |
| **30 minutes** | Saves every 30 minutes |

Autosave writes to a dedicated autosave slot within the active playthrough folder. It does not overwrite manual saves.

---

## Playthrough Folders

Each playthrough is stored in its own named folder. The player creates a folder when starting a new game and can maintain multiple playthroughs simultaneously.

```
Saves/
  ├── Playthrough_01/
  │     ├── content_config
  │     ├── autosave
  │     ├── save_01
  │     ├── save_02
  │     └── ...
  ├── Playthrough_02/
  │     ├── content_config
  │     ├── autosave
  │     └── ...
  └── ...
```

### Per-Playthrough Content Tier

Each playthrough folder stores its own content tier configuration. Content tier is set when the playthrough is created and applies to all saves within that folder. It is not a global setting.

This enables a player — or a streamer — to maintain separate playthroughs with different content configurations:

| Playthrough | Content tier | Use |
|-------------|-------------|-----|
| Streaming | Base | Safe for broadcast |
| Personal | Super Spicy | Full intended experience |

Switching between playthroughs at the main menu switches the active content configuration. No manual content tier adjustment is needed between sessions.

---

## Streamer Mode

### First Boot Dialog

On first launch, before reaching the main menu, the game asks:

> *"Are you a streamer or content creator?"*

Players who select **Yes** are walked through streamer configuration. Players who select **No** skip directly to the main menu. The question can be revisited at any time in the settings menu.

### Streaming Platform Content Presets

Streamers who opt in are asked which platform(s) they stream to. Each platform has a content preset reflecting that platform's typical content policy:

| Platform | Preset |
|----------|--------|
| Twitch | Base tier only |
| YouTube | Base tier only |
| *(others as applicable)* | TBD per platform policy |

The preset applies to any playthrough the player designates as a streaming playthrough. It does not affect playthroughs not designated for streaming.

Platform presets are advisory, not enforced. The player remains responsible for their own compliance with platform TOS. The game surfaces the information; it does not police the decision.

### Streaming Software Detection

The game requests permission to scan for running streaming software:

> *"May the game check whether streaming software (OBS, Streamlabs) is running, so it can warn you before broadcasting content that may violate your platform's terms of service?"*

This permission is opt-in and can be revoked at any time in settings. The scan checks only for the presence of known streaming applications; it does not read stream configuration, capture data, or transmit anything.

### TOS Warning

If streaming software is detected running and the active playthrough's content tier is Spicy or Super Spicy, the game displays a warning:

> *"Streaming software appears to be active. Your current content settings may not comply with your platform's terms of service. Switch to your streaming playthrough?"*

The player can dismiss the warning and continue, or switch playthroughs from the prompt. The warning reappears each session if the condition persists. It does not block play.

---

## Save File Versioning

Save files include a schema version field from the first public release. This is a non-negotiable requirement given the weekly build release schedule via Bittorrent/SubscribeStar — players will carry saves between builds, and a save written by build N must be loadable by build N+1.

When the game loads a save file:
1. It reads the schema version
2. If the version matches the current build, load normally
3. If the version is older, run the appropriate migration chain and load
4. If the version is newer than the current build (player loading a save from a newer build), display a warning and offer to proceed or cancel — do not silently corrupt

Migration functions must be written alongside any change to the save schema, not after.

Save files are stored in a location the player can easily back up: `Documents/MGA/Saves/` (Windows). This is documented in the game's FAQ.

---

## Save Slots

Each playthrough folder contains:
- **1 autosave slot** — overwritten on each autosave cycle; not user-managed
- **Unlimited manual save slots** — player-named with auto-generated timestamp; no artificial cap

Manual saves display: player-assigned name, timestamp, chapter label, and a screenshot thumbnail.

An **export/export save** option is available in the load screen — exports the full playthrough folder as a zip for backup or sharing between machines. Import validates schema version before loading.

---

## Open Items

- Exact slider increments for autosave (confirm: off / 5 / 10 / 15 / 20 / 30, or different range)
- Whether content tier can be changed on an existing playthrough or is locked at creation
- Number of manual save slots per playthrough folder
- Save file naming — auto-named by timestamp, or player-named
- Whether the streaming software scan runs continuously or only at session start
- Additional streaming platforms and their content presets
