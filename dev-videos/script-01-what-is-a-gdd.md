# Script — Video 01: What Is a GDD and Why Do You Need One?

**Target runtime:** ~20 minutes
**Audience:** Solo developers and small indie teams
**Format:** Talking head with screen share / document examples

---

## [00:00 — 01:30] Hook

You have a game idea. It's great. You know exactly what it is, you can see it, you can feel it. So you start building.

Six months later, you're staring at a folder full of half-finished systems and you cannot remember why you built half of them. The scope has doubled. The thing you're making doesn't quite match the thing you started making. And every time you sit down to work, you spend the first twenty minutes figuring out what you were doing last time.

This is not a skill problem. This is a documentation problem. And the fix is a Game Design Document.

I'm a solo developer. My game is called Ordinary Girl Meghan, Magical Girl Ava — MGA for short. It's a story-heavy adult action RPG with a complex plot, a large cast, and a skill system that has to hold together across six chapters and a sequel. Without a GDD I would have lost the thread inside the first month.

In this video I'm going to tell you what a GDD actually is — and more importantly, what it isn't — why it matters more for solo developers than for anyone else, and how to write one that will actually help you. We'll use MGA as a worked example throughout. The next video is a full walkthrough of that GDD if you want to see all of this applied to a real project.

Let's start with the most common misconception.

---

## [01:30 — 03:00] What a GDD Is Not

A Game Design Document is not a pitch deck. It is not a business plan. It is not a 400-page specification that defines every mechanic to five decimal places before you write a single line of code.

If you have seen GDDs from large studios — the Diablo design document from 1996 is the famous example that gets passed around — and thought "I need to produce something like that before I can start," stop. That document was written by a team, for a team. Its job was communication across twenty people. Your GDD has a different job.

Your GDD is a reference document. It is the map of your game. When you are six months in and you can't remember why a system works the way it does, you open the GDD. When you have to make a decision and you're not sure if it fits the game you're making, you open the GDD. When you bring in a collaborator — an artist, a composer, a voice actor — and they need to understand your vision quickly, you hand them the GDD.

It should be the single place where the answers live. Not your head. Not a pile of Discord messages. Not three different Notion pages that contradict each other. One document.

And critically — it is a living document. It is never finished. It grows as your game grows. Decisions get made; the GDD gets updated. Systems change; the GDD reflects the change. You are not writing something to be printed and filed. You are writing something to be used.

---

## [03:00 — 05:00] Why It Matters More for Solo Devs

When you have a team, the GDD is a communication tool. It keeps twenty people pointed in the same direction.

When you are one person, it serves a different and arguably more important function: it is the external memory for a project that will take years.

Solo development is long. A serious indie game takes three to eight years. In that time, you will step away from the project. Life happens. You will come back after two weeks off and not remember what you were in the middle of. You will spend a month on something, ship it, and six months later have no idea why you made the choices you made. Without documentation, every return to the project starts with archaeology.

There is a second problem unique to solo development: scope. When you are one person with a great idea, there is nothing stopping you from adding features. Nobody to say no. Nobody to ask "does this fit?" You add one thing, then another, then another, and three years in you have a project that cannot be finished by one person in one lifetime.

The GDD is the thing that says no. Specifically, the thing I call the Razor — and we will get to that in a moment — is the tool that kills features before they become expensive mistakes. It does the job that a producer or a lead designer would do on a larger team. For a solo developer it is not optional. It is survival.

---

## [05:00 — 07:30] The Razor

The most important thing in your GDD is the first thing you should write, and it is the shortest thing in the entire document.

The Razor is one to three sentences that describe what your game fundamentally is. Not the plot. Not the feature list. The thing that makes this game this game and not something else. It is the filter you apply to every feature decision. If a feature fits the Razor, it belongs. If it doesn't, it gets cut, regardless of how good an idea it is.

Here is the Razor for MGA:

*A skill-unified action RPG where the skills Meghan builds as an ordinary person become the tools she uses as a magical girl — because she was always one person, not two.*

That sentence does a lot of work. It tells you that there is one progression system, not two. It tells you that the life-sim half and the action half are not separate modes — they are the same Meghan. And it gives you a test: if a proposed feature requires Meghan to operate as two different characters with two different systems, it fails the Razor and it does not go in the game.

[VISUAL: Show the Razor sentence on screen]

The Razor saved me from several features I would have built and then had to cut later. Every time I thought "what if there was a separate combat skill tree" — Razor. No. Every time I thought "what if transformation gave her access to abilities she couldn't otherwise build" — Razor. No. She is one person. The systems are unified.

Write your Razor before you write anything else. It does not have to be perfect on the first draft. Mine changed twice before I was happy with it. But the act of writing it forces you to understand what your game actually is — and that understanding is the foundation everything else sits on.

A useful test: if you cannot explain your game's core identity in three sentences, you do not yet understand it well enough to build it. The Razor is not a marketing exercise. It is a thinking exercise. The output is clarity.

---

## [07:30 — 10:30] What Sections to Include

Once you have the Razor, you build the GDD around it. Here are the sections that I think every GDD should have, roughly in the order you should write them.

**The Razor and Elevator Pitch.** The Razor you already know. The elevator pitch is a paragraph — four to six sentences — that a stranger could read and understand what your game is. Setting, tone, who you play, why it's interesting.

**Core Loop.** What does the player actually do? Not the story — the verbs. What are the actions available to the player, how do they connect to each other, and what is the pressure that drives them to keep playing? For MGA this is the 24-hour clock, the economy, the school calendar, and the five jobs. These are the systems that fill a session. If you don't know what your core loop is, you don't know what your game is.

**Art Direction.** What does your game look like? This is more than "I want it to look like Zelda." It is the specific choices that produce your visual style — the rendering approach, the colour palette, the character design principles. Art direction documented early prevents expensive visual inconsistency later. It is also what you hand to every artist you work with.

**Audio Direction.** What does your game sound like? Music style by gameplay context — not one answer but several. Combat sounds different from exploration sounds different from emotional story moments. Document them separately.

**Systems Overview.** A one-paragraph description of every major system in the game, with links to detail documents where they exist. This is the index that lets anyone — including future you — find what they need quickly.

**Story Overview.** Spoiler-light. The premise, tone, setting, and cast at a level that communicates what kind of story you are telling without giving away every beat. This is the document you share publicly. Your full plot document is private.

**Platforms and Tech.** What are you building on, what are you building for, and what are the minimum specifications? Nail this down early. Platform decisions affect everything from rendering choices to control schemes to distribution strategy.

**Scope.** What is in the game and what is not. This is the hardest document to write because it requires you to make explicit cuts. But an unscoped project is a project that never ships. Be specific. "Chapters one through six are in scope. Chapters seven and eight are the sequel." Say the things out loud.

**Additional feature documents** as needed — save system, UI and UX, specific mechanics. These grow as the game develops.

---

## [10:30 — 13:30] How to Start Writing Yours

The most common reason people don't write a GDD is that they don't know how to start. The blank page is real. Here is the sequence that works.

**Step one: Write the Razor.** One to three sentences. Do not worry about whether it's good. Write your best current answer. You will revise it.

**Step two: Write the elevator pitch.** Four to six sentences. Setting, protagonist, what the player does, what makes it interesting.

**Step three: Write the core loop.** Not a feature list — the verbs. What does the player do moment to moment? What is the pressure that makes them keep playing? What does a typical session look like?

If you can write those three things, you have the nucleus of a GDD. Everything else can be added progressively as you develop the game and make decisions.

Do not wait until you have designed everything to start writing. The GDD is not the output of your design process — it is part of the design process. Writing forces thinking. The act of trying to describe your game in clear language will reveal things you haven't figured out yet. Those become open items — a list of questions you haven't answered. Open items are good. They are an honest record of what you still need to decide.

On format: I use Markdown files in a git repository. Everything is plain text, everything is version-controlled, and I can see every change I ever made to any document. If something breaks — a decision I made in month two that turned out to be wrong by month twelve — I can see when it changed and why. This matters on a long project. Use whatever tool you will actually maintain, but version control of some kind is worth the setup cost.

For MGA I have two repositories. A public one with design documents and the GDD — the things I am comfortable sharing. And a private one with the full plot, character truth, and story spoilers. If you are making something with IP worth protecting, this separation is valuable from day one.

---

## [13:30 — 16:00] Common Mistakes

**Writing too much too soon.** A GDD written before you understand your game will be wrong. A fifty-page specification written in month one will be obsolete by month six. Start with the minimum — Razor, pitch, core loop — and expand as decisions are actually made.

**Never updating it.** A GDD that doesn't reflect the current state of the game is worse than no GDD at all, because it gives you false confidence. Every time a major decision is made, the GDD gets updated. This is not optional. It is the discipline that makes the document useful.

**Confusing the GDD with the game.** The GDD describes the game. It is not the game. Do not spend three months perfecting the GDD before building anything. Build, learn, update the document, build more. The GDD and the game develop together.

**Leaving open items unresolved forever.** Open items are healthy. An open item that's been sitting for two years is a decision you are avoiding. Sometimes you are avoiding it because you don't know the answer yet — that is fine. Sometimes you are avoiding it because the answer is uncomfortable. Find those and deal with them.

**Scope creep in the GDD itself.** The Razor is the guard against this, but the Razor only works if you apply it. Every time you add something to the GDD, ask whether it fits. A document that has grown to contain every good idea you ever had is not a GDD — it is a wishlist. Wishlists do not ship.

---

## [16:00 — 18:30] Tools and Workflow

I want to briefly cover the practical side of how to maintain a GDD.

Plain text and version control are your friends. Markdown is readable, writable in any editor, and survives indefinitely without software rot. A Word document from 2018 might open with formatting corruption. A Markdown file from 2018 is just text.

Git gives you a complete history of every change. This is invaluable on a long project. When you change your mind about a system six months from now, you can look back at when and why the original decision was made. That context matters.

If you want a hosted solution, GitHub with a private repository is free for individual developers and gives you version control, a readable web interface, and backup.

Notion, Obsidian, and similar tools work too if that's what you'll actually use. The best tool is the one you will maintain. Whatever you choose, make sure it is version-controlled somehow — either natively or through a regular export habit.

One structural note: separate your public and private documentation. Your GDD — the document you might share with collaborators, post publicly, or reference in a video — should contain nothing that spoils your story or reveals IP you are not ready to share. Keep the full plot, the character truth, the spoiler-heavy design in a separate private document or repository. This costs you nothing and protects everything.

---

## [18:30 — 19:30] The Living Document

I want to come back to this because it's the thing most people understand intellectually but don't actually do.

Your GDD at the end of development will not look like your GDD at the start. That is correct. That is success. A GDD that looks the same on day one and day one thousand means either the game never changed — which is unlikely — or the documentation never kept up — which is a failure.

Date your decisions where the context matters. Mark open items clearly so you can find them. When you resolve an open item, update the document — don't just delete the question, replace it with the answer and the reasoning. Future you will want to know why you decided what you decided, not just what you decided.

And when the game changes significantly — a system gets cut, a mechanic is redesigned, a story beat moves — update the GDD to match. The document should describe the game that exists, not the game you planned eighteen months ago.

---

## [19:30 — 20:00] Wrap-Up

A Game Design Document is a map of your game. It helps you navigate when you are lost, communicate your vision to collaborators, make feature decisions consistently, and remember in year four why you built what you built in year one.

For solo developers especially, it is not a nice-to-have. It is the external brain that holds the project together across the years it takes to build something worth playing.

Start with the Razor. Then the pitch. Then the core loop. Everything else follows.

The next video is a walkthrough of the actual GDD for MGA — every section, with the reasoning behind the decisions. If you want to see how all of this applies to a real project in progress, that is the one to watch.

I'll see you there.

---

*[END OF SCRIPT]*

---

## Production Notes

- Runtime estimate: 20–22 minutes at a natural speaking pace
- Recommend screen share when showing MGA GDD sections referenced in the text
- The Razor deserves a full on-screen moment — pause on it, let it breathe
- Common mistakes section could benefit from b-roll of a messy/abandoned project folder to land the "scope creep" point visually
