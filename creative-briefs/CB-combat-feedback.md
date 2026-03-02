# Creative Brief — Combat Feedback

---

## Overview

Combat feedback is the layer between mechanics and feel. The damage pipeline is well-defined — collision, bone detection, phase checks, clothing cascade, CP reduction — but none of that matters if the player cannot read what is happening. Feedback is how the player knows their punch landed, how hard they were hit, whether their block worked, and what just happened to their jacket.

MGA's combat is action-biased, not souls-like. The feedback language needs to be readable at speed — clear enough that a player mid-combo can tell the difference between a clean hit, a partial hit through clothing, and a whiff without pausing to check numbers. Every feedback element serves one question: **what just happened, and what should I do next?**

The no-death design rule shapes feedback fundamentally. There are no death animations, no blood, no gore. Characters go unconscious, not dead. The feedback language must communicate impact and consequence without lethal imagery — which means leaning harder on motion, sound, light, and material response rather than on visceral damage visuals.

**Related documents:**

- [CB-combat](CB-combat.md) — damage pipeline, attack data model, defence mechanics
- [CB-clothing](CB-clothing.md) — clothing damage cascade, dissolve shader, destruction stages
- [Combat Mechanics](../../mgaPriv/mechanics/combat.md) — blocking, bleedthrough, knockback
- [Animation Architecture Notes](../../mgaPriv/mechanics/animation-architecture-notes.md) — hit reaction research questions
- [Audio Direction](../gdd/audio-direction.md) — elemental sound signatures, UI audio philosophy
- [Art Direction](../gdd/art-direction.md) — cel shader, outline rendering, material system
- [Difficulty Framework](../gdd/difficulty.md) — reaction windows, AI parameters
- [Magical Girl Weapons](../../mgaPriv/mechanics/magical-girl-weapons.md) — ability-specific knockback effects

---

## Use Cases

### UC1 — Hit Confirmation (Attacking)

**Actor:** The player
**Goal:** Know immediately and unambiguously that an attack connected, how cleanly it connected, and what it did
**Trigger:** Any attack from Meghan/Ava lands on a target

**What the player perceives on a successful hit:**

1. **Hitstop.** A brief freeze frame on impact — both attacker and target pause for a fraction of a second. The duration scales with hit quality: a glancing blow gets minimal hitstop; a clean heavy hit gets a longer freeze. Hitstop is the single most important feedback element — it makes the difference between an attack that feels like it connects and one that feels like it passes through
2. **Impact particle.** A burst effect at the point of contact. The particle type reflects the damage source — physical strikes produce a sharp radial burst; ice attacks produce crystalline shatter fragments; fire produces ember scatter; lightning produces spark discharge. The particle is brief and directional (emanates from the hit point outward)
3. **Hit sound.** An audio impact layered from two components: the attack sound (fist, weapon, spell) and the surface sound (flesh, clothing material, metal armour, magical shield). The combination tells the player what they hit and what was in the way. A punch hitting a leather jacket sounds different from a punch hitting bare skin, which sounds different from a punch hitting a magical barrier
4. **Camera response.** A subtle directional nudge — the camera shifts slightly in the direction of the hit's force, then recovers. Heavier hits produce a larger nudge. This is not screenshake (which is reserved for environmental effects and AoE) — it is a directed impulse that reinforces the hit direction
5. **Target reaction.** The target plays a hit reaction animation (see UC2). The attacker sees the consequence of their strike in the target's body response

**Hit quality tiers and feedback scaling:**

| Hit Quality | Hitstop | Particle | Sound | Camera |
|------------|---------|----------|-------|--------|
| Clothing-only (no CP damage) | Minimal | Muted, fabric-coloured | Cloth impact, soft | Slight nudge |
| Partial (clothing + CP) | Moderate | Standard burst | Layered cloth + body | Clear nudge |
| Bare zone (full CP) | Full | Bright, sharp | Clean body impact | Strong nudge |
| Critical hit | Extended | Exaggerated, element-coloured | Amplified with tonal accent | Strong nudge + brief zoom tighten |

**What the player does NOT see:** Damage numbers floating over the target. MGA communicates damage through visual consequence (clothing state, CP bar movement, target reaction severity) rather than through numbers. The CP bar provides the quantitative read; everything else is qualitative.

---

### UC2 — Hit Reactions (Target Response)

**Actor:** The target (NPC or Meghan when hit)
**Goal:** Communicate the severity and direction of an incoming hit through the target's physical response
**Trigger:** Any attack successfully deals damage to a character

**Reaction tiers (escalating severity):**

| Tier | Name | Visual | When it occurs |
|------|------|--------|----------------|
| 1 | **Flinch** | Brief involuntary twitch; does not interrupt the target's current action | Light hits, glancing blows, minor damage through heavy clothing |
| 2 | **Stagger** | Stumble in the hit direction; interrupts current action; brief recovery window before the target can act again | Moderate hits, clean strikes, accumulated light damage |
| 3 | **Knockback** | Forced movement away from the hit source; longer recovery; may collide with environment | Heavy hits, charged attacks, specific abilities (Stone Arrow, Shield Rush) |
| 4 | **Knockdown** | Target falls; must stand up before acting; grounded vulnerability window | Very heavy hits, critical hits at close range, specific ability effects |

**Hit direction matters.** A hit from the left produces a reaction toward the right. A hit from behind produces a forward stumble. A hit from above (overhead strike, falling attack) compresses downward. The directional response makes combat spatially readable — the player can see where the hit came from by watching the target react.

**Reaction severity factors:**

- **Damage amount** relative to the target's Fortitude (FOR). A hit that would stagger a low-FOR target may only flinch a high-FOR one
- **Attack type.** Blunt attacks produce more stagger and knockback than slashing or piercing. Heavy weapons produce higher-tier reactions than light weapons
- **Target state.** A target already staggered is more susceptible to knockback. A target mid-action is more susceptible than one in a neutral stance. Resistance Training (Physical) reduces reaction severity
- **Hyper armour.** Some heavy attacks and specific abilities grant the attacker resistance to flinch and stagger during execution — they absorb hits without interrupting. This is communicated through a visual tell (a brief flash or outline glow on the attacking character) so the defender knows their hit landed but did not interrupt

**Meghan's reactions.** When Meghan takes a hit, the player feels the same reaction tiers — flinch as a brief camera jolt and control stutter, stagger as momentary loss of control with the camera following the stumble, knockback as forced movement, knockdown as a fall and recovery sequence. The player's input is not ignored during reactions — directional input during stagger influences recovery direction; mashing during knockdown speeds the stand-up.

---

### UC3 — Damage Communication (Taking Hits)

**Actor:** The player (as Meghan/Ava)
**Goal:** Understand how much damage was received, from where, and what state Meghan is in
**Trigger:** Meghan takes damage from any source

**Feedback layers when Meghan is hit:**

1. **Directional indicator.** A brief arc or wedge at the screen edge in the direction of the incoming hit. The colour of the indicator reflects the damage type — warm tones for fire, cool tones for ice, white for physical, purple for dark/curse. Fades quickly; does not clutter the screen during sustained combat
2. **Screen edge vignette.** At low CP, the screen edges darken or tint progressively. This is a persistent ambient indicator — the player always knows roughly how much CP remains without looking at the bar. The vignette intensifies as CP drops, reaching maximum intensity near the knockout threshold
3. **CP bar response.** The CP bar drops smoothly on hit. A trailing ghost bar shows the pre-hit value for a moment, making the damage amount readable at a glance. Large hits produce a visible chunk removal; small hits produce a smooth drain
4. **Clothing feedback.** If clothing absorbed damage, the dissolve shader advances visibly on the affected garment. At threshold moments (pocket failure, garment drop, destruction), the clothing system produces its own feedback events (see UC7)
5. **Hit reaction.** Meghan's body responds per UC2 — the player feels the reaction through camera and control response
6. **Audio.** An impact sound from the hit itself, plus a brief vocal response from Meghan scaled to severity — a grunt for a flinch, a sharper exhale for a stagger, a cry for a knockback or knockdown. Vocal responses vary to avoid repetition

**CP state communication:**

| CP State | Ambient Feedback |
|----------|-----------------|
| Above 75% | Clean screen; full capability; no ambient indicators |
| 50–75% | Subtle edge vignette; occasional breathing audio between actions |
| 25–50% | Visible vignette; Meghan's idle stance shifts (more guarded, less relaxed); movement slightly less fluid |
| Below 25% | Strong vignette; audible laboured breathing; visible fatigue in animations; CP bar pulses |
| Near zero | Screen desaturates slightly; tunnel vision effect; audio muffles; final-warning pulse on CP bar |

---

### UC4 — Defensive Feedback (Blocks, Parries, Evasions)

**Actor:** The player
**Goal:** Know that a defensive action succeeded and how well it succeeded
**Trigger:** Meghan blocks, parries, or evades an incoming attack

**Block feedback:**

1. **Impact sound** — a distinct blocking sound (weapon clang, shield thud, forearm impact) layered with the attack sound. Different from a clean hit sound — the player learns to distinguish "I blocked that" from "I got hit" by audio alone
2. **Block spark/flash** — a brief visual at the point of contact. Distinct from hit particles — a block produces a flatter, wider flash (deflection) rather than a sharp burst (penetration)
3. **Knockback/stagger (bleedthrough)** — blocking does not eliminate impact effects. The bleedthrough always carries some physical response — a blocked heavy hit still pushes Meghan back. The visual distinction is in severity: a blocked hit produces a controlled brace (Meghan absorbs the force deliberately) rather than an involuntary stagger
4. **Damage reduction visibility** — the CP bar drops less than an unblocked hit. The trailing ghost bar makes the reduction visible — the player can see how much the block saved

**Parry feedback (perfect timing):**

A successful parry is the highest-reward defensive action and needs the most satisfying feedback:

1. **Distinct parry sound** — sharper, cleaner, more resonant than a regular block. A tonal accent that the player learns to associate with success
2. **Time dilation** — a very brief slowdown (shorter than hitstop but perceptible) at the moment of the parry. The world pauses just long enough for the player to register "I nailed that"
3. **Flash effect** — a brighter, more defined flash than a block. Directional, emanating outward from the parry point
4. **Counterattack window** — the attacker is briefly staggered by the parry, and Meghan's next action has priority. The window is communicated through the attacker's reaction (visibly off-balance) and optionally a subtle UI prompt
5. **Minimal knockback** — a perfect parry absorbs force cleanly. Meghan barely moves. The contrast with a regular block (which pushes her back) reinforces the quality difference

**Evasion feedback:**

1. **Whoosh audio** — the attack passes through the space Meghan just left. The sound communicates the near-miss
2. **Motion trail** — a brief afterimage or motion blur on Meghan during the dodge. Communicates speed and invincibility frames without a UI element
3. **No impact effects** — the absence of hit feedback IS the feedback. The player knows the evasion worked because nothing happened
4. **Defensive critical (high Evasion skill)** — a perfect dodge at high skill may produce a distinct audio cue and a brief slowdown, signalling a counterattack window similar to a parry

---

### UC5 — Status Effect Feedback

**Actor:** The player / the system
**Goal:** Communicate when a status effect is applied, active, and cleared — on Meghan and on enemies
**Trigger:** Any status effect application or expiration

**Status effect application:**

When a status lands, the player needs to know immediately:

1. **Application sound** — a brief audio cue distinct to the status category. Mental statuses (Shaken, Flustered, Provoked, Embarrassed) use a softer, internal-sounding cue. Physical statuses (Burning, Frozen, Shocked) use sharper external cues matching the element
2. **Icon flash** — a status icon appears briefly near the affected character, then settles into a persistent position (near the CP bar for Meghan, near the target's nameplate for enemies). The icon is readable at a glance — distinct shape and colour per status
3. **Visual effect on the character** — sustained subtle visual for the duration. Burning: faint heat shimmer. Frozen: frost creep on extremities. Shocked: occasional spark. Mental statuses: posture shift (hunched for Shaken, arms crossed for Flustered, tense stance for Provoked). These are ambient, not distracting — the player notices them in quiet moments, not mid-combo

**Reading enemy status:**

The player reads enemy status the same way the AI reads the player — through visible tells:

- **Staggered enemy** has a visible off-balance posture
- **Burning enemy** has a heat shimmer and flinches periodically
- **Shaken enemy** has wider, less controlled movements
- **Provoked enemy** becomes more aggressive (posture forward, movements sharper)

The system does not rely on floating status icons over enemies at a distance. The player reads the enemy's body language — the same body language the AI reads on Meghan. Signal parity extends to feedback: if the AI can read Meghan's status from her animations, the player should be able to read an enemy's status from theirs.

**Status clearing (meditation):**

When a status effect clears — through meditation, through time, or through a teammate's assistance — a brief resolution cue plays: a soft exhale audio, the persistent visual fading out, and the status icon dissolving. The player feels the relief of the status ending.

---

### UC6 — Elemental and Magical Feedback

**Actor:** The player
**Goal:** Distinguish between elemental types by feel, not just by colour
**Trigger:** Any magical attack lands (from Meghan/Ava or against her)

**Elemental feedback signatures:**

Each element has a distinct combination of visual, audio, and impact character. The player should be able to identify an element with their eyes closed (audio) or with the sound off (visual). Both channels carry the information independently.

| Element | Visual Signature | Audio Signature | Impact Character |
|---------|-----------------|-----------------|-----------------|
| **Ice (Ava)** | Crystalline shatter; sharp angular fragments; frost creep from impact point | Clean, sharp crack; resonant sustain; glass-like quality | Brittle and precise; hitstop feels crisp |
| **Fire (Nagi)** | Ember scatter; heat distortion ripple; brief flame bloom | Roaring whoosh; crackling sustain; deep warmth | Explosive and expansive; knockback-biased |
| **Earth (Nisa)** | Debris scatter; dust cloud; ground crack propagation | Deep thud; rumbling sustain; grinding stone | Heavy and grounded; stagger-biased |
| **Lightning (Arri)** | Spark discharge; branching arc flash; brief screen brightness spike | Sharp electrical crack; buzzing sustain; ozone snap | Fast and disorienting; flinch-chain-biased |

**Magical vs physical distinction:** Magical attacks have an additional layer of visual feedback — a brief glow or emanation at the point of impact that physical attacks lack. This is subtle but consistent, training the player to distinguish magical damage sources from physical ones. Audio direction (established in audio-direction.md) reinforces this: magical impacts have a tonal quality that physical impacts do not.

**Elemental interaction feedback:** When elements interact — ice meeting fire, lightning meeting earth — the impact produces a combined feedback event. The visual blends both elements' particles; the audio layers both signatures. This communicates elemental synergy and opposition to the player through sensory experience rather than through a UI tooltip.

---

### UC7 — Clothing Damage Feedback

**Actor:** The system
**Goal:** Communicate the progressive state of clothing damage during combat
**Trigger:** Clothing takes durability damage from any source

**Progressive clothing feedback:**

Clothing damage is communicated through the dissolve shader, which advances as durability decreases. But threshold moments — when something changes functionally — need distinct feedback events:

| Event | Visual | Audio | Significance |
|-------|--------|-------|-------------|
| **Minor damage** | Dissolve shader advances slightly; barely noticeable in motion | Fabric tear/stress sound, brief | Cosmetic; no gameplay effect yet |
| **Moderate damage** | Visible wear; dissolve pattern clearly progressing | Louder material stress | Player should consider repair after combat |
| **Pocket failure** | Items visibly fall from the garment; brief slow-motion on the drop | Distinct "scatter" sound — items hitting ground | Functional loss; player may lose access to stowed items mid-fight |
| **Garment drop (wearability threshold)** | Garment physically falls off; brief camera attention to the falling piece | Fabric dropping sound; distinct from item scatter | Zone is now unprotected; significant tactical change |
| **Garment destruction (0%)** | Garment disintegrates; dissolve shader completes to nothing; items scatter | Destruction sound (material-specific: fabric rip, leather crack, metal snap) | Permanent loss; replacement required |

**Layered clothing awareness:** When an outer garment is destroyed, the inner layer becomes visible and is now the outermost protection for that zone. The visual change communicates the exposure — the player sees what's now taking hits.

**Uniform-specific feedback:** The magical girl uniform has its own feedback language. Uniform damage does not use the dissolve shader — it manifests as a glowing crack pattern (magical energy disruption) that spreads as uniform HP decreases. At the 75% curse threshold, the crack pattern pulses with the curse colour. At destruction, the uniform shatters into light fragments and reforms as the lower-tier fallback (childhood uniform or civilian clothes from pocket dimension, depending on context).

---

### UC8 — KO and Unconsciousness

**Actor:** The system
**Goal:** Communicate knockout without lethal imagery
**Trigger:** Any character's CP reaches zero

**The no-death constraint requires a distinct KO language:**

1. **Final hit.** The hit that drops CP to zero gets exaggerated feedback — extended hitstop, amplified impact, and a brief time dilation. The player (or AI) knows this was the finishing blow
2. **Collapse.** The character plays an authored collapse animation that transitions into ragdoll. The collapse is not a stiff fall — it reads as loss of consciousness, not death. The character's body goes limp progressively (arms first, then torso, then legs), not rigidly
3. **Grounded state.** Once on the ground, the character enters a breathing idle — subtle chest rise and visible breathing. This is the primary visual distinction between unconscious and dead. The character is clearly alive. In anime-styled rendering, this can be communicated through exaggerated but gentle breathing motion
4. **Post-KO interaction.** Unconscious characters can be bound (Binding skill), searched (Search skill), or left. They cannot be further damaged — the personal shield nanites prevent it. The feedback for interacting with an unconscious character is calm and deliberate, distinct from combat feedback

**Meghan's KO (player character):**

When Meghan's CP hits zero:

1. Screen desaturates fully; audio muffles and fades
2. Camera pulls to a wider angle as Meghan collapses
3. Brief blackout
4. Transition to the appropriate consequence — forced 6-hour rest (core loop), failure pipeline entry (bounty/hacking), or story-specific outcome

The transition is not abrupt. The player has a moment to register what happened before the screen changes.

**Ally KO (party member):**

When a teammate is knocked out:

1. A distinct audio cue (teammate voice line — pained exhale, name call from nearby allies)
2. Brief camera attention if the player is controlling another character
3. The downed ally's portrait on the UI dims and shows a KO indicator
4. The downed ally is now a tactical concern — they can be targeted for binding/capture by enemies, and the remaining team must protect or recover them

---

## Open Items

- Hitstop duration values — exact frame counts per hit quality tier
- Whether hitstop applies to both attacker and target or target only
- Camera nudge magnitude and recovery timing per hit tier
- Whether damage numbers exist at all, or if communication is purely visual/bar-based (current design leans no numbers)
- Screen vignette colour — red (traditional) or CP-bar-coloured for consistency?
- Hit reaction animation count per tier — how many variants to prevent repetition?
- Hyper armour visual tell — flash, outline glow, or something else?
- Parry time dilation duration — how many frames of slowdown?
- Whether evasion has visible invincibility frames (character flickers/ghosts) or is purely spatial
- Status effect icon design — per-status unique icons or category-based?
- Whether status effects on enemies are readable only through body language or also through a targetable status display (CAMERA widget?)
- Elemental interaction feedback specifics — how many element combinations produce unique feedback?
- Clothing dissolve shader performance impact — can the shader update mid-combat without frame drops?
- Uniform crack pattern art direction — how does the magical energy disruption look in the cel-shaded style?
- KO breathing idle animation — how exaggerated for anime style while still reading as "alive"?
- Whether knockdown has a getup invincibility window and how that window is communicated visually
- Haptic/controller rumble support — intensity profiles per feedback event?
- Sound occlusion — do hit sounds change based on environment (indoor echo, outdoor open)?
- Whether feedback intensity scales with difficulty setting or is fixed
- Feedback for hitting an ally accidentally (friendly fire indicator)
- Audio barks (Meghan's vocal reactions) — how many variants per reaction tier to prevent repetition?
