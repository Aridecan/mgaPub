# MGA Dev Report — February 2026

*Ordinary Girl Meghan / Magical Girl Ava*

---

## Back From the Dead (Sort Of)

It's been a while since I've posted a proper update, so let me catch everyone up. The short version: MGA is very much alive, and I've made more documented progress in the last two weeks than in the previous several months combined.

The catalyst was discovering **Claude Code** — the terminal-based version of Anthropic's AI assistant. If you've ever tried to use a chatbot for serious game design work, you know the pain: every session starts from zero, you spend half your time re-explaining your own project, the AI forgets critical details you told it twenty minutes ago, and you end up going in circles. Claude Code changed that completely. It works directly with my file system, reads my existing documents, remembers context across the project through persistent files, and — critically — it stops me from having to be the bottleneck between what's in my head and what's on the page. I can brain-dump for an hour and walk away with properly structured, cross-referenced documentation instead of a chat log I'll never look at again. It's been a genuine force multiplier.

---

## The Documentation Blitz

Over roughly ten working sessions between February 19th and 26th, I went from scattered notes and ChatGPT conversation logs to a comprehensive, structured project across three repositories:

### Public Repo (mgaPub) — Game Design Document

The full GDD is now written and organized. This covers:

- **The Razor** — the one-sentence design filter: *"A skill-unified action RPG where the skills Meghan builds as an ordinary person become the tools she uses as a magical girl — because she was always one person, not two."* Every feature decision runs through this.
- **Manifesto** — 13 developer commitments covering player respect, systems-first design, no in-game purchases, mod-friendly architecture, and honest physical asymmetry in the game world.
- **Core Loop** — moment-to-moment, session-level, and macro gameplay documented. Five jobs (Waitress, Courier, Mechanic, Hacker, Bounty Hunter), school calendar pressure, economy, and failure pipeline.
- **Art Direction** — Modern Anime Stylized (Genshin/Blue Protocol/Honkai reference). 4-tone cel shader, 98-entry colour palette (64 base chromatic + 6 signature + 8 flesh + 8 greyscale + 12 blush), double-index palette material system, inverse hull outlines.
- **Character Pipeline** — single female body mesh shared across all characters, 2AM skull-cap hair system, Chaos Cloth physics, Marvelous Designer clothing pipeline. 16 front hair styles x 10 back styles = 100+ NPC combinations at launch.
- **Audio Direction** — three musical pillars (Rock/Metal for combat, Orchestral for emotion, Electronic/Synthwave for the world). AI voice-over at launch with human VA replacement planned post-feature-complete.
- **Systems Overview** — 12 attributes, 50+ skills trained through use (no levels, no feats), four resource pools (HP, CP, SP, Mana), real-time 24-hour clock, faction reputation, CAMERA drone system.
- **Content & Distribution** — three content tiers (Base, Spicy, Super Spicy), independent text/voice localization packages, custom boot manager (ABM/MBM), save system with per-playthrough content config and streamer mode.
- **Platforms & Tech** — Windows primary, Linux native secondary, Mac aspirational. Jenkins-automated builds. Demo on itch.io (Chapter 1 + 3 jobs), full game on Steam.
- **Test Modes** — four job test modes plus a standalone adventure sandbox for validating clothing destruction, quest systems, and enemy AI in isolation.

### Creative Briefs (12 completed)

Each creative brief breaks down a player-facing system into concrete use cases:

- **CB-Combat** — 7 use cases. Unified action system, 21 skeleton-based body regions, front/back hit determination via dot product, clothing damage cascade from outermost layer inward.
- **CB-Clothing** — 11 use cases. Two degradation tracks (combat + laundering), repair ledger with retroactive improvement, wearability thresholds, cloth type x damage type bypass matrix.
- **CB-Inventory** — 6 use cases. Physically modelled (dimensions, volume, weight). Containers have capacity constraints. Carry limit = STR x 2.5 kg. Magical girl uniform pocket dimension for phone storage.
- **CB-Camera** — 7 use cases. CAMERA drone provides third-person from game start. Battery management, Xtalics upgrades, team drone building, party control.
- **CB-Jobs** — 7 use cases. Waitress shifts with memory-based order taking, courier vehicle progression, mechanic DRM bypass via Hacking, bounty hunting with capture consequences.
- **CB-HUD Modification** — widget point budget system, dual-cap (phone + CAMERA hardware), widget porting from eye AR to drone, named profiles.
- **CB-Skills Menu, Main Menu, Settings Menu, In-Game Menus, Mod Manager** — all documented with use cases and open items tracked.

### Private Repo (mgaPriv) — Full Lore Bible

This is the spoiler-heavy repository. **175 markdown files** covering:

- **7 Chapter Files** — Prologue through Chapter 6. Full narrative documentation from Meghan's arrival on Terridyn through the climax.
- **13 Character Files** — Deep profiles for Meghan/Ava, Celeste, Daisy, Jennifer, Milo, Elisa and Elena Kael, Alexander, Trevor, Imara, and additional key characters. Birthdays, blood types, zodiac signs, and thematic symbolism documented.

<details>
<summary>⚠️ Story Spoiler — Full character file list</summary>

The additional characters are Tierney, Tabithia Brown, Teressa Straton, and Terry Straton.

</details>
- **21 Corporation Files** — All Big 20 megacorps plus TMC. Each with leadership, industry, Terridyn operations, and strategic role.
- **20 World/Setting Files** — Terridyn planet and colony history, city layout (2 km x 2 km downtown core, 4 km derelict outer belt, river division, two malls), the three human subspecies (Normal Humans, Resource-Optimized Humans, Heavy Worlders) and their origins, Hell structure, mana aspects, Calibran homeworld, the Alliance and political systems, the Exodus and how it depopulated the colony, weapons technology, holidays, the 400-day T3PC calendar, and cultural details like the Gospel of Prosperity.
- **5 Hell Faction Files** — Demon species (8 types by sin alignment), canine demons, lust demons, wrath demons, Hell Council.
- **~85 Mechanics Files** — Attributes, combat system, 50+ individual skill articles, magical girl uniforms, weapons, elemental magic, crystal system, STALKER registry, capture/restraint, resource pools, inhibition.
- **7 Plot Structure Files** — Chapter overview, master plan, epilogue/NG+/NG++, rivalry arc, story structure critique with Hero's Journey mapping.
- **3 Education Files** — MCI (Minerva Collegiate Institute) and TMS (Terridyn Mining School) with enrollment demographics.

### Meta Repo — Almanac Tool

A C++23 library and CLI tool that handles the game's calendar system (400-day year, 12 months), date conversions between Gregorian/Interstellar/T3PC formats, and will eventually provide orbital data for skybox rendering. 15/15 tests passing. Several bugs fixed (month calculation, Linux compatibility, holiday date collisions).

### Dev Video Scripts

Two tutorial video scripts written (~20-30 min each): one on what a GDD actually is and why it matters for solo devs, and one walking through MGA's GDD as a worked example. A third script on design document decomposition (creative briefs vs. feature briefs vs. technical briefs) is outlined.

---

## Hands-On Work

Documentation isn't the only thing that's been happening:

- **Character Mesh** — I've been working on the main character body mesh, and I think I finally have something I'm happy with. The pipeline calls for a single female mesh shared across all characters, varied by uniform XYZ scale for height and a breast shape key. Getting the base right is critical since every character in the game derives from it.
- **Hair** — I've started on the hair system using the 2AM skull-cap method in Blender. It's coming along, but it still needs a lot of work. The front signature styles need to read clearly at gameplay distance, and getting Chaos Cloth to behave on the back hair modules is its own adventure. This is going to be an ongoing battle for a while.
- **City Terrain** — The basic 8 km x 8 km terrain is laid out with the waterway mapped. I've drawn out the base grid for the city. On the tech side, the city uses Unreal's World Partition system set up at 500 m increments, with two partitions loaded around the player at all times. The city streets are designed with a maximum turn of 580 m at 12 degrees — which means you should never see buildings pop in and out as partitions stream in the background. The geometry of the city works with the streaming, not against it.
- **Relearning Unreal Engine** — I'm back in the engine and working through the rust. It's been a while since I was deep in UE, so I'm rebuilding that muscle memory — materials, blueprints, the animation system, all of it. The good news is the documentation work means I know exactly what I'm building toward.
- **Game AI Research** — I've gone deep into learning about different types of AI for game design. Enemy AI is one of the identified gaps in the GDD, and I want to make informed decisions about navigation (nav mesh vs. nav graph), behaviour trees vs. utility AI vs. GOAP, perception systems, and how to make enemies feel smart without being unfair. This is still research phase, but it's shaping how I'll approach the enemy AI design document.

---

## What's Next

The GDD audit identified several gaps that still need design documents:

- Enemy AI design
- Dialogue system
- Difficulty framework
- Tutorial design
- Cutscene system
- Combat feedback / juice
- Animation architecture

On the art side, the hair system is the immediate priority, followed by getting a basic clothing pipeline working in-engine.

---

## By the Numbers

- **175+** markdown files across the lore bible
- **20+** GDD articles in the public repo
- **12** creative briefs with use cases
- **50+** individual skill articles
- **21** corporation profiles
- **13** character profiles
- **7** chapter narratives (6 + prologue)
- **98** colours in the palette
- **15/15** almanac tests passing
- **10** working sessions over ~8 days

The project has gone from scattered notes and circular AI conversations to a structured, cross-referenced, version-controlled design bible. There's still a mountain of work ahead — but for the first time in a while, the mountain has a map.

---

*MGA is a solo-developed adult action RPG currently in pre-production. Follow along for more updates.*
