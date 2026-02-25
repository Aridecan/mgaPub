# Script — Video 03: Design Document Decomposition — Creative Briefs, Feature Briefs, and Technical Briefs

**Target runtime:** ~15–20 minutes
**Audience:** Solo developers and small indie teams; viewers of Videos 01–02
**Format:** Talking head with diagrams and document examples
**Prerequisite:** Video 01 recommended

---

## [00:00 — 01:30] Hook

You have a GDD. It describes your game at a high level — what the player does, what it looks like, what it sounds like. And now you need to build something. You pick a feature — say, a HUD system — and you sit down to design it.

Where do you start? Do you start with the player's experience? The screen layout? The code architecture? All three at once?

If you try to do all three at once, you will end up with a document that is part wishlist, part wireframe, and part pseudocode, and none of those three audiences — the designer, the UI artist, the programmer — can use it cleanly. Including you, even if you are all three of those people.

The solution is decomposition. Break the design into layers, each with a clear purpose and a clear audience. This video covers a three-tier model: Creative Briefs, Feature Briefs, and Technical Briefs. I use this on MGA, and I think it is worth understanding even if you choose to simplify it for your own project.

---

## [01:30 — 04:00] The Three Tiers

[VISUAL: Diagram showing the three tiers as a vertical stack]

```
Creative Brief  →  "What does the player experience?"
Feature Brief   →  "How does this feature work for the player?"
Technical Brief →  "How is this built in code?"
```

Each tier answers a different question, from a different perspective, at a different level of detail.

**The Creative Brief** describes the player experience. Think of it as a use case. Who is the actor? What is the goal? What is the flow? What does success look like? What does failure look like? It does not care about screen layouts or data structures. It cares about what the player does and what they feel while doing it.

**The Feature Brief** describes the feature from the player's perspective — but now with specifics. Screen layouts, interaction flows, state transitions, edge cases the player might encounter. If the creative brief says "the player configures their HUD," the feature brief shows what that configuration screen looks like, what the constraints are, and what happens when the player exceeds them.

**The Technical Brief** describes the implementation. Data models, class hierarchies, event systems, performance considerations. This is the document a programmer reads to build the thing the feature brief designed.

Each tier decomposes into the next. The creative brief is the input to the feature brief. The feature brief is the input to the technical brief. You can trace any line of code back through the feature brief to the creative brief to a player experience.

---

## [04:00 — 07:00] The Creative Brief — Use Cases, Not Features

[VISUAL: Example creative brief header]

The creative brief is the closest thing to a UML use case in game design. If you have a software engineering background, you already know this pattern. If you don't, here is the structure.

A use case has:

- **An actor** — the player, or sometimes the game system itself
- **A goal** — what the actor is trying to accomplish
- **A flow** — the steps from start to goal
- **Success conditions** — what it looks like when the goal is achieved
- **Failure conditions** — what happens when it isn't

Here is an example from MGA. The HUD modification creative brief:

> **Actor:** The player
> **Goal:** Customise what information is visible during gameplay
> **Flow:** Open the phone, navigate to the widget editor, select widgets from the available pool, arrange them on screen, confirm the layout
> **Success:** The player's HUD shows exactly the information they chose, within the budget their hardware allows
> **Failure:** The player tries to add more widgets than their budget supports — the system communicates the constraint clearly and the player makes a trade-off decision

That is the entire creative brief for HUD modification, reduced to its essentials. It tells you what the player is doing and what the experience should feel like. It does not tell you how many widget points a phone has, or what the drag-and-drop interface looks like, or what data structure stores the layout. Those belong in the next two tiers.

The value of writing this first is that it forces you to understand the experience before you design the implementation. If you cannot describe what the player does in plain language, you are not ready to design the screens. And if you cannot describe the screens, you are not ready to write the code.

---

## [07:00 — 10:00] The Feature Brief — Screens and Interactions

[VISUAL: Example feature brief with a wireframe or layout sketch]

The feature brief takes the creative brief's use case and designs the player-facing implementation. This is where you answer questions like:

- What does the screen look like?
- What are the interactive elements?
- What are the states and transitions?
- What are the constraints the player encounters?
- What are the edge cases?

Continuing the HUD example: the feature brief would specify that the widget editor is a drag-and-drop interface accessed through the phone. It would show that each widget has a point cost displayed on its icon. It would define the budget bar at the top of the screen that fills as widgets are placed. It would describe what happens when you try to place a widget that exceeds the remaining budget — does it bounce back? Grey out? Show a warning?

The feature brief is the document you would hand to a UI artist or a UX designer. It has enough detail for them to build mockups and prototypes. It does not have enough detail for a programmer to implement it — that is the technical brief's job.

One important rule: the feature brief should not contradict the creative brief. If the creative brief says the player makes a trade-off decision when they hit the budget limit, the feature brief cannot design a system where the budget is invisible. The creative brief is the contract; the feature brief is the fulfilment.

---

## [10:00 — 12:30] The Technical Brief — Implementation

[VISUAL: Example technical brief with a class diagram or data flow]

The technical brief is the programmer's document. It takes the feature brief's design and specifies how to build it.

Data models — what structures hold the widget layout, the budget, the available widget pool. Event systems — what fires when a widget is placed, removed, or the budget changes. Performance considerations — how many widgets can be active before the frame budget is affected. Integration points — how does the widget system talk to the phone tier system, the skill system, the save system.

For a solo developer, the technical brief might be lighter than for a team. You are the programmer. You know your codebase. But even a short technical brief — "this system uses an array of widget structs, the budget is a simple integer comparison, the layout is serialised to the save file as a JSON blob" — saves you from making the same architectural decisions twice.

The technical brief is also where you catch problems that are invisible at the design layer. "The feature brief says widgets can be freely positioned, but the rendering system only supports grid-aligned positions." That conflict is found in the technical brief, not in the creative brief. Finding it here is cheap. Finding it in code is expensive.

---

## [12:30 — 14:30] Do You Need All Three?

Honest answer: it depends on the size of your team and the complexity of your project.

If you are a solo developer making a simple game, you might collapse all three into a single document per feature. That is fine. The important thing is not having three separate documents — it is understanding that three separate concerns exist: the experience, the design, and the implementation. Even if they live in one file, keeping those concerns mentally separate produces clearer thinking.

If you are on a small team — two to five people — the separation starts to matter. Your designer writes the creative brief. Your artist or UI person reads the feature brief. Your programmer reads the technical brief. Without the separation, everyone is reading one document and extracting only the parts relevant to them. That works until it doesn't.

If you are on a larger team, the separation is essential. It is how game studios operate. The decomposition is not bureaucracy — it is a communication tool that scales.

For MGA, I am one person, and I still find value in thinking in these three tiers even when I collapse them in practice. The creative brief forces me to understand the player's experience. The feature brief forces me to design before I code. The technical brief forces me to think about architecture before I open the editor. Each layer catches a different category of mistake.

---

## [14:30 — 16:00] The Decomposition as a Teaching Tool

[VISUAL: The three-tier diagram again]

If you are learning game design, or if you are teaching it, the decomposition is a useful exercise even for features you could design in your head.

Take something simple — a health bar. The creative brief: the player needs to know how close they are to death, at a glance, without reading a number. The feature brief: a horizontal bar, red, left-aligned, depletes left to right, flashes when critical. The technical brief: a UMG widget bound to the player's health attribute, updated via delegate, colour lerps from green to red based on percentage.

Three documents for a health bar is overkill in production. But as a learning exercise, it teaches you to think in layers. The experience is not the design. The design is not the implementation. Each one is a separate decision space with separate concerns.

Once you can decompose a health bar into three tiers cleanly, you can decompose anything. And you will know when collapsing the tiers is a pragmatic shortcut versus when it is skipping a step that will cost you later.

---

## [16:00 — 17:00] Wrap-Up

Three tiers. Creative Brief: what does the player experience? Feature Brief: how does this work for the player? Technical Brief: how is this built?

Each tier answers a different question. Each tier is the input to the next. The decomposition is the discipline; how rigidly you apply it is your call. A solo developer can collapse layers. A large team should not. But understanding the layers is valuable at any scale.

If you are building a GDD — and you should be, if you watched the first two videos — the creative brief is where your GDD meets your implementation. It is the bridge between "what is this game" and "how do I build this feature." Start there. The rest follows.

Thanks for watching.

---

*[END OF SCRIPT]*

---

## Production Notes

- Runtime estimate: 15–20 minutes at a natural speaking pace
- The three-tier diagram should be a persistent visual reference — show it at the start, return to it at transitions
- The health bar example in the teaching section benefits from building the three tiers visually on screen as they are described
- Consider showing a real MGA creative brief (CB-hud-modification) alongside the simplified examples to ground the concept in the actual project
- This video works standalone but pairs naturally with Videos 01 and 02
