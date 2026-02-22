# Script — Video 02: Walking Through a Real GDD — MGA

**Target runtime:** ~25–30 minutes
**Audience:** Solo developers and indie teams; viewers of Video 01
**Format:** Screen share walkthrough with talking head segments
**Prerequisite:** Video 01 recommended but not required

---

## [00:00 — 01:30] Introduction

If you watched the first video, you know what a GDD is and how to write one. This video is the proof of concept. We are going to walk through the actual Game Design Document for my game — Ordinary Girl Meghan, Magical Girl Ava, MGA for short — section by section. I will show you what each document contains and explain why I wrote it the way I did.

MGA is an adult action RPG set on a colony world three centuries from now. You play Meghan, a retired magical girl trying to live an ordinary life. The skills she builds working, studying, and paying rent are the same skills she uses when the world stops letting her stay ordinary. One progression system. One character. The game is in development — this GDD is a working document, not a finished one.

[VISUAL: Open the GDD index — gdd.md]

This is the top level of the GDD. Every section is here. Some link to complete documents; a few are still marked forthcoming and we will talk about what that means when we get to them. Let's go through it.

---

## [01:30 — 04:30] The Razor and Elevator Pitch

[VISUAL: gdd.md — Razor section]

Everything starts here. This is the Razor for MGA:

*A skill-unified action RPG where the skills Meghan builds as an ordinary person become the tools she uses as a magical girl — because she was always one person, not two.*

I covered the Razor concept in the first video. What I want to show you here is how it actually functions as a decision-making tool.

MGA has a life-sim layer and an action layer. The temptation in any game like this is to treat them as two separate experiences that happen to share a save file — a Stardew Valley half and a Devil May Cry half that don't interact. The Razor kills that option. If I ever design a feature that requires Meghan to have a separate "combat mode" with different rules, the Razor says no. Every skill she builds waitressing or delivering packages has to also matter when she is fighting. That constraint is not a limitation — it is what makes the game coherent.

The Razor changed twice before I settled on this wording. The first version was too vague. The second was too long. This one is the minimum clear statement of what makes MGA MGA.

[VISUAL: Elevator pitch paragraph]

Below the Razor is the elevator pitch — four sentences that a stranger could read and understand the game. Setting, protagonist, premise, the hook. This is not marketing copy; it is a reference tool. When anyone asks what the game is, I read them this paragraph and they have enough to understand the conversation we are about to have.

---

## [04:30 — 08:00] The Manifesto

[VISUAL: manifesto.md]

The Manifesto is separate from the GDD proper but lives alongside it. It is not a description of the game — it is a description of how I am making it. Commitments to the player and to the modding community.

A few that are worth calling out because they are design constraints, not just philosophy.

Player agency is never removed. The player's controls will not be locked out for more than five to ten seconds under any circumstances. That is a hard constraint that affects how I design every cinematic, every dialogue sequence, every story beat. If I want to tell a particular story moment, I have to do it without taking control away from the player for more than ten seconds. That is a difficult constraint. It produces better design because of the difficulty.

Failure has consequences, not game over screens. This one shaped the entire failure pipeline system — the black market holding mechanic where Meghan enters a trafficking pipeline if she fails a mission instead of hitting a game over screen. That system exists because the manifesto says failure should produce content, not dead ends.

No in-game purchases. One line. Non-negotiable.

The manifesto is the document that tells you what kind of developer I am committing to be. If I violate it, I am lying — to the player, to myself. That accountability is part of why it's worth writing.

---

## [08:00 — 11:30] Core Loop

[VISUAL: core-loop.md]

The core loop is the practical heart of the game. Not the story — what does the player actually do?

[VISUAL: The Fantasy section]

I start with "The Fantasy" — a paragraph that describes the emotional experience of playing, not the mechanics. The player is Meghan, a person trying to hold an ordinary life together in a city that keeps making that harder. The fantasy is competence under pressure. That framing tells me what the mechanics need to deliver emotionally, not just functionally.

[VISUAL: The 24-hour clock section]

The 24-hour clock is the central organising system. Time advances proportionally to actions — study for an hour, an hour passes. There are no turn-based action slots. This creates natural time pressure without hard walls. Meghan's CP — Consciousness Pool — drains over the day and only recovers through sleep. If it hits zero, she is out for six hours. That is a real cost with real consequences, not a game over.

[VISUAL: Economy table]

The economy section lists every ongoing expense — rent, food, tuition, clothing replacement, debt repayments. All of these are real, recurring, and non-optional. The financial pressure is not abstract. Every day Meghan does not earn money is a day she is falling behind.

[VISUAL: Five jobs section]

Five jobs cover the income side. Each trains a distinct skill cluster. The waitress job trains social skills. The courier trains traversal. The mechanic trains engineering and hacking. The hacker trains intelligence skills under pressure. The bounty hunter trains the full combat and infiltration toolkit. A player who focuses on one job will arrive at the game's later chapters with a different set of tools than one who spreads across all five. Both are valid builds.

Notice what connects all of this: every skill built in any job is the same skill used in combat and story interactions. The Razor holds throughout. There is no mechanical separation.

---

## [11:30 — 14:30] Art Direction

[VISUAL: art-direction.md]

Art direction is one of the most developed documents in the GDD because the visual system is highly specific and technical.

[VISUAL: Visual Style section]

The style is anime and manga. Clean topology, stylised proportions, cel shading throughout. The guiding principle is that the look is achieved in the render pass, not after it — no post-processing where a geometry or shader solution exists. That keeps overhead low and the frame budget predictable.

[VISUAL: Colour palette section]

64 colours. That is the entire palette for the game. Every character, every environment, every UI element draws from these 64 entries. The palette is intentionally bright and cheerful — this was a deliberate decision. Terridyn is a dystopian corporate world, half-empty after a plague. The art refuses to reflect that darkness back at the player. The world looks like a place where good things can happen, because they can. That visual tone has to be established before certain story moments land the way they need to.

[VISUAL: Material system section]

The material system is the most technically interesting part of the art direction. Textures in MGA are not used as direct colour maps. They are used as a double index into the palette. Each material instance has 30 colour slots for a primary colour and 30 for a secondary colour. The texture tells the shader which slot applies to each pixel region, and what mix ratio to use between them.

The practical result: I can recolour any asset entirely by changing material parameters. No texture repaint required. A costume in a different colour, an NPC skin variant, a faction-coloured prop — all of these are parameter changes. That is a significant time saving across a game with a large cast and a wide variety of environments.

[VISUAL: Asset Sourcing section]

I am not modelling every asset from scratch. I am using purchased 3D assets from Humble Bundle deals and free assets from the Epic Games Store, stripping their original materials, and applying the custom material system on top. The anime aesthetic is achieved at the shader layer, not the model layer. A clean mesh reads correctly under cel shading regardless of where it came from.

---

## [14:30 — 17:00] Audio Direction and Save System

[VISUAL: audio-direction.md]

The audio direction documents three musical pillars.

Rock and metal for combat — with a distinction between Meghan fighting as an ordinary person and Ava fighting as a magical girl. The music earns the transformation. When she transforms, the ceiling goes higher. The biggest moments hit symphonic metal — full orchestra running alongside full band simultaneously.

Orchestral for home, emotional, and stealth sequences. Chamber-scale at home, full orchestra for the emotional peaks that earn it.

Electronic and synthwave for city exploration and corporate environments. Terridyn is a sci-fi ghost city. It should sound like one.

Burton's Bar — the bar where Meghan works — gets Irish and Celtic pub music. Fiddles, bodhrán, the sound of a place that has been a bar for a long time.

[VISUAL: Voice Acting section]

Voice overs will be AI-generated for development and launch. I have a large script and I am one person. The AI VO is not a placeholder I am apologising for — it ships with the game. The replacement plan, if budget allows post-launch, is full human cast once the script is locked. The AI performances serve as casting reference; no additional direction documents are needed at that point.

The key is that every line is written with emotional context — the character's internal state, the intent of the delivery. That document is the input to the AI pipeline, the handover to fan localizers, and the director's brief for human actors if we ever get there. One document, three uses.

[VISUAL: save-system.md]

The save system has two interesting design decisions.

The autosave slider gives players control over frequency — from off to thirty minutes. That is a manifesto-level respect for player time: nobody is forced into a save interval they did not choose.

Per-playthrough content tier is the more significant one. Each playthrough folder has its own content tier configuration. This solves the streamer problem cleanly. A streamer can have one playthrough set to base-game content for broadcast and a separate playthrough for offline at their preferred tier. No global mode switch. No risk of accidentally streaming the wrong content.

The first-boot streamer dialog and the optional OBS and Streamlabs detection round this out — the game will warn a streamer if it detects streaming software running while they are in spicy or super spicy content.

---

## [17:00 — 20:30] UI/UX

[VISUAL: ui-ux.md]

The UI system is one of the most distinctive design choices in MGA, and it is worth spending some time on because it illustrates how design decisions that pass the Razor compound on each other.

[VISUAL: Philosophy section]

The UI is diegetic. Everything the player sees during gameplay is something Meghan actually sees — her cybernetic eye implants displaying AR overlays driven by her smartphone. The information available to the player is the information available to Meghan.

[VISUAL: Camera modes table]

The game has two camera modes. First-person is Meghan's eyes — the AR overlay is driven by widgets loaded from her phone into her eye implants. Third-person is the CAMERA drone — a separate physical drone that orbits Meghan and transmits its feed back to her via laser, received by her eye optics.

[VISUAL: Widget system section]

Widgets are modular HUD elements. The player selects which widgets to run from a licensed set, arranged freely on screen. The number of concurrent widgets is determined by Meghan's phone tier — the basic phone financed by Terry in Chapter 1 runs three widgets. Better phones run more. Widget availability is also gated by skill rank: you cannot run a vision cone overlay if Meghan's Perception is not high enough to make sense of the data.

This passes the Razor. The HUD is not a game layer on top of Meghan — it is Meghan's actual capability. A player who has invested in skills and hardware sees more. A player who has not sees less.

[VISUAL: Drone physical operation section]

The drone control mechanism is worth highlighting. Meghan controls the drone's position at a thought via a cybernetics-linked transmitter. The drone returns its video feed via laser — the laser bounces off surfaces in Meghan's field of vision to reach her eye receiver.

If Meghan is blindfolded, the laser cannot reach the receiver. The drone falls back to a radio channel with much lower bandwidth — the player gets a narrow view of just Meghan's immediate surroundings. They can see her profile, her outfit, but not the area around her.

That is not just a technical detail. It is a gameplay mechanic. In capture sequences, in pipeline failure states, the player is as blind as Meghan. The information they lose is exactly the information she loses. That is the Razor working at the UI layer.

[VISUAL: Smartphone as menu hub section]

The phone is also the menu system. Map, skills, inventory, missions, contacts — all accessed through the phone, in real time, without pausing the game. If Meghan loses her phone, she loses access to all of it. The only always-available options are save, load, game settings, and exit — the things that exist outside the game world.

---

## [20:30 — 23:30] Scope, Platforms, and Systems Overview

[VISUAL: scope.md]

The scope document is the one that takes courage to write, because it requires you to say out loud what you are not making.

[VISUAL: Story scope table]

Game 1 is Chapters 1 through 6, the epilogue, New Game Plus, and New Game Plus Plus. Chapters 7 and 8 are the sequel. That line is in the document. It is not ambiguous. It is not "maybe if we have time." It is not in Game 1.

[VISUAL: System scope table]

All the core systems are in scope. The one tentative item is the sports tournament in Chapter 4 — planned, but subject to cut if development timeline requires it. Marking something as tentative is not failure. It is honesty about what might have to give.

[VISUAL: Platform scope table]

Windows confirmed. Linux confirmed — native build, not a compatibility layer. Mac is aspirational: the target is a clean recompile via Unreal Engine's native Mac support with no external library dependencies. Not guaranteed, but not ruled out. If the build is clean, Mac ships. Steam Deck is a test platform — evaluated if the hardware is available.

[VISUAL: platforms-and-tech.md — Engine section]

A quick note on tech: Unreal Engine, Nanite enabled for all imported assets as a standard step, no path-traced lighting — the cel shader pipeline replaces it. The build pipeline runs through Jenkins. Every release build is produced by the build machine, not my local dev environment. That is a manifesto commitment.

[VISUAL: systems-overview.md]

The systems overview is the index of every major system in the game — one paragraph each with links to detail documents where they exist. This is the navigation tool. If you need to find a specific system, this is where you start.

---

## [23:30 — 26:00] What's Still Forthcoming — and That's Fine

[VISUAL: gdd.md — full index]

If you look at the full GDD index, you will see that several system detail documents are still marked forthcoming. The attribute system. The combat system. The relationship system. Others.

This is not a problem. Those systems exist in private design documentation that is not yet ready for the public GDD. They will move here as they are finalised. The GDD index is the promise; the documents are the fulfilment of that promise over time.

What matters is that the top level is complete. Anyone looking at this GDD can understand what the game is, how it works at a high level, what is in scope, what it looks like, and what it sounds like. The detail documents fill in underneath that as the game develops.

There is also a separate private repository. That is where the full plot lives — the complete story with all its spoilers, the character truth, the design of the mature content tiers. None of that belongs in a public GDD. The separation of public and private documentation is something I would recommend to any developer who has IP worth protecting.

---

## [26:00 — 27:00] Wrap-Up

That is the MGA GDD. It was built across multiple sessions, section by section, as design decisions were made. It is not a document I wrote before I started designing — it is a document that grew alongside the design.

The lesson I want you to take from this walkthrough is not "your GDD should look like mine." Every game is different and every GDD should reflect its game. The lesson is that the GDD is always reachable. You start with the Razor. You add the pitch. You add the core loop. Everything else is added as you make decisions, not before you make them.

If you found this useful, the first video covers the concepts in more depth — what a GDD is, why you need one as a solo developer, and how to start writing yours from a blank page.

Thanks for watching.

---

*[END OF SCRIPT]*

---

## Production Notes

- Runtime estimate: 25–30 minutes at a natural speaking pace
- This is a screen share video throughout — have the GDD open and navigate between documents as described
- Each [VISUAL] cue is a transition to that document or section
- The widget system and drone laser sections benefit from a simple diagram if one is available
- Recommend cutting between the talking head and the screen share at natural section breaks
- The Razor section is the emotional core of both videos — give it time
