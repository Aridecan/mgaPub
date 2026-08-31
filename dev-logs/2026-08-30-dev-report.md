# MGA Dev Report — August 2026

*Ordinary Girl Meghan / Magical Girl Ava*

---

## The Honest Number

**Source submissions in August: zero.**

July's report ended with fifty-one. The last changelist in this project's history is dated the 30th of July and nothing has landed since. That isn't a rounding artifact or a reporting-window quirk — no code changed hands this month.

There are two reasons, and they're worth separating because only one of them is a problem.

The first is burnout. July was a hard month by design — a build pipeline stood up from nothing, six settings tabs, a rebindable control scheme, a content-tier system, a leak found and gated. It closed on the 28th with CoreX-M1 declared done and version `0.1.0-cxm1.57` published and verified. Then the tank was empty, and the mesh work that was supposed to start immediately slipped a few weeks. That is what it looks like when one person does this.

The second is a deliberate change of shift. **CoreX-M2 is an art milestone.** The whole point of it is a character standing in a city, and the character does not exist yet — which means the critical path this month ran through Blender, not through the engine. Blender work doesn't produce changelists until a mesh is finished enough to check in.

So: a quiet month with a real dip in it. Here is what's in it anyway.

---

## First, the Tail of July

Last month's report was written on the 26th, and the four days after it were busy. Folding them in here rather than losing them:

**CoreX-M1 closed.** The remaining items landed — localization packs published as plugin targets, gamepad glyphs per key, the UI-scale slider fixed, the project's C++ and CI scripts swept to pure ASCII to kill the corrupted-character bug on the splash screen, and a boot-order fix moving content-tier activation to a point in the sequence that isn't forty milliseconds ahead of the age gate. Verified in a packaged build, published, audited.

**CoreX-M2 was defined:** *Delivery Girl: First Run*. One sentence — Nessa stands up in a hand-built block of the city, in a packaged build, walkable, with sound. The milestone exists to prove a specific list of unknowns, and the honest headline from planning it was that **the packaged game currently has no city in it at all**. The city exists in the editor. Getting it through the cook is a real piece of work, not a formality.

**The mode got a premise.** Nessa is a student working delivery to make rent. One of her packages is a prototype drone; something goes wrong; the drone locks to her and the recipient wants it back. She now has a debt and a piece of equipment she can't return. It's non-canon and deliberately light — the premise exists to give the test mode a direction, not to be a story. The interesting mechanical piece is that she doesn't receive jobs directly: she has to find crypto keys hidden around the city, and each key unlocks the object holding the next batch of work. A day-side app and a night-side key hunt, using the same city.

**The third technical unknown was proven.** The movement plugin the project is standardising on now builds, cooks, stages and boots in a packaged build. Getting there cost a genuinely surprising discovery — enabling it silently force-enables the Steam online subsystem, which an empty-looking project file gives no hint of.

**The first megablock of roads was baked.** The city's roads are generated procedurally, which is wonderful in the editor and useless in a shipped build. Sixteen static meshes now exist covering nine full superblocks and seven edge superblocks, with the procedural originals kept alongside so the whole thing can be regenerated after changes. Nanite enabled on all sixteen. This is expected to be re-authored later — it's the first quick pass, not the final geometry — and saying so now is cheaper than pretending otherwise later.

---

## The Month's Real Decision: Start the Body Over

MGA has had a female base mesh since last autumn. It was built between August and October 2025, it's rigged, it's textured, and it has the only working facial shape set in the project.

It's being replaced.

Not because it's broken. Because five specific things about it are wrong, and every one of them gets multiplied by the fact that **this is the most-shipped asset in the game** — every female character in MGA is this mesh, scaled, with a shape key. A defect here is a defect several hundred times over.

| The defect | What it is |
|---|---|
| **Back arch too pronounced** | The lumbar curve is overstated; it reads in every idle pose |
| **Glutes sitting too high** | Wrong mass placement relative to the sacrum |
| **Hands and feet** | Copied from open-source Reallusion meshes — a third-party dependency in the most-used asset in the project |
| **Eyelids not straight** | Loops that don't run clean, which fights every facial expression built on top |
| **Crotch geometry doesn't flow** | Too much geometry, badly routed |

There's a third reason on top of the artistic one, and it's the honest one: **relearning Blender.** A year away from a tool costs you, and there's a course — 3D Anime Character Creation, by the YouTuber 2AM — whose target style is genuinely the style MGA is aiming at, and whose hair-modelling method is *already* the documented hair pipeline in this project. Same author, same head shape, same assumptions. Following it properly rather than skimming it is a defensible use of a month.

### Three sculpts, six exports, one skeleton

The scope grew, deliberately, while the decision was being made. The plan isn't one body — it's **three sculpts (female, male average, male heavy), six exports, and a single shared skeleton**. The marginal cost of the two male bodies is small, because the course is being worked through anyway and the sculpts will never again be this cheap to make. Three bodies sculpted at the same skill level in the same stretch of weeks look like one art direction; three bodies sculpted a year apart look like three.

The sequencing splits the difference between learning and shipping: run the early chapters — blockout, sculpt, retopo, UV — in lockstep across all three, because that's where repetition actually teaches and where cross-body consistency matters. Then **break lockstep, drive the female body all the way to a finished engine-ready export**, and come back for the males. Authoring fifty-two facial blendshapes three times in lockstep teaches almost nothing the second and third time and costs a great deal of calendar.

The critical path only needs one of the six. Nessa is the only character CoreX-M2 renders.

### Coverage that can't be turned off

The most useful thing to come out of designing this was a compliance decision, and it's a better one than the project had before.

MGA's base game is stream-safe by construction — that was July's commitment, using the Twitch VTuber minimum-cover guideline as an actual spec. The obvious way to implement it is to spawn a cover garment onto the body. The problem with the obvious way is that anything spawned can fail to spawn: a bug, a bad config, a mod, and the base build has a nude character in it.

So the cover is **merged into the base mesh itself** — a half-millimetre shell with its own material slot, joined into the geometry, shipped as one asset. Coverage becomes inseparable from the thing that ships. That's compliance by construction rather than compliance by spawn logic, which is the standard the content-tier boundary was always supposed to meet and previously met only by intention.

The same reasoning drove the tier split. Rather than hand-editing a second copy of the body — which drifts the first time the sculpt changes and never tells you it has — the master mesh carries a `Featureless` shape key. The adult export ships with it at zero; the base export bakes it in at full and strips it. Two genuinely separate meshes still ship, with identical vertex order by construction rather than by discipline, and changing the sculpt regenerates both.

One more correction fell out of it. The automated check was going to assert an exact morph-target count on the base export. That's a proxy, and a fragile one — it breaks the moment the facial shape set isn't exactly fifty-two. The robust form is a **denylist**: assert that no base export carries the four adult morph targets by name. It says what the rule actually says, it survives any future decision about the facial rig, and it fails just as loudly.

---

## The Skeleton Decision, Measured

A separate thread, from thinking about toes.

Standard game skeletons give each foot a single toe bone. MGA wants per-toe articulation — but not at the cost of throwing away compatibility with the enormous library of animation that assumes the Unreal mannequin. The question was whether you can have both.

You can, and it required actually counting rather than assuming. Bone-name sets pulled from the shipping engine assets:

| Skeleton | Bones |
|---|---|
| **Manny / Quinn (UE5 mannequin)** | 159 |
| **MetaHuman body** | 342 |

**All 150 of Manny's real deforming joints appear in MetaHuman under identical names** — including the whole corrective set. Epic aligned the mannequin to MetaHuman. So retargeting between them is name-identical on every driven chain, and adopting MetaHuman costs nothing in animation compatibility while adding twenty toe joints, a large finger corrective set, and access to MetaHuman-targeted animation and Control Rig assets.

**The decision changed: MGA's characters rig to a MetaHuman-compatible skeleton, not Manny.**

There is exactly one trap, and it's the kind that would have been found the hard way six months from now. **MetaHuman has no IK bones at all.** `ik_foot_root`, `ik_hand_gun`, `weapon_l/r` and the rest exist on Manny and are simply absent — and a large amount of third-party animation and most standard foot-IK setups reference them. They have to be added back by hand. That's now a checklist in a technical brief rather than a discovery.

The reasoning here is the same as the hair-bone reasoning from July, and it's becoming a house rule: **author the bones now, decide later what drives them.** The skeleton is shared by every body and every garment, so adding joints later means re-skinning everything made in between.

---

## What Actually Got Built

Hands, feet, and the body blockout with arms and legs — the geometry, live in the file as this is written:

| Piece | Vertices | Faces | Notes |
|---|---|---|---|
| **Body block** | 1,273 | 1,228 | **All quads**, mirrored |
| **Hand** | 1,380 | 1,442 | Hand-authored from primitives |
| **Foot** | 1,238 | 1,310 | Hand-authored from primitives |

Target for a finished body is roughly 17k triangles, which is Genshin-class and correct for a game that needs crowds. It also settles something worth writing down before someone standardises it away: **characters are deliberately not Nanite.** Seventeen thousand triangles is below the point where Nanite earns anything, and environment assets in this project get Nanite as a default import step — so characters need to be the stated exception rather than an accident.

Two things about that table are more interesting than the numbers.

**Hands and feet came first.** They're the hardest geometry on a human body, they're visible constantly, and they were the one part of the old mesh that wasn't MGA's own work. Doing them first means the third-party dependency is gone from the project's most-shipped asset before anything is built on top of it.

**All quads on the body.** Quads throughout, triangulated only at export. That isn't fastidiousness — it's what makes the cel-shaded outline read cleanly and what makes deformation behave when the character moves. The hands and feet carry a small number of triangles at the fingertips and toe tips, which is normal and fine.

---

## Three Control Modes, Not Two

Early in the month the control scheme got a revision that should have happened earlier.

The design had two on-foot modes: first person and third person. There are three, and the axis that actually matters isn't first-versus-third person at all. It's **whether the mouse cursor is captured or free.**

| Mode | Cursor | Move | Camera anchored to |
|---|---|---|---|
| **First Person** | captured | WASD, character-relative | the character's eyes |
| **Third Person Direct** | captured | WASD, character-relative | the character — park it over her shoulder and it stays there |
| **Third Person Pointer** | free | click a destination, *or* WASD camera-relative | the world — point it north and it holds north while she turns beneath it |

First Person and Third Person Direct share their controls completely; the second is just the first plus a momentary camera-adjust layer. **Third Person Pointer is the genuinely different one** — a point-and-click paradigm where the entire game is playable with the mouse alone, but where WASD stays fully live, because mouse-only should be an option and never a requirement.

Cursor capture is a hard boundary in the engine as well as in the design, which is why it's the right axis to build the state machine around. Everything else — heads-up display profile, camera rig, animation set — layers on top of it.

CoreX-M2 will ship first person and third-person direct. Pointer mode comes later.

---

## Where Two Stars Are

A worldbuilding session that produced something more durable than expected.

Terridyn and Calibran are now anchored to **real catalogued stars**. Not as in-fiction naming — nobody in 2352 calls Terridyn's sun "HD 143436" — but as an authoring backbone, so that distances, transit times, orbital parameters and night skies stay consistent and *derivable* rather than being invented one scene at a time.

| | Terridyn | Calibran |
|---|---|---|
| **Star** | HD 143436 | 18 Scorpii |
| **Constellation, from Earth** | Serpens | Scorpius |
| **Distance from Sol** | 145.6 ly | 46.1 ly |
| **Type** | G3V | G2Va |

Both are documented solar twins with no catalogued planets, which leaves their systems entirely free to invent. The selection had to satisfy three things already fixed in the lore: Terridyn's star is Sol-like and slightly younger than Sol, Terridyn's year is 400 days, and Calibran is roughly six months away by Curve Drive.

The satisfying part is how well the real data cooperated. HD 143436 is a near-exact match to Sol in temperature, mass and iron abundance, and its one anomaly is about **six times solar lithium** — independent evidence of youth. Its published age carries a ±2.9 billion year uncertainty, which is *exactly* the lore position: Terridyn's star is younger than Sol and we do not say by how much.

And the geometry landed better than it had any right to. The three systems are nearly collinear: Sol to Calibran is 46.1 light years, Calibran to Terridyn 100.4, Sol to Terridyn 145.6 direct. **Calibran sits 7.5 light years off the straight line** — routing through it costs a 0.6% detour, which is nothing next to the fact that a jump needs a star at each end anyway. Calibran is on the road to Terridyn because the real sky puts it there.

Meghan's journey out is about a year, aimless for the first half and directed for the second. That now has a number attached to it rather than a vibe.

---

## Diamond Shreddies

The other document written this month is about how this project talks to the people watching it, and it exists because of a YouTube video about breakfast cereal.

In 2008, Post's Canadian Shreddies brand rotated its square product 45 degrees and marketed the result as *Diamond Shreddies*. New shape. Completely deadpan, at full production values, eventually reaching a combo pack containing both squares and diamonds. Nobody was fooled. Nobody was meant to be. It ran for years and it worked.

Three properties of that campaign describe how MGA is made, and they're now written down as a commitment rather than a vibe:

**Enormous sincere investment behind a premise that doesn't need it.** The commitment *is* the point. Work done far past the level the joke requires stops being a joke and becomes an act of good faith.

**The audience is a co-conspirator, not a mark.** The pleasure is being on the inside of something performed with a straight face. The moment an audience is being *worked*, the whole thing collapses.

**The affection is displaced into the construction.** Nothing is stated directly. The effort is the statement.

That last one is the register the game is written in — reticence expressed as work, feeling routed through infrastructure because the direct channel is closed. It's also, not coincidentally, a description of the protagonist.

The reason this needed writing down now, in a month with nothing to show, is the rule that came out of it. A project this long has to answer a question a short one never faces: what is the relationship with people who are paying attention for *years*? The failure mode is **promise debt** — building anticipation for a payoff you keep deferring, until the audience stops being collaborators and starts being creditors. The rule is therefore: **the rotation must be visible early; the reason can take years.** Distributed payoff, never deferred payoff. Show the diamond now. Never sell the reveal.

Which is, in the end, why this report opens with a zero instead of burying it.

### The bill that isn't being sent

MGA picked up its first paying patron this month. Eight other people follow along for free; one of them decided to pay.

Under everything written above, the correct response to that isn't a thank-you post. It's a **pause on billing** — no charges until there's something here worth charging for.

Because look honestly at what the money would currently be buying. Eight monthly reports and a public task board. That's a real thing and I'm glad people read it, but it is text about a game, and text about a game is not a game. Charging for it would mean accepting money against a promise, which is the precise mechanism the section above exists to refuse. Being on the inside of something built in public should cost nothing until the thing being built can be installed.

So the billing is paused. It turns back on when there's a build worth putting on a hard drive — and that is a description of a state, not a date.

### What the free tiers get

Everything. There is no paywalled development content, and there won't be while the billing is paused. These reports go out in full to every tier on both platforms, and the [public dev board](https://github.com/users/Aridecan/projects/1) — which is more current than any monthly report — has never required an account at all.

One thing about that will change shape later, and it's worth saying in advance so it doesn't look like a tier being invented.

MGA is an adult game, and the character work now underway means these reports will eventually want to show images. Some of those images won't be postable everywhere. When that starts, each update **splits into two editions covering the same development** — one with the adult images, one without. Not two tiers, and not a paid edition and a free one. The same report, twice, so that the version safe to post in a given place exists without the development being cut down to fit it.

Which is the same split the game itself is built around: a base that is safe by construction, and adult content as a separate thing you opt into. It would be strange to architect the game that way and then not run the dev updates the same way.

---

## Housekeeping

**The owned-asset inventory** is now **1,039 products** on Fab, up 26 across three merges this month. The tooling for this is boring and keeps earning its keep: a saved library page goes in, a delta comes out, and the "do we already own something for X?" digest regenerates.

**Two audio bundles were checked and both were declined.** A 38-product bundle turned out to be **28 products already owned** — about C$1,470 of it — against 9 genuinely new. Separately, a vendor's AI-powered "build a bundle for your game" tool was given a detailed prompt about MGA and returned ten recommendations, **three of which were packs already sitting in the account**, and six of which were later volumes of series already owned. The tool matches a catalogue against your prompt; it doesn't check what you have. Useful to know, and cheaper to find out this way.

**A new column is coming to the asset inventory: generative-AI artwork.** Found on a pack whose store description disclosed AI content in one texture on a prop screen — trivially replaceable, but only if you know it's there. Two reasons it needs tracking: Steam's submission flow asks for an affirmative statement about AI content in the shipped build, and that answer needs a record behind it rather than a memory; and triage differs enormously between "a texture on a screen" and "the base meshes", which you want to tell apart without reopening the store page. It's designed as a **tri-state** — yes, reviewed-and-clean, or blank — rather than a checkbox, because a two-state box would silently manufacture a thousand unearned "clean" assertions on packs nobody has looked at. Blank is the honest default.

**Unreal 5.9 exists.** Announced on stage at Unreal Fest in Seoul and not yet in any published Epic material, which makes it one of those officially-unofficial situations. Noted; nothing to do about it yet. The engine upgrade to 5.8.1 is deliberately held until the body mesh is finished and ready to import, at which point everything moves together.

---

## By the Numbers

- **0** source submissions in August, against **51** in July — the honest headline
- **~3 weeks** of slippage on the mesh work, from burnout, said plainly
- **1** base body mesh being rebuilt from scratch, for **5** specific defects
- **3** sculpts, **6** exports, **1** shared skeleton — the scope it grew into
- **159 vs 342** — mannequin bones vs MetaHuman bones, measured rather than assumed
- **150** of the mannequin's deforming joints present in MetaHuman under identical names
- **9** IK and weapon bones MetaHuman is missing, which have to be added back by hand
- **3** hand-authored pieces finished: hands, feet, and the body blockout
- **1,273 / 1,380 / 1,238** vertices — body block, hand, foot
- **~17k** triangle target per finished body; **not** Nanite, deliberately
- **0.5mm** — the thickness of a cover shell merged into the mesh so it cannot fail to spawn
- **3** on-foot control modes, where the design previously had 2
- **9** patrons, **1** of them paying — and billing now paused until there's a build worth paying for
- **2** real stars now anchoring the setting, **145.6** and **46.1** light years from Sol
- **0.6%** — the detour cost of routing Sol → Calibran → Terridyn
- **16** road static meshes baked, covering 9 full and 7 edge superblocks
- **1,039** owned Fab products inventoried, **+26** this month
- **28 of 38** products in an audio bundle already owned — bundle declined
- **3 of 10** AI-recommended asset packs already owned — recommender doesn't check entitlements

---

## What's Next

- **Finish the body.** Torso and head are the remaining sculpt work, then retopology and UVs across all three bodies in lockstep. That's the critical path and everything else waits behind it.
- **Then break lockstep** and drive the female body through the facial shape set, skeleton, weights, cover shell and export — the first character asset this project has made entirely itself.
- **Get the city through the cook.** The packaged game still has no city in it. The roads are baked; the buildings and the streaming setup aren't.
- **Upgrade the engine** to 5.8.1 once the mesh is ready to import, so the toolchain moves in one deliberate step instead of piecemeal.
- **Implement the generative-AI column** in the asset inventory, and start the review pass that fills it in.

July's report ended with a machine that turns this project into something a person can install. This month didn't add to that machine at all. What it did instead was decide — carefully, and with more measuring than usual — what the machine is going to be asked to carry.

Next month there should be a character.

---

*MGA is a solo-developed adult action RPG currently in pre-production. Follow development on the [public dev board](https://github.com/users/Aridecan/projects/1), on [Patreon](https://www.patreon.com/c/aridecan), or at the [Project Tangent SubscribeStar](https://subscribestar.adult/aridecan). Every tier, free included, gets every update in full.*
