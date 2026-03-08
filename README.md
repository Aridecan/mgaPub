# Ordinary Girl Meghan / Magical Girl Ava

A story-heavy action RPG built in Unreal Engine.

---

## About

*Ordinary Girl Meghan / Magical Girl Ava* (MGA) is an adult action RPG set approximately 300 years in the future on the colony planet Terridyn — a half-emptied colony world run by megacorporations and quietly contested by powers nobody talks about. You play Meghan, a retired magical girl trying to disappear into ordinary life: work, study, pay rent, make friends. The skills she builds doing that are the same skills she uses when the world stops letting her stay ordinary.

| | |
|---|---|
| **Genre** | Action RPG / Adventure |
| **Engine** | Unreal Engine |
| **Platforms** | Windows (primary), Linux (secondary), Mac (aspirational) |
| **Audience** | Adults (18+) |
| **Monetisation** | Buy once — no in-game purchases |
| **Content** | Modular DLC tiers — Base / Spicy / Super Spicy |

---

## Status

Early development. Design documentation is actively being written.

---

## Repository Structure

```
mgaPub/
├── gdd/                  Game Design Document
├── creative-briefs/      Feature-level design briefs (30 briefs)
├── dev-videos/           Dev video scripts
└── README.md
```

---

## Game Design Document

The GDD is the central design reference. Start with **[gdd.md](gdd/gdd.md)** for the full index.

| Document | Contents |
|----------|----------|
| [Manifesto](gdd/manifesto.md) | Developer commitments to the player and modding community |
| [Content Architecture & DLC](gdd/content-and-dlc.md) | Content tiers, localization, region overrides, load stack |
| [Core Loop](gdd/core-loop.md) | Clock system, economy, school calendar, five jobs, failure pipeline |
| [Systems Overview](gdd/systems-overview.md) | All major systems with brief descriptions and links |
| [Story Overview](gdd/story-overview.md) | Spoiler-light premise, tone, and setting |
| [Title and Branding](gdd/title-and-branding.md) | Title rationale, fraction logo, title screen art, chapter operator system |
| [Art Direction](gdd/art-direction.md) | Visual style, cel shader, colour palette (98 entries), material system |
| [Character Pipeline](gdd/character-pipeline.md) | Body mesh architecture, hair system, clothing pipeline |
| [Audio Direction](gdd/audio-direction.md) | Music and sound design intent |
| [Boot Manager](gdd/boot-manager.md) | ABM/MBM phased lifecycle, pak search order, dependency resolution |
| [Platforms & Tech](gdd/platforms-and-tech.md) | Engine, platform targets, build pipeline |
| [Scope](gdd/scope.md) | What is in Game 1 and what is not |
| [Save System](gdd/save-system.md) | Autosave, playthrough folders, content tier persistence, streamer mode |
| [UI/UX](gdd/ui-ux.md) | HUD widgets, camera modes, smartphone menu hub |
| [Inventory](gdd/inventory.md) | Equipment, items, and inventory management |
| [Difficulty](gdd/difficulty.md) | Combat-only difficulty framework (11 parameters, presets + Custom) |
| [Cutscenes](gdd/cutscenes.md) | Cutscene technical architecture |
| [Traversal](gdd/traversal.md) | Movement and traversal systems |
| [Test Modes](gdd/test-modes.md) | Standalone job test modes and quest-loop adventure sandbox |

---

## Creative Briefs

30 briefs covering the player-facing experience of each major system.

### Core Gameplay

| Brief | UCs | Contents |
|-------|-----|----------|
| [Combat](creative-briefs/CB-combat.md) | 7 | Melee, ranged, damage pipeline, defending, AoE, grappling, intimate combat |
| [Action Bar](creative-briefs/CB-action-bar.md) | 6 | Slot system, folders, grapple mode, form-specific bars, profiles, display layout |
| [Clothing](creative-briefs/CB-clothing.md) | 11 | Damage cascade, durability, pockets, repair, open/closed state |
| [Inventory](creative-briefs/CB-inventory.md) | 6 | Pick up, stow, retrieve, world containers, overload |
| [Camera](creative-briefs/CB-camera.md) | 7 | CAMERA drone, battery, upgrades, team drones, party control |
| [Traversal](creative-briefs/CB-traversal.md) | — | Analog movement, climbing, jumping, crawling, motion matching |
| [Exploration](creative-briefs/CB-exploration.md) | 6 | City navigation, search, bounty investigation, restricted areas, map |
| [Combat Feedback](creative-briefs/CB-combat-feedback.md) | 8 | Hit confirmation, reactions, damage communication, elemental, KO |
| [Knockout & Recovery](creative-briefs/CB-knockout-recovery.md) | — | Brainwave sync minigame, negative CP, post-revival state |
| [Weather & Environment](creative-briefs/CB-weather-environment.md) | 8 | Day-night, skybox, weather, clouds, wind, temperature, gameplay effects |

### Jobs & Economy

| Brief | UCs | Contents |
|-------|-----|----------|
| [Jobs](creative-briefs/CB-jobs.md) | 7 | Waitress, courier, mechanic, hacker, bounty hunter, unlock, reputation |

### Progression

| Brief | UCs | Contents |
|-------|-----|----------|
| [Studying](creative-briefs/CB-studying.md) | 6 | Solo study, classes, study groups, exams, academic performance |
| [Training](creative-briefs/CB-training.md) | — | 8 training hubs, lessons, repeatable drills, cooperative training |
| [Meditation](creative-briefs/CB-meditation.md) | 6 | Basic, shrine, curse-breaking, micro-meditation, stress management |

### Social & Narrative

| Brief | UCs | Contents |
|-------|-----|----------|
| [Social](creative-briefs/CB-social.md) | 7 | Conversation, skill-gated encounters, relationships, faction, inhibition |
| [Dialogue](creative-briefs/CB-dialogue.md) | 8 | Story conversations, choices, skill gates, ambient, phone, group |
| [Failure Pipeline](creative-briefs/CB-failure-pipeline.md) | 6 | Capture, escape, Terry's intervention, consequences, pipeline as story |
| [Notes & Discovery](creative-briefs/CB-notes-and-discovery.md) | 9 | Note generation, categories, map pins, time-sensitive, mission ranking |
| [Cutscenes](creative-briefs/CB-cutscenes.md) | 6 | Non-interactive, interactive, ambient world scenes, skip, memories |

### UI & Menus

| Brief | UCs | Contents |
|-------|-----|----------|
| [HUD Modification](creative-briefs/CB-hud-modification.md) | — | Widget point budget, CAMERA dual cap, widget porting, profiles |
| [Main Menu](creative-briefs/CB-main-menu.md) | — | Three-menu architecture (title, phone, lost phone), new game flow |
| [In-Game Menus](creative-briefs/CB-ingame-menus.md) | — | Phone menu (System button), lost phone menu, notes, memories |
| [Skills Menu](creative-briefs/CB-skills-menu.md) | — | Skill view, attributes, progression tracking |
| [Settings Menu](creative-briefs/CB-settings-menu.md) | — | Display, audio, controls, content, gameplay settings |
| [Controls](creative-briefs/CB-controls.md) | 10 | Keyboard/mouse, gamepad, modifier layers, posture, device switching |
| [Mod Manager](creative-briefs/CB-mod-manager.md) | — | Catalog URL, available/installed/load order views, dependency cascade |

### Systems

| Brief | UCs | Contents |
|-------|-----|----------|
| [Tutorials](creative-briefs/CB-tutorials.md) | — | Tutorial and encyclopedia system |
| [Lock Picking](creative-briefs/CB-lock-picking.md) | 7 | Concentric ring minigame, restraints, electronic locks |
| [Hacking](creative-briefs/CB-hacking.md) | 12 | Cylinder minigame, watchdog, port exposure, corporate signatures |
| [Repair](creative-briefs/CB-repair.md) | — | Signal routing minigame, diagnosis, field repair |

---

## Dev Videos

| Script | Contents |
|--------|----------|
| [What Is a GDD](dev-videos/script-01-what-is-a-gdd.md) | Dev video explaining game design documents |
| [MGA GDD Walkthrough](dev-videos/script-02-mga-gdd-walkthrough.md) | Dev video walking through the MGA GDD |
| [Design Document Decomposition](dev-videos/script-03-design-document-decomposition.md) | Creative briefs, feature briefs, and technical briefs |

---

## Publishing

MGA is developed by Aridecan Games. Development updates are shared through dev videos and SubscribeStar.

---

## License

All rights reserved. This repository contains design documentation for a commercial game in development.
