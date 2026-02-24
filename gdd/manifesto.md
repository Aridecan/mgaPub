# MGA — Developer Manifesto

These are the principles that guide every decision made in the development of *Ordinary Girl Meghan / Magical Girl Ava*. They are commitments to the player, to the modding community, and to myself.

---

## 1. Respect the Player's Time

A game developer has no right to a player's time. Time spent in this game is a gift, and it will be treated as one. This means no padding, no artificial barriers to progress, no systems designed to manufacture engagement at the expense of enjoyment. Every hour a player spends here should feel like their choice.

---

## 2. Systems Before Content

The systems of this game must be fun before the story is told. Systems bring the player in; story keeps them there. Content built on top of broken or unengaging systems is content that cannot save the experience. The gameplay loop will be proven first.

---

## 3. Single Player Freedom

This is a single player game. Players are free to mod it, hack their save files, and alter their experience however they choose, provided they are not harming anyone else. What a player does in their own game is their business.

---

## 4. Mod-Friendly, Not Mod-Supported

I support the modding community. I will provide tools for loading mods and managing load order, and I will document the mod boot process. That is the extent of what I can offer. I am one person. I cannot provide full mod support, and I will not pretend otherwise. Mod-friendly is a commitment; full mod support is not.

The game will not police or restrict mod content. What a mod contains is between the mod author and the player. Any system that attempted to enforce content compliance on mods would only be as reliable as mod authors choosing to comply — which cannot be guaranteed — making it a false assurance rather than a real protection. The tools are provided; the responsibility rests with the player.

---

## 5. Player Agency Is Never Removed

The player's controls will not be locked out for more than 5 to 10 seconds under any circumstances. If the game knocks out the main character during a fight, the player will be given a sequence to recover — it may not be easy, but the chance is always there. Cutscenes and dialogue are skippable.

There is one planned exception: a single scene, approximately 30 to 45 seconds, where a character playfully obscures the skip prompt. It happens once, it is a joke, and it is the exception that proves the rule.

---

## 6. Failure Has Consequences, Not Game Over Screens

Failure leads to consequences and failure content. It does not lead to a dead end. Story progression will never be locked behind a successful outcome — there is always a path forward through success. Failure may open additional story paths, or it may not, but success will always be available.

Game over screens may exist in specific circumstances if the design requires them, but they are not the default response to a player losing.

---

## 7. Structure Friction Control

Some mechanics will be hard. Assists will be available for players who need them. These assists are opt-in, clearly communicated, and free of shame. They reduce friction without removing the underlying systems — the challenge remains intact and available. Access to the game's content should not be gated by reflex or precision if the player does not want it to be.

---

## 8. Community Autonomy Over Enforcement

Where communities form around challenges — speedruns, puzzle completions, self-imposed restrictions — I will provide tools to support them. Timers, widgets, data hooks where possible. The rules of those communities are theirs to set and enforce, not mine. My role is to give them the infrastructure; their role is to build the culture.

---

## 9. Automation Is Part of the Product

Official builds are built by Jenkins, not by my local machine. Every part of the build and release pipeline that can be automated will be automated. Reproducibility and auditability are not optional features.

---

## 10. The Boot Lifecycle Is Supported

The game has two boot phases. The first brings the player to the main menu; mods are not loaded or active at this stage. The second begins when a new game is started or a save is loaded; mods are loaded at this point. The game must always be able to reach the main menu from a cold boot. The main menu is the guaranteed safe state.

---

## 12. No Rails

Features are designed to enable multiple approaches, not to funnel the player down a single path. When a system is built, the question is not "what is the intended use?" but "what are all the ways this could be used?" Players will find interactions and exploits the designer never anticipated — that is not a flaw to be patched, it is a sign that the systems have real depth.

A game with genuine systemic freedom will always be played in ways its creator did not foresee. That is the goal.

---

## 11. No In-Game Purchases

This game is sold once. There are no in-game purchases, no battle passes, no loot boxes, no premium currencies, no temporary digital assets sold for real money. Ongoing support for development is available through SubscribeStar for those who choose it. That is the full extent of monetization.

I helped build some of the earliest implementations of in-game purchase systems on console. I watched what that became. It is not in this game.
