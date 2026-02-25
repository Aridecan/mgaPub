# Ordinary Girl Meghan / Magical Girl Ava — Game Design Document

---

## The Razor

> *A skill-unified action RPG where the skills Meghan builds as an ordinary person become the tools she uses as a magical girl — because she was always one person, not two.*

The Razor is the filter for every feature decision. If a proposed feature requires Meghan to operate as two separate characters — a life-sim Meghan and a combat Meghan with separate systems, separate progression, separate rules — it does not belong in this game. Features that reinforce a unified Meghan pass. Features that split her fail.

---

## Elevator Pitch

Ordinary Girl Meghan / Magical Girl Ava is an adult action RPG set on a colony world three centuries from now — half-emptied by plague, run by megacorporations, and quietly contested by powers nobody talks about. You play Meghan, a retired magical girl trying to disappear into ordinary life: work, study, pay rent, make friends. The skills she builds doing that are the same skills she uses when the world stops letting her stay ordinary. One progression system, one person — in a city where almost everyone around her is hiding more than they appear.

---

## Document Index

| Document | Contents |
|----------|----------|
| **[Manifesto](manifesto.md)** | Developer commitments to the player and modding community |
| **[Content Architecture & DLC](content-and-dlc.md)** | Content tiers, localization, region overrides, load stack |
| **[Core Loop](core-loop.md)** | Clock system, economy, school calendar, five jobs, failure pipeline |
| **[Systems Overview](systems-overview.md)** | All major systems with brief descriptions and links |
| **[Story Overview](story-overview.md)** | Spoiler-light premise, tone, and setting |
| **[Title and Branding](title-and-branding.md)** | Title rationale, fraction logo, title screen art, evolving screens, chapter operator system |
| **[Art Direction](art-direction.md)** | Visual style, cel shader, colour palette, material system, outlines |
| **[Character Pipeline](character-pipeline.md)** | Body mesh architecture, hair system, clothing pipeline, Blender workflow |
| **[Audio Direction](audio-direction.md)** | Music, sound design intent, and reference |
| **[Boot Manager](boot-manager.md)** | ABM/MBM phased lifecycle, pak search order, dependency resolution |
| **[Platforms & Tech](platforms-and-tech.md)** | Engine, platform targets, build pipeline |
| **[Scope](scope.md)** | What is in Game 1 and what is not |
| **[Save System](save-system.md)** | Autosave, playthrough folders, per-save content tier, streamer mode |
| **[UI/UX](ui-ux.md)** | HUD widgets, camera modes, smartphone menu hub, out-of-character menu |
| **[Test Modes](test-modes.md)** | Standalone job test modes and quest-loop adventure sandbox |
| **Accessibility** *(note)* | Art guideline: avoid red adjacent to green; full accessibility design is out of scope for the initial release |

---

## Game At a Glance

| Field | Value |
|-------|-------|
| **Title** | Ordinary Girl Meghan / Magical Girl Ava |
| **Short title** | MGA |
| **Genre** | Action RPG / Adventure |
| **Tone** | Story-heavy, action-biased; not souls-like |
| **Setting** | Colony planet Terridyn, ~300 years in the future |
| **Audience** | Adults (18+); all characters explicitly over 18 |
| **Platforms** | Windows (primary), Linux (secondary), Mac (aspirational) |
| **Engine** | Unreal Engine |
| **Player count** | Single player |
| **Monetisation** | Buy once; no in-game purchases |
| **Content** | Modular DLC tiers — Base / Spicy / Super Spicy |
