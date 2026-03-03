# Creative Brief — Knockout & Recovery

---

## Overview

When a character's Consciousness Points (CP) reach zero, damage continues to accumulate as **negative CP**. The character is unconscious, and the player enters the **brainwave sync minigame** — an interactive waveform-tracking game where the player restores consciousness by synchronising five brain wave bands. The deeper into negative CP, the harder and longer the recovery.

There is no game over from knockout. There is no retry prompt. The fight continues without the downed character. Poor minigame performance means longer unconsciousness, a lower post-revival CP cap, and a worse tactical situation when the character wakes up. The world does not wait.

**Related documents:**

- [Combat Resources](../../mgaPriv/mechanics/combat-resources.md) — CP pools, cap degradation, revival mechanic detail, negative CP
- [Combat Creative Brief](CB-combat.md) — combat system overview, attack data model
- [Meditation](../../mgaPriv/mechanics/skills/meditation.md) — skill that improves revival minigame performance
- [Action Bar System](../../mgaPriv/mechanics/skill-bar.md) — 10-slot bar reused as minigame input
- [Combat Feedback Creative Brief](CB-combat-feedback.md) — UC8 KO/unconsciousness feedback

---

## UC1 — Entering Knockout

**Trigger:** Character's CP reaches zero. Incoming damage continues to push CP into negative values.

**Player experience:**

- The screen transitions to Meghan's unconscious perspective — dark, abstract, interior
- No cut to a separate screen or menu — the transition is visual, representing consciousness fading
- Battle audio becomes muffled and distant
- The brainwave display appears: five horizontal wave bands stacked vertically, each labelled (Delta at bottom, Gamma at top)
- Only the waves required by the knockout depth are active; inactive waves are greyed out
- The minigame begins immediately — no prompt, no "press to start"

**What the player understands:** Meghan is unconscious. The waves represent her brain activity. She needs to get them back in sync to wake up. The action bar buttons — the same ones used for combat — now control the waves.

---

## UC2 — Tracking a Single Wave

**Context:** Shallowest knockout — only one or two waves active. The player's introduction to the mechanic.

**Player experience:**

- A target waveform scrolls left to right across the Delta band — an irregular, organic squiggle, not a clean sine wave
- The player's needle sits on the wave band with a position range of 10 steps (5 up, 5 down from centre)
- Action bar slot 1 moves the needle up; slot 2 moves the needle down
- **Tap** nudges the needle one step; **hold** ramps continuously
- The player rides the needle up and down to follow the target waveform as it scrolls
- A sync indicator shows how closely the needle tracks the target: green (within 1 step), yellow (within 2), red (3+)
- Matching the wave for the required hold duration completes the sync — the wave locks in, the screen clears, Meghan wakes up

**What the player learns:** The core mechanic. Up button, down button, follow the wave. The same two-button input produces different physical actions depending on the wave — slow sweeps for Delta (hold the button), rapid taps for faster waves.

---

## UC3 — Managing Multiple Waves

**Context:** Moderate knockout — three waves active (Delta, Theta, Alpha). The plate-spinning begins.

**Player experience:**

- Three wave bands are active simultaneously, each scrolling at its own speed with its own waveform shape
- Delta (slots 1/2): slow rolling curves — easy to follow, can be maintained mostly by feel
- Theta (slots 3/4): gentle undulations — needs periodic attention
- Alpha (slots 5/6): steady rhythm — needs more active tracking
- The player cannot perfectly track all three simultaneously — they must rotate attention
- Unattended waves **drift out of sync** at their band's drift rate. The player sees the sync indicator sliding from green toward red
- The player develops a pattern: hold Delta by feel, watch Alpha directly, check Theta periodically

**Drift and destabilisation:** Poorly managed waves affect adjacent bands. If Alpha desyncs badly, Theta may begin drifting faster. The overall system must stay stable, not just individual waves.

**Progressive lock-in:** As CP climbs back from negative, waves lock in from the bottom up — Delta first, then Theta. Each lock-in reduces the player's workload. The player feels the situation improving.

---

## UC4 — Deep Knockout (Full Five-Wave Recovery)

**Context:** Heavy negative CP — all five waves active. Maximum difficulty.

**Player experience:**

- All five wave bands active: Delta through Gamma
- Delta (slots 1/2): slow, predictable — maintained by feel
- Theta (slots 3/4): gentle but needs checking
- Alpha (slots 5/6): moderate — steady attention required
- Beta (slots 7/8): fast oscillation, frequent reversals — demands active tracking
- Gamma (slots 9/10): jittery, erratic — constant small corrections, the hardest band
- The player is managing ten buttons across five wave bands at different frequencies
- This is genuine triage — the player rotates attention, keeps each wave "good enough," and prevents any from fully desyncing
- The hold duration is longest at this depth — 30–60+ seconds of sustained multi-wave management
- Lock-in progression rewards persistence: Delta locks first, then Theta, lightening the load progressively

**What the player feels:** The load is overwhelming at first and gradually becomes manageable as waves lock in. The final moments — just Gamma remaining — feel like breaking the surface after being underwater.

---

## UC5 — Post-Revival State

**Trigger:** Final wave locks in. Consciousness restored.

**Player experience:**

- The abstract brainwave display clears — screen transitions back to the game world
- Battle audio returns to full volume
- Meghan is wherever she fell — the fight has been happening around her
- Her CP is at the **post-revival cap**, determined by knockout depth:

| Knockout Depth | Post-Revival CP Cap |
|---------------|-------------------|
| Shallow | ~50% of normal cap |
| Moderate | ~30–40% of normal cap |
| Deep | ~20–25% of normal cap |

- CP recovers at the **normal recovery rate** toward the post-revival cap — no flat time lockout
- SP may have partially recovered during the downtime
- A revived magical girl still needs MP above the transformation threshold to re-transform
- The existing CP cap degradation model applies on top — the post-revival cap is subject to further degradation from cumulative encounters; only sleep fully resets

**What the player understands:** She's back, but fragile. The reduced cap means a few hits could knock her out again. The fight she wakes up to may have shifted — allies repositioned, enemies advanced, objectives changed. The world did not pause.

---

## UC6 — Skipping the Minigame (Natural Recovery)

**Context:** The player doesn't want to engage with the brainwave minigame — either by preference, because the situation is low-stakes, or for accessibility reasons.

**Player experience:**

- At any point during the brainwave display — including immediately on entry — the player can press the skip input
- The minigame exits; the screen stays in the unconscious perspective but the wave tracking interface is removed
- Meghan recovers naturally: CP climbs from negative toward the post-revival cap at a rate based on her **Meditation skill level** and **base CP recovery rate**
- **Game time continues advancing** — the fight plays out, allies act, the situation evolves
- When CP reaches the post-revival cap, Meghan wakes up automatically and the screen transitions back to the game world

**The tradeoff:** The minigame is always faster than natural recovery. Active wave tracking accelerates the process. Skipping accepts the full time cost — longer unconsciousness, more time allies fight alone, and potentially a lower post-revival cap from extended downtime. But the option is always there.

**Meditation as the equaliser:** The same skill that makes the minigame easier (wider windows, look-ahead, auto-sync) also makes natural recovery faster. A player who invests in Meditation and skips the minigame still recovers reasonably quickly. A player with low Meditation who skips pays a steeper time penalty — but it's never a game over.

---

## UC7 — Ally Knockout (Party Member Down)

**Context:** A party member other than Meghan is knocked out.

**Player experience:**

- The knocked-out ally is visually down in the game world — collapsed, inactive
- The player does **not** play the brainwave minigame for allies — only for Meghan (the player character)
- Ally recovery happens automatically at a rate influenced by the ally's own Meditation skill level
- Deeper knockout = longer auto-recovery, same as the player's mechanic but without the interactive element
- The player can continue fighting, reposition to protect the downed ally, or adjust tactics for the reduced team
- When the ally recovers, they rejoin at their post-revival CP cap with the same depth-based scaling

**Why allies auto-recover:** The brainwave minigame represents Meghan's internal experience of unconsciousness. Allies have the same mechanical system (negative CP, depth-scaled recovery, cap reduction) but the player isn't inside their heads. The minigame is a first-person experience.

---

## Open Items

- Action bar controller mapping during the minigame — the 10 slots need comfortable simultaneous access for rapid switching between wave pairs; needs UX testing
- Exact scroll speeds per wave band — Delta through Gamma frequency ratios need playtesting to find the sweet spot between "brain-wave authentic" and "physically possible to track"
- Exact CP thresholds for progressive wave lock-in during recovery
- Visual design of the unconscious perspective — how abstract, how much battle information bleeds through, whether it changes with story context
- Audio design — muffled battle sounds, heartbeat, wave-tracking audio feedback (tones for sync quality?)
- Whether the brainwave display should show any information about the ongoing fight (ally health? enemy count?) or be purely internal
- Accessibility considerations — the skip option plus Meditation investment provides a non-minigame path; whether additional accessibility options are needed beyond this
- MP knockout (magical girl at zero MP): does this use the same brainwave minigame, or a different visual metaphor representing mana depletion rather than consciousness loss?
- Whether enemy organic knockouts use the same system (enemies attempting brainwave recovery while downed) or simply use a timer
