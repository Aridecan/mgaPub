# MGA Dev Report — September 2026

*Ordinary Girl Meghan / Magical Girl Ava*

---

## The Honest Number, Again

**Source submissions in September: zero.**

That's two months in a row, and it's worth saying why this one is different from August.

August's zero had burnout in it. September's doesn't. It was a quiet month in the engine because the work was somewhere else. Last month's report said the critical path for CoreX-M2 now runs through Blender, and this month it did. The character course is at **lesson 12**. The saved files in the art depot are numbered well past that, but only because Blender's incremental save bumps the number every time, so the filenames overstate the progress. None of this work becomes a changelist until there's a mesh finished enough to import, and there isn't one yet.

So nothing changed in the engine, and the mesh moved forward a lot. Here's the mesh.

---

## The Body Is Done; the Head Is Next

At the end of August the body was a blockout with hands and feet. At the end of September:

- **The full body is done.** The hands and feet from August are now part of it rather than separate pieces.
- **The head exists but isn't attached yet.** Face, eyes, teeth and tongue are built as separate pieces. Joining the head to the body waits on the foot fix described below, because the order of those steps matters.
- **The hair is half done.** The front is finished and the back is in progress, following the course's hair method, which was already the documented hair pipeline for this project.

The face is the part that matters most. It's where the old mesh's worst defect was (eyelid loops that didn't run clean), and it's what every facial expression will be built on. So it's the part being built slowly.

### Measuring the proportions, and a trap in the foot

Partway through the month the figure got measured properly for the first time. Stylised anime characters are specified by **head count**: total standing height divided by the height of the head. It's the single number that most decides whether a figure reads as childlike, heroic, or somewhere in between.

MGA's base body measures **7.25 heads**. That sits mid-band for an adult anime figure. It's also a **cast-wide constant**, because every female character in the game is this one mesh at a different uniform scale. No mechanism exists to vary it per character, and building one would break the shared-animation and shared-clothing design the whole character pipeline depends on.

The measuring turned up a trap that's worth recording.

**The foot is modelled in a high-heel pose.** It's plantar-flexed 25.2 degrees, so the heel sits 1.72 units above the ball. The lowest point of the mesh is therefore the ball of the foot, not the heel, and **you can't see that from the front**. From the front the foot looks flat. Measure the figure in front view and you get **7.55 heads**, not 7.25, and the character looks noticeably leggier on paper than she is. Reference charts all use barefoot stature, so the correct measurement is heel to crown, taken from the side.

The fix is to **flatten the foot in the mesh before rigging**, so the bind pose is flat by construction and matches the MetaHuman reference pose the skeleton now targets. The order of the steps matters. Flatten, re-measure (the prediction is about 7.30), apply scale, apply the mirror, and only then join head to body and skin. All of that is now written into the character pipeline document as a numbered procedure rather than left to memory. The alternative, rigging in heels and fixing the rest pose afterwards, is a well-known way to end up with a skeleton that believes the foot is flat while the mesh doesn't.

---

## Hair Under Hats

A design problem that has to be solved before any hair is final: what happens to a character's hair when she puts on a hat?

The easy answer is to hide the hair. It's also wrong. A beanie that makes the whole head of hair vanish looks bald, and a character whose ponytail disappears under a baseball cap looks broken. The rule MGA has adopted is: **hide only the hair the headwear actually covers, and reshape the rest.**

The hair is cut into four regions along a shared line: cap, crown, fall and fringe. Headwear is sorted into **five classes** by how it meets the head:

- **Band**: sits on a ring around the head (caps, beanies, most hats)
- **Arc**: goes over the top from ear to ear (headphones)
- **Strap**: a thin band around the head (goggles, respirator straps)
- **Shell**: a hard enclosure (helmets)
- **Hood**: soft fabric that drapes over everything

Arc and Strap exist because this is a cyberpunk setting. Headphones, goggles and respirators are everyday items, and a system with only hats in mind would treat them badly. Each class hides the regions it covers and applies a per-class compression shape to the rest, so the hair visibly presses in where the headwear sits. The class also gives the hair author and the hat author a shared outline to fit against.

One thing is deliberately out of scope: magical girl hair ornaments. Transformation hair is signature hair and gets handled as its own case.

---

## The Inventory Moved Out of the Phone

A UI decision that fixed two contradictions in the design documents.

The in-game UI now has **three categories**, and each one is gated by something different:

- **Phone**: digital things, available if Meghan has her phone
- **Physical**: the inventory, limited by what she can actually reach
- **Out-of-character**: player tools that exist outside the fiction, always available

The inventory used to be a section of the phone app, which made no sense. Losing your phone shouldn't stop you checking your pockets, and the prologue taught the inventory before the phone even existed in the story. Now it has its own input, runs in real time, and is **reach-based**. What it offers depends on what's worn, how it's layered, which pockets are usable, whether her hands are free, and whether she's restrained.

Configuring the action bar moved the other way, out of the phone and into the out-of-character menu. It's a player aid that Meghan herself never sees, so it shouldn't be locked because she lost her phone or transformed.

---

## A Bug in the Sky

The project has a small C++ library for the setting's astronomy: sun and moon positions, twilight, the calendar. This month it turned out to have been putting the orbital colony in the wrong place for over a year.

The colony orbits the star, not the planet, with an 800-day period. The library derived its orbital radius from Kepler's third law with the wrong exponent, which gave **1.34 AU**. The correct figure is **1.692 AU**. The old number describes a 568-day orbit, and that error spread into the lore as a light-travel distance roughly half of what it should be. Fixed at the source, and every document that quoted the old number now points to one place.

That fix led to a proper write-up of the colony itself. It's a classic three-island O'Neill cylinder, 10 km by 10 km, spun for 1g, with long mirrors bringing in starlight. That produced one principle that's now a working rule:

**Background lore that never reaches the player stays deliberately vague.** Mirror masses, cap structures and flywheel sizing have each had one plausibility pass and then stopped. Someone born on the station doesn't know every spec by heart. They know it works. The physics gets done properly only where it reaches the player: travel times, light levels, and gravity and spin that someone could notice. A thrown ball curves slightly on a station spinning at 0.42 rpm, and people raised there learn to allow for it. That kind of detail earns its keep. The tonnage of the mirror hinge doesn't.

---

## Character Backgrounds

Some of the month went into the characters' backstories. That's private, spoiler-heavy work, so all this report will say is that it happened. What it produced isn't coming out in a dev report. It'll come out in the game.

---

## Housekeeping

**The owned-asset inventory** is at **1,041 products** on Fab, up 2 from Free Asset Tuesday pickups.

**The generative-AI column is live.** Last month's report promised it; it now exists as a hand-maintained flag file that's merged into the inventory each time it's rebuilt, so it survives a full re-parse of the library. It has three states: flagged, reviewed and clean, or blank. **1 of 1,041 products has been reviewed so far, and it's the one that prompted the column.** Blank means nobody has checked yet. That's not the same as clean, and the inventory says so on its first page.

**A week off the day job started yesterday**, and it's going straight into CoreX-M2. The engine upgrade to 5.8.1 is still held until the mesh is ready to import, so the toolchain moves in one step.

---

## By the Numbers

- **0** source submissions in September, second month running, this time with no burnout in it
- **Lesson 12**: where the course actually is, whatever the filenames say
- **1** full body finished; **1** head built but not yet attached
- **½** a head of hair: the front done, the back in progress
- **7.25** head count: the cast-wide proportion, measured, not assumed
- **7.55**: what you get if you measure it from the front by mistake
- **25.2°**: the plantar flexion hidden in the foot, to be flattened before rigging
- **5** headwear classes, **4** hair regions, **0** hats that make a character bald
- **3** UI categories: phone, physical, out-of-character
- **1.34 → 1.692 AU**: the colony's orbit, after fixing the Kepler exponent
- **0.42 rpm**: the colony's spin, which is why a thrown ball curves slightly there
- **1,041** owned Fab products, **1** reviewed for AI artwork

---

## What's Next

- **Finish the back of the hair, flatten the foot, re-measure, and attach the head.** Then retopology and UVs.
- **Break lockstep** and take the female body to an engine-ready export: facial shapes, skeleton, weights, the merged cover shell, and both tier exports.
- **Import it.** Upgrade the engine at the same time.
- **Get the city through the cook.** The packaged game still has no city in it, and that hasn't changed.

Last month ended with "next month there should be a character." There's more of one than there was: her body is finished, her head is built, and her proportions are measured and written down. She isn't in the engine yet, and that's the job for this week.

---

*MGA is a solo-developed adult action RPG currently in pre-production. Follow development on the [public dev board](https://github.com/users/Aridecan/projects/1), on [Patreon](https://www.patreon.com/c/aridecan), or at the [Project Tangent SubscribeStar](https://subscribestar.adult/aridecan). Every tier, free included, gets every update in full.*
