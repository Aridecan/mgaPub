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
├── creative-briefs/      Feature-level design briefs
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
| [Art Direction](gdd/art-direction.md) | Visual style, cel shader, colour palette, material system |
| [Character Pipeline](gdd/character-pipeline.md) | Body mesh architecture, hair system, clothing pipeline |
| [Audio Direction](gdd/audio-direction.md) | Music and sound design intent |
| [Boot Manager](gdd/boot-manager.md) | ABM/MBM phased lifecycle, pak search order, dependency resolution |
| [Platforms & Tech](gdd/platforms-and-tech.md) | Engine, platform targets, build pipeline |
| [Scope](gdd/scope.md) | What is in Game 1 and what is not |
| [Save System](gdd/save-system.md) | Autosave, playthrough folders, content tier persistence, streamer mode |
| [UI/UX](gdd/ui-ux.md) | HUD widgets, camera modes, smartphone menu hub |
| [Inventory](gdd/inventory.md) | Equipment, items, and inventory management |
| [Test Modes](gdd/test-modes.md) | Standalone job test modes and quest-loop adventure sandbox |

## Creative Briefs

| Brief | Contents |
|-------|----------|
| [HUD Modification](creative-briefs/CB-hud-modification.md) | Widget point budget, phone tiers, CAMERA dual cap, widget porting |
| [Inventory](creative-briefs/CB-inventory.md) | Picking up, stowing, retrieving, world containers, overload |
| [In-Game Menus](creative-briefs/CB-ingame-menus.md) | Phone-based menu system |
| [Main Menu](creative-briefs/CB-main-menu.md) | Main menu design |
| [Mod Manager](creative-briefs/CB-mod-manager.md) | Mod loading and management UI |
| [Settings Menu](creative-briefs/CB-settings-menu.md) | Settings and configuration UI |
| [Skills Menu](creative-briefs/CB-skills-menu.md) | Skill view and progression tracking UI |
| [Prologue](creative-briefs/prologue.md) | Prologue sequence design |

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
