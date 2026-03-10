# Creative Brief — Cooking

---

## Overview

Cooking in MGA is a **knowledge puzzle**. The player's Cooking skill determines how much of a recipe is revealed — at low skill, instructions are vague and incomplete; at high skill, the recipe is detailed with tips, warnings, and the science behind each step. The challenge is not reflexes or timing; it is figuring out what to do with the information available.

This fits Meghan's situation: she is a newcomer on Terridyn, an alien planet with unfamiliar ingredients. Earth cooking intuition does not transfer directly. Learning how Terridyn ingredients behave — which ones burn at high heat, which ones release liquid, which ones absorb too much stock — is part of settling into this world.

Cooking requires a kitchen. There is no campfire cooking or field preparation. The minigame is the same everywhere; the context changes the pressure.

**Related documents:**

- [Cooking Skill](../../mgaPriv/mechanics/skills/cooking.md) — WIS/AGI, progression tiers, training, synergies, narrative hooks
- [Cooking Minigame Design Notes](../../mgaPriv/mechanics/skills/cooking-minigame.md) — recipe structure, ingredient properties, skill integration, result quality
- [Jobs Creative Brief — UC1 (Waitress)](CB-jobs.md) — Burton's Tavern shifts, customer satisfaction, tips
- [Training Creative Brief](CB-training.md) — Celeste training sessions, Burton's Tavern as training hub
- [Notes & Discovery Creative Brief](CB-notes-and-discovery.md) — recipe collection as discovered knowledge
- [Social Creative Brief](CB-social.md) — cooking as relationship activity (Celeste, Terry, housemates)
- [Studying Creative Brief](CB-studying.md) — MCI cooking classes

---

## Use Cases

### UC1 — Cooking a Meal at Home

**Actor:** The player
**Goal:** Prepare a dish in the apartment kitchen using the recipe puzzle system, with no time pressure
**Trigger:** The player interacts with the kitchen in the apartment shared with Terry and Celeste

**Step by step:**

1. The player selects a recipe from their recipe collection, or chooses to experiment without a recipe
2. The recipe is displayed at the **clarity level determined by Cooking skill:**
   - Skill 1–10: bare outline — step names and general ingredients, no quantities, times, or temperatures
   - Skill 11–25: rough guide — approximate quantities ("a handful"), general heat ("medium-ish"), approximate times
   - Skill 26–50: clear recipe — specific quantities, heat levels, times, some technique notes
   - Skill 51–75: expert recipe — full detail plus tips, warnings, and substitution suggestions
   - Skill 76–100: master — full recipe with the *why* behind each step
3. The player works through the recipe step by step. Each step presents one or more **decisions:**
   - **Ingredient selection** — which ingredient to add (from available inventory)
   - **Quantity** — how much (too little / right amount / too much)
   - **Technique** — how to prepare it (chop, dice, mince, crush, whole)
   - **Heat level** — low / medium / medium-high / high
   - **Duration** — how long at current heat
   - **Step order** — which step comes next when the recipe is ambiguous
4. Not every step requires all decision types. A simple step might just be "add this ingredient." A complex step might involve selecting the ingredient, choosing the technique, setting the heat, and judging the duration
5. **Sensory feedback during cooking:** the game provides visual and audio cues — sizzle sounds, colour changes, steam, smoke. Higher Perception skill makes these cues clearer and more readable. A player paying attention can use the cues to judge doneness even when the recipe is vague
6. After the final step, the dish is complete. Result quality is determined by the percentage of correct decisions:
   - **Excellent** (90%+): full buff, best relationship effect, most training XP
   - **Good** (70–89%): partial buff, normal XP
   - **Mediocre** (50–69%): minimal or no buff, reduced XP. Edible but unremarkable
   - **Poor** (30–49%): no buff, negative reaction from housemates
   - **Inedible** (below 30%): wasted ingredients. Training XP still granted — you learn from failure
7. **At home, there is no time pressure.** The player can take as long as they want, re-read the recipe between steps, and think through each decision. This is the learning environment

**What the player experiences:** Cooking at home is where the player learns. Early on, recipes are vague and Meghan's results are mixed — Celeste is encouraging, Terry is honest. As Cooking skill improves, recipes become clearer and the player's own knowledge of Terridyn ingredients fills in the remaining gaps. Cooking for housemates is a domestic beat that reinforces the apartment dynamic — choosing what to cook, learning preferences, and the simple act of sharing a meal.

---

### UC2 — Cooking at Burton's Tavern

**Actor:** The player
**Goal:** Prepare dishes for customers during a tavern shift, where the same cooking puzzle has implicit time pressure from the job system
**Trigger:** A customer order comes in during a tavern shift (see [CB-jobs UC1](CB-jobs.md))

**Step by step:**

1. During a shift at Burton's, customer orders arrive. Each order specifies a dish from the tavern menu
2. The player enters the cooking minigame — **identical to UC1**. Same recipe display, same decisions, same skill-based clarity
3. **The difference is context:** while the player is cooking, customers are waiting. The job system tracks customer satisfaction, which degrades over time spent waiting. Faster correct cooking = happier customers = better tips
4. There is **no on-screen timer**. The pressure is indirect — the player knows customers are waiting because the job system tells them, not because a countdown is ticking. The player can still take time to think, but every moment spent is a moment the customer's patience erodes
5. **Multiple orders** may be active simultaneously during busy shifts. The player must decide: cook one dish at a time (slower but fewer mistakes) or manage multiple dishes (faster service but more decisions to juggle). Situational Awareness skill helps track multiple active dishes
6. Result quality affects tips directly:
   - Excellent dishes + fast service = high tips, reputation gain
   - Mediocre dishes or slow service = low tips
   - Poor or inedible dishes = complaints, reputation hit, possible penalty from Celeste or Burton
7. **Celeste is present** during tavern cooking. At early skill levels, she may provide verbal hints ("that needs more heat" / "you're overcooking the redleaf") that partially compensate for incomplete recipe clarity. As Meghan's skill improves, Celeste's prompts decrease — she trusts you to handle it

**What the player experiences:** Burton's is where cooking skill is tested under real conditions. The same puzzle the player practised at home now has stakes — customers, money, reputation. The player feels the pressure without an artificial timer: orders are backing up, Celeste is watching, and that tip is slipping. It rewards the player who practised at home and learned the recipes, because they can execute quickly with confidence.

---

### UC3 — Learning a New Recipe

**Actor:** The player
**Goal:** Add a new recipe to Meghan's collection through one of several acquisition methods
**Trigger:** The player encounters a recipe source (NPC teaching, book, class, experimentation)

**Step by step:**

1. **Celeste teaching** — Celeste walks Meghan through a dish step by step in the apartment kitchen or at Burton's. During the lesson, Celeste provides the full recipe regardless of Meghan's Cooking skill — the instructor fills in what skill doesn't reveal. The recipe is added to the collection at the player's current clarity level (future cooking without Celeste uses skill-based clarity)

2. **MCI cooking classes** — structured lessons that teach specific dishes. The instructor demonstrates and explains. Recipe added to collection. During the class itself, skill-gates on clarity are relaxed (the instructor is guiding). Same as Celeste — post-class cooking uses normal skill-based clarity

3. **Books and written recipes** — recipe items found in the world, purchased from bookshops, or discovered in NPC homes. When read, the recipe is added to the collection. Written recipes provide a fixed clarity level based on how detailed the source is — a professional cookbook is clearer than a scribbled note on a napkin. The player sees whichever is higher: the book's clarity or their skill-based clarity

4. **NPC conversations** — NPCs share recipes during Small Talk. A market vendor mentions how to prepare what they sell. A neighbour describes their favourite dish. Negotiation can persuade a professional chef to share a technique. Recipes acquired this way are often partial — the NPC gives the gist, not the full instructions

5. **Experimentation** — the player can attempt to cook without a recipe, choosing ingredients and techniques freely. Results are unpredictable at low skill:
   - If the experiment produces a recognisable dish (correct ingredients in roughly correct proportions), a new recipe entry is created based on what the player did
   - If it fails, no recipe is created — but the player learns something about the ingredients (this thornfruit released a lot of liquid)
   - At high Cooking skill (76+), the player can deliberately invent new dishes by combining knowledge of ingredient properties

6. All acquired recipes are stored in the notes system ([CB-notes-and-discovery](CB-notes-and-discovery.md)) as discoverable knowledge entries

**What the player experiences:** Recipe acquisition is woven into the world. Celeste teaches you at home. MCI teaches you in class. A market vendor mentions something in passing. A book in an NPC's apartment has a recipe scribbled in the margin. Experimentation in the kitchen sometimes produces something worth keeping. The recipe collection grows as Meghan's life on Terridyn expands — it is a record of who she has met and what she has learned.

---

### UC4 — Learning Terridyn Ingredients

**Actor:** The player
**Goal:** Discover how unfamiliar Terridyn ingredients behave through cooking, study, and NPC guidance
**Trigger:** The player encounters a Terridyn ingredient they have not used before

**Step by step:**

1. Terridyn ingredients are alien — Earth cooking intuition does not transfer directly. Each ingredient has properties the player must discover:
   - How does it respond to heat? (some release liquid, some burn easily, some need high heat to sear)
   - How much should you use? (some are potent — half the quantity of Earth equivalents)
   - What technique does it need? (some must be crushed, not chopped; some need blanching to remove bitterness)
   - What does it pair with? (flavour combinations that work on Terridyn are not the same as Earth)

2. **Discovery through cooking:** when the player uses an unfamiliar ingredient and the result is Good or Excellent, the ingredient's key property is recorded in the notes system. "Thornfruit releases liquid when heated — natural deglaze." Future recipes involving that ingredient benefit from this knowledge

3. **Discovery through teaching:** Celeste and MCI instructors explain ingredient properties during lessons. These are recorded immediately regardless of result quality

4. **Discovery through study:** the Research skill + books/databases can reveal ingredient properties without cooking. A player who reads about dustgrain before cooking with it knows to halve the quantity

5. **Discovery through failure:** a Poor or Inedible result that was caused by an ingredient property (burned the redleaf because the heat was too high) still teaches the player. A note is added: "Redleaf burns above medium-high." Failure is not wasted — it is information

6. Ingredient knowledge persists with the player, not just the character. Once the player learns a property, they know it even on a new playthrough. Cooking skill reveals the information through recipe clarity; player experience provides it through memory

**What the player experiences:** Terridyn's kitchen is unfamiliar territory. Early cooking is experimental — things burn, go mushy, or turn out bland because Meghan (and the player) does not know these ingredients yet. Over time, a library of ingredient knowledge builds up. The player starts to predict how new ingredients will behave based on what they have learned. Cooking becomes less about guessing and more about understanding — the same progression arc as Meghan's life on Terridyn.

---

### UC5 — Cooking as Relationship Activity

**Actor:** The player
**Goal:** Use cooking as a shared activity that builds relationships with housemates and friends
**Trigger:** The player cooks at home when housemates are present, or cooks for a specific NPC

**Step by step:**

1. **Cooking for housemates:** when Meghan cooks in the apartment, Terry and Celeste (and later party members) are the audience. Their reactions depend on result quality:
   - Excellent: genuine appreciation, conversation, relationship progression
   - Good: positive but unremarkable — "that was nice, thanks"
   - Mediocre: polite but honest — Terry will tell you it was bland
   - Poor/Inedible: negative reaction — embarrassment, wasted food, Terry's unfiltered honesty

2. **Learning preferences:** each NPC has food preferences — favourite dishes, disliked ingredients, dietary needs. These are discoverable through Small Talk, observation, and experimentation. Cooking someone's favourite dish provides a relationship bonus. Cooking something they dislike has a small negative effect

3. **Cooking with Celeste:** some cooking sessions are cooperative — Celeste and Meghan cook together. Celeste handles some steps while Meghan handles others. Celeste provides guidance, positive reinforcement, and occasionally shares personal stories during the cooking. These sessions build the Celeste relationship and provide better training XP than solo cooking

4. **Cooking for guests:** when the apartment has visitors (party members, friends, story NPCs), the player can choose to cook for them. This is a social action — the equivalent of hosting a dinner. Dish quality and guest preferences both matter

5. **Food as social infrastructure:** per the Cooking skill synergy with Small Talk — cooking for someone and talking to them about what they like opens conversational paths. Serving someone their favourite dish before a difficult conversation provides a social context bonus

**What the player experiences:** Cooking is not just a buff system. It is one of the ways Meghan builds a life on Terridyn. The apartment kitchen is where relationships deepen — Celeste's encouragement, Terry's honesty, the simple act of sitting down together. Learning what someone likes to eat and making it for them is a quiet form of care that the game recognises mechanically.

---

## Open Items

- Full Terridyn ingredient list and properties
- Food buff system — what buffs does food provide? CP recovery, SP recovery, temporary stat boosts, mood effects?
- Whether recipes have quality variants (one recipe with quality scaling, or separate entries for the same dish at different quality levels)
- Cooking minigame UI — dedicated screen or in-world interaction?
- AgriFood detection interaction at intermediate Cooking skill (26–50) — how does this surface in the minigame?
- Cooperative cooking specifics — Celeste handles which steps? Is it scripted or dynamic?
- Recipe complexity ceiling — maximum number of steps for the most complex dish
- Elite infiltration cooking (skill 51+) — does the minigame appear during infiltration sequences, or is it abstracted?
- NPC food preference data — how many preferences per NPC, how detailed
- Whether the cooking minigame applies to drink preparation (cocktails at Burton's?) or food only
