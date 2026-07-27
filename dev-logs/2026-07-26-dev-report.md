# MGA Dev Report — July 2026

*Ordinary Girl Meghan / Magical Girl Ava*

---

## Two Pillars

June ended with a city that builds itself. July stopped building the world and started building the *game*.

The month got a name — **CoreX-M1** — borrowed from the EA design flow: *prove out the X*. Before you make content, you de-risk the core technologies a viable game needs, using placeholders instead of art. Not "make the first level." Make the machinery the first level will one day sit on, and prove it works.

Two pillars. One you can see: the **front end** — the thing that happens between double-clicking the icon and standing in the world. One you can't: the **pipeline** that turns a source tree into something a player can install. July built both, and the honest way to tell the month is as two parallel stories.

By the end of it, MGA boots from a desktop shortcut, shows a splash, asks your age, tells you what you're about to see, opens a main menu, lets you set your resolution and your volume levels and your key bindings and your language, remembers all of it, and quits cleanly. None of that is gameplay. All of it is the difference between a project and a product.

---

## Pillar A — The Front End

### The boot sequence, visible end to end

The boot flow is a state machine (B0 through B8), and the rule this month was that **every stage of it gets a visible screen** — a coloured panel with the stage's name on it — so you can always tell where you are and, more usefully, where it stopped. Placeholders on purpose: zero art files, engine-drawn, replaced later.

It runs: pre-engine splash → age gate → legal and content warnings → activation progress → content advisory → main menu.

That first step is stranger than it sounds. The splash has to draw **before the engine exists** — before UObjects, before Blueprints, before almost everything. It's raw Slate, registered from a C++ module, and it cost two genuinely obscure lessons. The module has to register at exactly the right loading phase (register too early, as everything sensible suggests, and the screen manager hasn't been created yet, so you get a null pointer). And the manager calls `OnPlay()` and then asks for your widget, but **never calls `Init()`** — so if you build your widget in `Init` like every example implies, you get a perfect, silent black screen.

### Settings, six tabs deep

The settings menu went from nothing to six working tabs: **Language, Game Play, Video, Audio, Controls, Sensitive Content.**

**Video** does resolution, window mode, VSync, frame limit and quality scaling, backed by a subclass of Unreal's own settings object — plus the unglamorous correctness work that separates "it has a resolution dropdown" from "it works": preselecting the current resolution, clamping windowed sizes to the actual monitor work area so the title bar can't end up off-screen, and making the packaged build DPI-aware.

**Audio** is eight channels, and the split is a design statement rather than a technical one: Master, **out-of-fiction music** and **in-fiction music** (the bar jukebox, the boombox on the street corner — the music that exists inside the story), character dialogue, ambient dialogue, the player's own sound effects (footsteps, breathing), ambient effects, and interface. Anyone who has tried to hear a conversation over a nightclub will understand why those are separate sliders.

### Controls: rebind everything

A full Enhanced Input rebinding screen, which turned out to be the single biggest chunk of the month: **52 canonical actions** in a fixed, human-sensible order, click-to-capture ("press any key…"), separate keyboard and gamepad slots, per-cell clear buttons, a settings-wide reset to defaults, and live conflict detection that refuses a binding already in use.

Conflict detection is where the interesting bug lived. First-person bindings were blocking third-person bindings — the validator had only *one* axis of exclusion, so it couldn't express "these two contexts never run at the same time, let them share a key." Fixing it meant teaching the system about mutually exclusive control contexts, which is a better system than the one that was there before the bug showed up.

### Language, and a template for someone else's work

Language got a per-module table: one row per content module, a **Text** column and a **Voice** column, each a dropdown, each able to be set to None. Source text is English (Canada); everything else is a swappable content pack.

The naming convention is the point: `LocText_<Tier>_<Language>` / `LocVoice_<Tier>_<Language>`. A community translator clones one of those, fills it in, and ships it — no engine access, no source, no permission needed. That's the north star for localization here, and this month is when the plumbing to support it got built rather than just described. (The packs themselves are still empty skeletons; filling them is next month's problem.)

### Streamer safety, engineered rather than promised

MGA is an adult game, and adult content is a content *tier* — separately installed, separately cooked, not present unless you add it. That structure made a related decision easy to state precisely:

**The base game's outfits meet the Twitch VTuber minimum-cover standard.** Not "we'll be careful" — an actual published third-party guideline, used as the spec. The base game is stream-safe by construction, which means Streamer Mode isn't a filter bolted on afterwards; it's the default state of the shipped product. On top of that: a **Sensitive Content** settings tab where the adult toggles live, and a boot-time advisory that points recording and streaming users at it before they've broadcast anything they didn't intend to.

---

## Pillar B — The Pipeline

### From zero build jobs to a packaged game

On the 1st of July there were no build jobs at all. The project built on one machine, by hand.

By the 11th, a Jenkins pipeline (`mga-weekly`, running on a dedicated Windows build agent) was doing the whole thing unattended: sync from version control, build the C++, cook the content, package it, audit it, archive it. **First green end-to-end build: ~23 minutes**, producing an executable and pak files that installed and launched.

Getting there meant confronting an awkward truth: three of the project's most valuable plugins are **editor-only city-building tools**, and one of them ships with source that doesn't compile. They can't go in the shipped game and they don't need to. The pipeline now switches them off transiently — editing its own working copy of the project file, building, then restoring it — so the depot and the live editor are never touched. It's a small trick that unblocked everything downstream.

### Content tiers as real, separate downloads

The adult tiers aren't a flag in a config file. Each is a **GameFeature plugin cooked into its own pak**, which the game discovers, mounts and activates at runtime — and which simply isn't there if you didn't install it.

The test for whether that works is deliberately the smallest possible surface: the content advisory screen. The base game supplies a warning. Installing a tier registers an *override* that chains onto the base one. Installing the next tier chains again. So the number of lines on that one screen tells you, unambiguously, which tiers loaded and in what order — and it exercises exactly the mechanism the whole game will eventually need for tiered content, on a surface small enough to debug.

It works. Base alone gives one line, base plus one tier gives two, all three give three, and removing the middle tier correctly refuses to load the one that depends on it rather than half-loading it.

### Versioning and publishing

Builds are now stamped `0.1.0-cxm1.<build>` and published as separate 7z archives — one for the base game, one per content tier — to a release share, with a manifest recording sizes, hashes and the exact source revision.

The one-archive-per-component split is doing real work: it makes "did an adult tier ship in the Steam build?" a question about *which file was uploaded*, rather than a question about the contents of a 900 MB blob.

### The audit gate

Every release build now runs a gate that asserts no content-tier data is present in the base game's containers, and fails the build if any is. It was written after a leak that put two tier files into a base build — caught before release, fixed at the root, and now the sort of thing that can't recur silently.

Two details from that worth keeping: the leak came from the **cooker**, not the game (the cook process was loading tier data as a side effect of its own startup, which quietly pulled it into the build); and the gate has to inspect the modern container format directly, because the leaked files lived in the 870 MB data container while the legacy pak the gate originally read held only 11 MB of descriptors. A gate that checks the wrong file passes the build that ships the problem.

---

## Act Three — Story and Design

The first week of July, before the engine work took over, was spent on the script.

**A full-arc critique.** The whole story got read end to end against its own structure, and the most useful finding was an uncomfortable one: **the protagonist is too passive.** Things happen *to* Meghan and she reacts. Two independent read-throughs landed on it separately, which is usually a sign it's real.

The fix isn't to rewrite her into someone louder. It's a principle now written down: **solve it on the player-agency axis, not the protagonist-agency axis.** A quiet, reactive character is a legitimate and interesting protagonist — the problem is a *player* who has nothing to decide. Give the player meaningful choices inside Meghan's situation and the passivity reads as characterisation instead of a hole. Two related rules came out of the same pass: every chapter needs its own arc, not just a position in the larger one, and stakes described as cosmic have to be made concrete and put on a clock or they stop mattering.

The finale also got split into two chapters. It was doing too much work as one.

**The Chapter 1 economy.** The month's best design work went into the ordinary-life half of the game: a time-management loop with a long-term stamina budget, study time toward an entrance exam, and five jobs — three legal, two decidedly not.

The legal jobs are deliberately tuned to cover only about **75–90% of your monthly expenses.**

That number is the whole design. It isn't a difficulty knob; it's the coupling between the game's two worlds. A player who does everything correctly and legally still comes up a little short each month — and the shortfall, not a cutscene, is what makes the riskier work look reasonable. The normal world's demands push you toward the secret one. There's a satirical edge to it as well: the legal jobs enforce a hard 40-hour weekly cap, which is presented as a worker protection and functions in practice as a funnel toward the unregulated market.

---

## Housekeeping: knowing what we already own

One unglamorous but genuinely valuable exercise: a full audit of the asset libraries this project has accumulated. Store pages and launcher caches were parsed into a searchable inventory — **1,013 products on Fab, 269 from Cosmos, 537 archives on disk, 247 audio packs** — and then triaged into a "do we already own something for X?" digest organised by what MGA actually needs.

For a solo project the expensive mistake isn't buying an asset. It's spending a week building something you already had.

---

## By the Numbers

- **2** pillars in the milestone: front-end framework, build pipeline
- **6** distinct boot screens built — pre-engine splash, age gate, legal/warnings, activation progress, content advisory, main menu
- **6** settings tabs — Language, Game Play, Video, Audio, Controls, Sensitive Content
- **8** independent audio channels, including a diegetic/non-diegetic music split
- **52** rebindable actions, keyboard and gamepad, with conflict detection
- **51** source submissions in July (against 31 in June) — almost all engine and pipeline
- **0 → 1** continuous build pipeline: sync, build, cook, package, audit, archive
- **~23 min** for the first green end-to-end packaged build
- **3** separately cooked, separately installed content paks (base + two tiers)
- **4** install configurations verified automatically, including the negative test that a tier refuses to load without its dependency
- **1** release-versioning and publishing scheme, one archive per component
- **1** audit gate now standing between a content tier and the base build
- **1** protagonist-agency problem found, and a principle written down for fixing it
- **75–90%** — the share of monthly expenses the legal jobs cover, on purpose
- **1,013 / 269 / 537 / 247** — owned assets inventoried across Fab, Cosmos, disk and audio

---

## What's Next

- **Fill the loadables.** The content packs are proven as *plumbing* and empty as *content*. Next is a machine-translation pass over the string tables so the localization packs have something in them, and real placeholder content in the tiers.
- **Finish the front end.** Gamepad glyphs in the rebind rows, the analog sensitivity and dead-zone controls, and the main-menu buttons that should be visibly disabled until there's a save system behind them.
- **Two known bugs, still open.** The pre-engine splash renders a corrupted character at the end of each line — root-caused this week to a version-control encoding setting that silently mangles non-ASCII text *only on the build machine*, so it never reproduced locally. And the UI scale slider does nothing: also root-caused (it was driving the wrong scaling hook — the game viewport recomputes its own scale every frame and overrode it), fixed in source, not yet verified in a packaged build.
- **Then: gameplay.** The milestone was always a foundation, not a feature. Its exit condition is that the boring, load-bearing, invisible things are done — so that what comes after can be the game.

June's report ended with "the first city was built by hand; the second one is learning to build itself." July's ending is less romantic and possibly more important: **there is now a machine that turns this project into something a person can install and run.**

---

*MGA is a solo-developed adult action RPG currently in pre-production. Follow development at the [Project Tangent SubscribeStar](https://subscribestar.adult/aridecan).*
