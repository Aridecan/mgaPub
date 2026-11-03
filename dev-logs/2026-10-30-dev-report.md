# MGA Dev Report — October 2026

*Ordinary Girl Meghan / Magical Girl Ava*

---

## Billing: Still Paused

**Patreon billing is still paused, and it stays paused until further notice.** Nothing about the reason has changed since August. It turns back on when there's a build worth putting on a hard drive, and there isn't one yet.

This month's report also comes in **two posts** on each platform. The text is the same in both. The members post adds work-in-progress pictures of the character; the free post is text only. That split exists because of the pictures, not because of the billing. Nobody is being asked to pay to see them.

---

## The Honest Number, Third Time

**Source submissions in October: zero.**

That makes three months running. Last month's report ended with a week off the day job "going straight into CoreX-M2." It did, but not into the engine. The critical path for M2 runs through the character, and the character is in Blender. So that's where the week went, and most of the month after it.

The pattern is the same as September: nothing changed in Perforce, and the mesh moved forward a lot.

---

## The Prediction Held: 7.29

Last month's report found that the base body's foot was modelled in a 25-degree high-heel pose, which made the figure measure taller than it was. The fix was to flatten the foot before rigging and re-measure, and the prediction was **about 7.30 heads**.

The foot was flattened on 2 October. Re-measured from the side, heel to crown: **7.29**.

That's mid-band for an adult anime figure (the target range is 7.0 to 7.5), and it's now written into the public character pipeline document along with the measurement protocol, so the number can be reproduced instead of trusted. Two other numbers came out of the same pass:

- **Crotch height is 0.518 of stature.** Real adult women measure 0.47 to 0.48; stylised anime runs 0.50 to 0.53. So the legs are long on purpose, and still inside the style's normal range.
- **The foot is 23.75 cm at Meghan's height**, about 8% small against stature. Its internal proportions (width against length, heel against length) all sit inside real adult ranges, so it was accepted as-is. One lesson worth keeping: a foot that measures about one head long looks too long in the viewport. The eye is the unreliable instrument here, not the mesh.

The whole cast now has scale factors. Every female character is this one mesh at a different uniform scale, from **Tierney at 147 cm** to **Jennifer at about 192 cm**, with **Meghan at 170 cm** as the canonical height. The factors get recomputed once the mesh is final, because every edit to the sole of the foot shifts them slightly. The current values are good to under 2 mm.

---

## The Body Is Done, in Both Tiers

The body is finished and scaled to Meghan's 170 cm. The bigger news is how the two content tiers now live inside it.

MGA ships a **Core** game (the Steam build, with Twitch-safe coverage) and an optional **Spicy** tier that's never on Steam. Both use the same body. The way that works:

- **The full body is the base mesh.** A shape key removes the parts that don't belong in Core.
- **One export script produces both.** It exports the Spicy body first, then applies the removal key and exports the Core body. Same topology and vertex order either way, so skin weights, animation and clothing fit carry across without any extra work.
- **The textures are split the same way.** There are two texture sets. The Spicy one lives only inside the Spicy content, and the Core body's material never refers to it. The Spicy body gets its own material that swaps in only the two textures that differ.

That last part matters because of a gap it exposed in the build's safety net. The CI leak-audit gate (the check that stops Spicy content ending up in the Steam build) works by looking for Spicy asset *paths* inside the Core package. A shape key isn't a separate asset. It exports as a morph target, and its data sits *inside* the body mesh. If the Core export ever carried the removal key by mistake, it would pass the gate cleanly while still containing exactly the geometry it's meant to exclude. So the export script has a rule (the Core file carries no morph that encodes Spicy geometry), and the gate has a new candidate extension: a cook-time check on the body mesh itself. Better to find that hole before production starts than after.

---

## Hair, and a Test of the Magic

**The first hair mesh is done**, built with the skull-cap method from the documented hair pipeline. Its UVs are about three-quarters laid out.

The layout is deliberate. Every strand is a straight vertical strip in UV space, root at one end and tip at the other. That gives the material a root-to-tip gradient for free, and the gradient drives two things:

- **Colour**: the hair blends between two colours along its length.
- **Glow**: the same gradient boosts emission toward the tips.

The glow is a test, not final art. When magic is in play, the tips should glow brighter, and this is the cheapest way to find out early whether the emission path through the material system works the way the art direction document says it should.

---

## The Face Is Being Textured Now

That's the work in progress as this report goes out. It's also where the art direction document has its two newest open questions, both written down this month:

- **Does Unreal's new Toon shading model accept a normal map at all?** If not, normal maps get skipped on organic meshes like skin and kept for cloth and hard surfaces.
- **Can a face shadow map feed its light/shadow decision?** Anime faces usually shade from a hand-painted threshold map rather than true lighting, so that shadow shapes like the triangle under one eye fall where an artist put them. If the Toon model works out the shading band internally with no way to override it, faces may need their own material.

Both get answered in the editor, once there's a face to put on screen.

---

## A Second Test Mode: Haunted House

Delivery Girl, the first test mode, exists to build movement and traversal before the story needs them. This month it got a sibling with a different job: **Haunted House**, a Halloween horror mode, designed today. Like Delivery Girl it's **non-canon**, and like Delivery Girl it's a place to build systems the main game needs, somewhere they can be tried without a story depending on them.

The inspiration is the board game *Betrayal at House on the Hill*: a haunted house that's built as you explore it.

- **The house assembles itself behind you.** Doors to unexplored rooms stay shut. Open one, and a room is drawn and placed behind it for the rest of the run. In the fiction, the rooms are pocket spaces stitched together by doorways, an aspect of hell that surfaces at Halloween. That makes the house a graph of doors rather than a floor plan, so stairs and floor changes are just transitions between rooms.
- **The door hides the load.** Rooms are loaded when you decide to open the door, and the wait is covered by the door itself: most doors creak open in a second or two, and locked ones are a lock-picking, hacking or trap-disarming minigame. Which room you get is decided the moment you start, so failing the lock doesn't re-roll it.
- **Four from a cast of six.** You pick a party of four from Meghan, Celeste, Daisy, Jennifer, Trevor and one more still to be decided. **Nobody is magical** in this mode: these are the characters and their personalities, not their powers. You play one at a time and switch freely; the others run on AI.
- **The party doesn't stay together.** Companions wander, search, find things and get into trouble on their own, and their personalities decide how far they stray. One sticks close; another goes off alone.
- **Capture and rescue.** Lose a fight or an event, and that character ends up in a capture room in the basement or upper floor, possibly one nobody has found yet. The direction to a captured character shows in the AR display, and you can switch to them to see where they are. Captives can work on their own escape. At least one capture room has several cells, so getting two captives out means switching back and forth between them. Capture all four and a ten-minute countdown starts.
- **Every run is a mission.** The mission sets the house's mystery: appraise the place for a real-estate deal, investigate it for a school newspaper, extract data from a specific room, find a missing schoolmate, find out who sent out party invitations to the place. The twist is a mystery of the house that depends on the mission, not a traitor in the party.

It's solo with an AI party. Co-op would be wonderful and is out of scope, but there's a rule attached: **nothing gets built in a way that locks multiplayer out later.**

Two practical consequences. First, **the character pipeline needs a male base body**, which it doesn't have yet; Trevor is a big man and isn't a uniform rescale of the female base. Second, the mode comes **after Delivery Girl and before work on the prologue**, because it tests systems the prologue needs: combat, the inventory, party switching, capture and rescue, searching, stealth and room hazards. Ready for Halloween 2027 would be lovely. That's a wish, not a date.

### Clothing damage runs in every tier

The haunted house has room hazards that damage clothing, and designing them exposed a mistake. One of the design documents had placed clothing damage in the Spicy tier only. That broke a rule the tier system is built on: **game mechanics never change between tiers; only content does.** A fight shouldn't play differently depending on which content pack you installed.

So clothing damage now runs in every tier, Core included. Core stays stream-safe with a **floor at the underwear layer**: underwear can't be damaged in Core, and it already meets the Twitch-safe standard. Spicy removes the floor. Same system, same balance, different content. Clothing also gives item-specific bonuses (a gas mask in a room full of gas, gloves, shoes), so what's worn matters to the mechanics in every tier.

The mode doubles as a test of the tier split for content: mission lists differ by tier, so the list has to accept missions that a tier pack adds when it activates.

### What each mode tests

The two modes were designed to cover different ground. Roughly: **Delivery Girl tests the open world, and Haunted House tests what happens once you get somewhere.**

**Both modes** (each pushes these in a different direction):

- **Third-person camera.** Delivery Girl's is a plain follow camera in an open city. Haunted House's has to work in tight rooms: the camera bumps into walls, and doors swing shut so you see at most two rooms at once.
- **Searching.** Delivery Girl searches a large district for the crypto keys that unlock night jobs. Haunted House searches a small, dense house room by room.
- **Stealth.** Delivery Girl has stealth and exposure on night runs. Haunted House has stealth against the house's enemies.
- **Clothing.** In Delivery Girl, what she's wearing affects exposure. In Haunted House, room hazards damage clothing, and some items give bonuses (a gas mask, gloves, shoes).
- **The tier rule.** Delivery Girl tests it on bodies and outfits. Haunted House tests it on content: each tier pack adds its own missions.
- **The AR display.** Delivery Girl's highlight widget marks keys and containers, and costs widget points and drone battery. Haunted House's points toward captured characters.

**Haunted House only:**

- **Combat**, for the first time. Losing a fight leads to capture, and companions fight on their own.
- **The inventory**, for the first time: the reach-based physical inventory from last month's report, where what you can use depends on what's worn, which pockets you can reach and whether your hands are free.
- **A party, and switching** between its members.
- **Companion AI** that wanders, searches and gets into trouble on its own.
- **Capture and rescue**, including captives escaping on their own and rescues that take two characters.
- **Fights you aren't watching**, resolved in summary with an alert so you can switch in.
- **Rooms placed as you explore**, from a seed, with navigation built at runtime.
- **Loads hidden behind doors**, with minigames covering the wait.
- **Room hazards.**
- **Missions that set up the run's mystery.**
- **More than one playable character**, including the male base body the pipeline doesn't have yet.
- **Groundwork for multiplayer**, without co-op itself.

**Delivery Girl only:**

- **A large streamed city** that survives the cook. That's CoreX-M2's main job.
- **Day and night** as gameplay, with different work for each.
- **Deliveries**: the phone app and routes.
- **A debt and an economy** where honest work never quite covers it.
- **Drone battery** as something the player actively spends.
- **The HUD widget budget** as a real cost.
- **City sound**: ambience and footsteps that change with the surface.
- **Vehicles**, eventually, from a bicycle up to a hoverbike.

---

## Lore: Meghan's Cover Story, and Life on the Station

Most of the month's lore work is spoiler territory and stays private. Some of it doesn't need to.

**Meghan's cover story.** Meghan arrives on Terridyn needing local ID to enrol, rent and work, and the ID she gets says she was **born on the orbital colony**, the station that took in the Exodus evacuees. Specifically, it says she's a **pre-Exodus native**: one of the tens of thousands already living there during the century it ran half-empty, before nearly a million evacuees arrived in a matter of weeks.

It's a good cover, because natives are a small minority most evacuees never met, so "I don't remember you" is normal. It's also under constant pressure, because about nine in ten of her university-age peers grew up on that station, and the dangerous question is the ordinary one: *which school did you go to?*

Her technique is **true feelings, false facts**. Six months of drifting between stations before she arrived gives her the texture of station life honestly; only the specific station is false. *"Are your parents still up there?" "We don't talk."* That one's entirely true, and it closes the subject.

What she can't fake are the **physical tells**. Someone really raised on the station squints in Terridyn's sunlight (the station gets about a quarter of Earth's), feels heavy for weeks, misjudges catches without the spin, and is startled by a sun that rises in one place and sets in another. Meghan does none of that, and an observant station kid could notice. That's a candidate player mechanic: when someone asks about her past, you choose to deflect, answer with true feelings, or commit to a specific fact, and specifics can be remembered and checked.

**Life on the station.** Last month's orbit fix led to a proper write-up of the station itself, and the details that reach a player got done carefully:

- **Station slang has its own directions**: *turnwise* and *widdershins* (with and against the spin), *hingeway* and *sunway* (along the axis). Station kids say them by reflex. Meghan has to translate, and can slip.
- **Direction changes your weight.** Moving turnwise adds to the spin, so you get heavier; widdershins, lighter. A sprinter feels about 9% either way. A fast tram feels like 1.5g one way and 0.6g the other.
- **Dawn is the mirrors opening.** The light comes from mirrors that swing open through the day, so morning is dim and the sun's image sits low toward one end of the station.
- **The trip home is date-dependent.** The station orbits the star, not the planet, so its distance changes all the time. At the moment the game starts it's about 323 light-seconds away, a trip of a little over two days. At other times it's on the far side of the star.
- **Why TMC keeps it running.** The Terridyn System Defense Force, usually called the TMC Space Force, is based aboard. The system belongs to no interstellar nation and pirates are a standing threat; the station gives the Space Force a home port without Terridyn's gravity well.

**The magical girls' colours follow a scheme.** The first three are the additive primaries: **Ava blue, Nisa green, Nagi red**. After that the scheme moves to the subtractive set, starting with **Arri yellow**.

The rest (how hell is organised, where its demons came from, a good deal of history) stays private. As before, that part will come out in the game.

---

## Housekeeping

**The owned-asset inventory is at 1,046 products** on Fab, up 5 from a Free Asset Tuesday. **3 of 1,046 have been reviewed** for AI-generated artwork. Fab listing pages now show a "Generated with AI" field, which makes the review faster than it was. Blank still means unchecked, not clean.

**WorldBLD announced MCP support** for its city and road tools mid-month. The road bake that turns the procedural roads into static meshes is still a manual, click-through step with no scripting hook, and it's part of why the packaged game has no city. If the new support exposes the bake, that changes. It hasn't been evaluated yet. The engine upgrade to 5.8.1 stays on hold until WorldBLD ships a compatible build.

**The dev machine has been hard-resetting** about once a week, about a second after it's unlocked from idle. No crash dump, no hardware error logged. The CPU diagnostic passed, and a GPU power cap is being tested now to rule the power supply in or out. Separately, the main monitor turned out to have a stale 2023 colour calibration loaded into the graphics card, which made it read dim and blue. That's fixed, and the monitor is being kept as the HDR and lighting review screen.

---

## By the Numbers

- **0** source submissions in October, third month running
- **7.30**: the predicted head count after flattening the foot; **7.29**: the measured one
- **0.518**: crotch height against stature, long by design and inside the style's range
- **23.75 cm**: Meghan's foot, accepted as-is
- **147–192 cm**: the cast's height range, all one mesh
- **1** body finished, **2** tiers exported from it by **1** script
- **1** gap found in the leak-audit gate, before it could matter
- **1** hair mesh done, UVs **¾** laid out, **2** things driven by one gradient
- **2** open questions about Toon shading, both waiting on the face
- **2** test modes now: Delivery Girl and Haunted House, both non-canon
- **6** characters, **4** in a party, **0** of them magical, **7** missions so far
- **1** male base body the pipeline now needs and doesn't have
- **9 in 10**: Meghan's classmates who grew up on the station she claims as home
- **323 light-seconds**: the station's distance when the game starts, a two-day trip
- **1,046** owned Fab products, **3** reviewed for AI artwork
- **3** unexplained hard resets on the dev machine, **1** stale colour profile found

---

## What's Next

- **Finish texturing the face**, then answer the two Toon shading questions against it in the editor.
- **Finish the hair UVs** and build the gradient and glow material.
- **Join the head to the body**, rig, skin, and write the two-tier export script with its Core check.
- **Import her.** Upgrade the engine at the same time, once WorldBLD allows it.
- **Get the city through the cook.** The packaged game still has no city in it.
- **Write up Haunted House** as a creative brief, the same way Delivery Girl got one. Real-time capture is the hard open problem: how a fight resolves when you're playing a different character two floors away.

September ended with "she isn't in the engine yet." That's still true. What's changed is that the body she'll arrive in is finished, measured, and already split into both tiers. That was the part most likely to go wrong after import, and it's been dealt with before import instead.

---

*MGA is a solo-developed adult action RPG currently in pre-production. Follow development on the [public dev board](https://github.com/users/Aridecan/projects/1), on [Patreon](https://www.patreon.com/c/aridecan), or at the [Project Tangent SubscribeStar](https://subscribestar.adult/aridecan). Every tier, free included, gets the full text of every report. This month's work-in-progress pictures are in the members post.*
