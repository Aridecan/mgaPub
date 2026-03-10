# Creative Brief — Grapple Struggle

---

## Overview

The grapple struggle is a **real-time lane-based contest** between two characters. Both sides fire actions into lanes — unmitigated actions push a momentum track toward the attacker's goal (pinning the target) or the defender's goal (escaping the grapple). The same minigame serves both perspectives: when Meghan initiates a grapple, she sees the attacker's side; when she is grabbed, she sees the defender's side.

This replaces the simple contested skill rolls described in [CB-combat UC6](CB-combat.md). The grapple chain (Grab → Grappled → Pinned → Binding) is unchanged — the minigame is the *player experience* of progressing or resisting that chain.

**Actions, not abstract cards.** The grapple actions are real skill-tree unlocks from the Martial Arts and Escape Artist progression trees. Each action has a **state gate** — a set of conditions (Free, Grabbed, Grappled, Pinned, Restrained) that determine when it can be used. If an action doesn't work in the Grappled state, it can't be placed on the Grappled action bar. The grapple action bar has **one slot per lane** (5 slots), each containing a **folder** that opens into the full action bar for that lane. This is the existing folder system (CB-action-bar UC2) adapted for the lane contest.

The core tension: **5 lanes, limited actions, limited SP.** You cannot cover everything. Every action costs SP and has a cooldown. Every undefended lane is an opportunity for your opponent. Choosing which lanes to load with strong actions — and which to leave thin or empty — is a pre-combat loadout decision. Choosing when to fire those actions during the grapple is the real-time contest.

**Related documents:**

- [Combat Creative Brief — UC6](CB-combat.md) — grapple chain definition, binding, SP attrition, third-party intervention
- [Action Bar Creative Brief — UC2, UC4](CB-action-bar.md) — folder system, grapple mode action bar swap
- [Grapple Minigame Notes](../../mgaPriv/mechanics/skills/grapple-minigame.md) — detailed mechanics, action catalog, lane definitions, NPC AI behaviour
- [Binding Skill](../../mgaPriv/mechanics/skills/binding.md) — restraint application, grapple chain stages, restraint effects
- [Martial Arts Skill](../../mgaPriv/mechanics/skills/martial-arts.md) — grapple initiation, offensive action unlocks
- [Escape Artist Skill](../../mgaPriv/mechanics/skills/escape-artist.md) — defensive action unlocks, opposed rolls
- [Hojōjutsu Skill](../../mgaPriv/mechanics/skills/hojojutsu.md) — rope specialisation, binding from Grappled at 51+
- [Restraint Escape Creative Brief](CB-restraint-escape.md) — post-binding escape flow
- [Enemy AI Creative Brief](CB-enemy-ai.md) — NPC pattern adaptation, grappler archetype
- [Capture and Restraint](../../mgaPriv/mechanics/capture-restraint.md) — full capture/restraint mechanics
- [Skill Bar System](../../mgaPriv/mechanics/skill-bar.md) — grapple mode action tables

---

## Use Cases

### UC1 — Playing the Grapple Contest (Attacker)

**Actor:** The player (as grapple initiator)
**Goal:** Advance the grapple state from Grabbed toward Pinned, then apply restraints or maintain control
**Trigger:** Meghan's grab action connects with a target (Phase 1 targeting contest succeeds per CB-combat UC6)

**Step by step:**

1. **Grapple mode activates** — the action bar swaps to grapple mode. The lane contest opens, showing:
   - **5 lanes** between two sides — each lane represents a grapple vector (arm control, leg sweep, body leverage, head/neck control, hip position)
   - **The grapple action bar** — 5 lane slots, one per lane. Each slot is a folder containing the actions the player has loaded for that lane
   - **The opponent's lanes** on the other side — showing their actions as they fire
   - **Momentum track** between them — a horizontal bar showing the current grapple state

2. **The attacker's goal:** push momentum toward Pin. The momentum track has zones:
   ```
   [Escape] ← [Grabbed] ← [Grappled] → [Pinned] → [Bound]
   ```
   The grapple starts in the Grabbed zone. The attacker wants to push right; the defender wants to push left.

3. **Firing actions** — the player selects a lane slot (or opens its folder), then fires an action. The action begins traveling down the lane toward the opponent's side:
   - Each action has an **activation time** (travel speed) — fast actions reach the other side quickly; slow actions take longer but hit harder
   - Each action has an **SP cost** — deducted when fired
   - Each action has a **reset cooldown** — time before it can be fired again
   - Each action has **matchup properties** — what it counters and what counters it
   - Each action can only be **active in one lane at a time** — firing Arm Bar in the Arm Control lane means it's unavailable in other lanes until it resolves and resets

4. **Reading the opponent** — the defender is also firing actions. Their actions travel toward the attacker's side. The player sees incoming actions in their lanes and must decide:
   - Fire a counter-action in that lane to mitigate the incoming attack
   - Accept the hit (let momentum shift toward Escape) and commit actions to other lanes offensively
   - This is the core decision: **every action spent on defense is an action not available for offense**

5. **Unmitigated actions resolve** — an action that reaches the opponent's side without being countered pushes the momentum track:
   - Attacker's unmitigated action → momentum shifts toward Pinned
   - The amount of shift depends on the action's momentum value and **lane affinity** — an arm bar in the Arm Control lane pushes more than an arm bar in the Hip Position lane
   - Quick, cheap actions push a little; expensive, slow actions push a lot

6. **Action-on-action resolution** — when an attacking action meets a defending action in the same lane:
   - If the defending action counters the attack type: attack is fully mitigated, defender may get a momentum pushback
   - If the actions are neutral matchup: both are consumed, minimal momentum change
   - If the attacking action beats the defense type: attack is partially mitigated but still pushes some momentum

7. **Loadout matters** — the player's pre-configured grapple action bar determines what's available in each lane. A lane loaded with strong, well-matched actions is hard to penetrate. A lane with a single basic action (like a punch) is thin — but it's not empty. Even a weak action can slow down an incoming attack, or force the opponent to waste a strong action defending an otherwise empty lane. This is a **bluffing tool** — the opponent doesn't know which lanes are loaded heavy and which are loaded light until actions start firing.

**What the player experiences:** Initiating a grapple opens a real-time tactical contest shaped by a loadout the player configured before the fight. The player reads 5 lanes, decides where to attack and where to defend, and manages action cooldowns and SP. A lane loaded with an arm bar, a wrist lock, and a quick grab is a serious threat in Arm Control — but it means fewer actions available for other lanes. The grapple rewards preparation (smart loadout) and execution (reading the opponent in real time).

---

### UC2 — Defending and Escaping the Grapple (Defender)

**Actor:** The player (as grapple target)
**Goal:** Push momentum toward Escape and break free from the grapple
**Trigger:** An enemy's grab action connects with Meghan

**Step by step:**

1. **Grapple mode activates** — same lane contest, mirrored perspective. The player sees the defender's side. The grapple action bar shows the defender's lane loadout. The momentum track shows Grabbed state.

2. **The defender's goal:** push momentum toward Escape. Reaching the Escape end of the track breaks the grapple — Meghan is free and can act normally.

3. **Defender's action mix** — the defender's available actions come from the Escape Artist skill tree (and some Martial Arts actions that have Grabbed/Grappled state gates):
   - More **escape-oriented** actions: hip escape, arm slip, reversal, shrimp, frame
   - Fewer **advance-oriented** actions: the defender can push momentum back but it's harder to reach Pin from the defender's side
   - Defender's actions generally favor **speed and disruption** — fast activation, good at intercepting, moderate pushback
   - Attacker's actions generally favor **momentum and control** — slower but more momentum per unmitigated hit

4. **Reactive play** — the defender sees the attacker's actions coming and fires counters. The key defensive decisions:
   - Which lanes to defend (can't cover all 5 with limited actions and SP)
   - When to play offensively (firing actions toward the attacker to push momentum back toward Escape)
   - When to accept a hit to save actions for a stronger counter-push

5. **Counter-grapple** — if the defender pushes momentum far enough past Grabbed, the roles can reverse. The former defender becomes the dominant grappler. This requires sustained offensive play from the defender — not just blocking, but actively pushing back. Martial Arts skill level determines whether reversal actions are available.

6. **Escape resolution** — momentum reaches the Escape threshold → the grapple breaks. Meghan is free. Both characters return to normal combat. There is a brief recovery window after escape where both sides are momentarily vulnerable.

**What the player experiences:** Being grabbed is not a passive experience. The player immediately enters the same lane contest, but from the other side. The initial instinct is pure defense — block everything — but that only maintains the current state. To actually escape, the player must go on offense, pushing actions into the attacker's lanes to shift momentum. The reversal possibility makes the grapple genuinely two-directional — a weak grappler who grabs a strong target may find themselves pinned instead.

---

### UC3 — Momentum and State Transitions

**Actor:** The system
**Goal:** Translate lane contest results into grapple state changes that affect both sides' available actions
**Trigger:** Momentum track crosses a zone threshold during the grapple contest

**Step by step:**

1. **The momentum track** is a continuous bar divided into zones:
   ```
   [Escape] | [Grabbed] | [Grappled] | [Pinned] | [Bound]
   ```
   The grapple starts at the Grabbed/Grappled boundary. Momentum is pushed left (toward Escape) or right (toward Pinned) by unmitigated actions.

2. **Zone transitions** — when momentum crosses into a new zone:
   - **Visual/audio indicator:** both sides see the state change. The UI updates to reflect the new zone.
   - **Action availability shifts:** each grapple action has state gates (checkboxes for Grabbed, Grappled, Pinned, etc.). Entering a new zone activates or deactivates actions based on their gates:
     - Grabbed: all Grabbed-gated actions available for both sides
     - Grappled: actions gated to Grabbed-only become unavailable; Grappled-gated actions activate. Defender loses some mobility-based actions; attacker gains binding-attempt actions at penalty (Hojōjutsu 51+ only)
     - Pinned: Pinned-gated actions activate. Defender's action set narrows significantly (limited to escape attempts). Attacker gains the Bind action (requires restraint item + Binding skill)
   - **The grapple action bar updates in real time** — folders reflect which actions are currently available. An action that loses its state gate greys out or disappears from the folder.
   - **Momentum does not reset** between zones. Crossing from Grappled to Pinned doesn't reset the track — the attacker has earned that position and maintains it unless the defender pushes back.

3. **Escape threshold** — momentum reaches the left end:
   - Grapple breaks immediately
   - Both characters return to normal combat state
   - Brief recovery window (both sides momentarily in recovery animation)
   - The former defender may have a counterattack opportunity if they pushed through with force (critical escape)

4. **Pin threshold** — momentum reaches the right end:
   - Target is Pinned. The contest continues but the defender's options are severely limited
   - The attacker can choose to:
     - **Maintain Pin** — continue firing actions to keep momentum at Pinned
     - **Attempt Binding** — fire the Bind action (high activation time, high SP cost, requires restraint item in hand). If it reaches the other side unmitigated, the restraint is applied (Binding skill check per binding.md)
     - **Strike** — reduced attacks are possible while maintaining a pin
     - **Release** — voluntarily end the grapple

5. **Bound threshold** — if binding succeeds:
   - The grapple contest ends
   - The target enters the restrained state (per binding.md restraint effects)
   - The target's options shift to the restraint escape flow (CB-restraint-escape) — a different system entirely
   - Multiple restraints can be applied by re-entering the grapple contest or applying additional bindings while the target is already partially restrained

6. **Reversal** — if the defender pushes momentum far enough past the starting point:
   - Roles swap. The former attacker is now being controlled
   - Action sets swap accordingly (the player's loadout switches to the appropriate attacker/defender profile)
   - This represents the defender gaining dominant position — flipping the grapple
   - The momentum track continues from the new position; it does not reset

**What the player experiences:** State transitions are visible and meaningful. When the zone shifts to Grappled, some of the defender's actions grey out — the situation is getting worse. When it shifts to Pinned, most defender actions disappear — it's desperate. The action bar updates in real time so the player always knows what they can and can't do. Getting reversed feels dramatic — the UI flips, the loadout changes, and suddenly you're fighting from the other side.

---

### UC4 — Action Properties, Loadout, and Skill Integration

**Actor:** The player / the system
**Goal:** Ensure the grapple loadout is a meaningful pre-combat decision and character skills affect the contest without replacing player choices
**Trigger:** Configuring the grapple action bar (pre-combat) and entering any grapple contest (during combat)

**Step by step:**

1. **Grapple actions are skill-tree unlocks** — as the player progresses Martial Arts and Escape Artist, new grapple actions become available:
   - Martial Arts tree: offensive grapple actions (arm bar, power drive, mount advance, leg sweep, body press, etc.)
   - Escape Artist tree: defensive grapple actions (hip escape, arm slip, shrimp, bridge, reversal, frame, etc.)
   - Some actions appear in both trees or require levels in both (e.g., Counter-Grapple requires Martial Arts 30+ and Escape Artist 20+)
   - Higher-tier unlocks are more powerful: faster, more momentum, better counter matchups

2. **State gates** — each action has a checkbox set defining which grapple states it works in:

   | Gate | Meaning |
   |------|---------|
   | **Grabbed** | Usable when in the Grabbed zone |
   | **Grappled** | Usable when in the Grappled zone |
   | **Pinned** | Usable when in the Pinned zone |
   | **Restrained** | Usable while partially restrained (hands bound front, etc.) |

   An action with [Grabbed, Grappled] gates can be placed on the Grabbed and Grappled action bars but not the Pinned bar. The system enforces this during loadout configuration — you cannot place an action where its gates don't allow it.

   **Not limited to combat actions.** Any action with the appropriate state gates can go on the grapple bar — combat or non-combat. Non-combat actions useful during grapple include Intimidate (pressure morale while struggling), Taunt (disrupt the opponent's focus), Search (feel for hidden items or tools on the opponent's body), or social skills that make sense in close contact. The state gate is the only filter — if an action has a Grappled gate, it belongs on the Grappled bar regardless of its source skill tree.

3. **The grapple action bar** — 5 slots (one per lane), each containing a folder:
   - The player configures this via the phone interface, same as the regular action bar (CB-action-bar UC3)
   - Each lane slot opens into a folder with the full action bar (up to 10 slots deep, or nested folders)
   - Actions placed in a lane folder are the actions available for that lane during the grapple
   - **The same action can be placed in multiple lane folders** — but it can only be **active in one lane at a time**. Firing Arm Bar in lane 1 means it's on cooldown in all lanes until it resets.
   - Separate loadout profiles exist for attacker and defender (the player may face either role)

4. **Lane affinity** — actions work in any lane, but each action has a lane affinity bonus:
   - An Arm Bar in the Arm Control lane gets a momentum bonus (it's in its natural lane)
   - An Arm Bar in the Hip Position lane still works — it just pushes less momentum
   - This creates a loadout optimisation puzzle: put actions in their natural lanes for maximum efficiency, or spread them across lanes for coverage
   - **Bluffing with weak actions:** loading a basic Punch into a lane gives it a presence. The opponent doesn't know the lane is thin until they test it. A Punch in an otherwise empty lane can:
     - Slow down an unmitigated attack slightly (better than nothing)
     - Force the opponent to spend a strong action defending that lane (wasting their resources)
     - Create uncertainty about the player's actual loadout

5. **Skill effects on action stats:**

   | Skill | Effect |
   |-------|--------|
   | **Martial Arts** | Offensive action quality: faster activation, more momentum per hit, shorter cooldowns |
   | **Escape Artist** | Defensive action quality: faster counter-activation, more pushback on successful defense |
   | **STR** | Force-type actions (Power Drive, Body Press, etc.) gain raw momentum bonus |
   | **AGI** | Technique-type actions (Arm Slip, Redirection, etc.) gain speed bonus |

6. **Tell system** — at higher skill levels, the player receives advance information about the opponent's plays:
   - Skill 21–50: brief lane highlight when the opponent targets a lane (~0.5s before their action launches)
   - Skill 51–80: consistent highlight with enough time to react (~1s preview of target lane and action type)
   - Skill 81–100: full preview of the opponent's action choice and target lane before it launches
   - This creates the **act/react** dynamic — skilled grapplers read their opponent's intent and counter precisely; low-skill grapplers are guessing

7. **NPC action quality mirrors NPC skills** — an NPC with Martial Arts 60 has strong offensive actions; an NPC with Escape Artist 30 has weak defensive actions. The NPC's loadout is determined by its skill set — same rules as the player.

**What the player experiences:** The grapple loadout is a strategic decision made before the fight. "Do I load heavy in Arm Control and Body Leverage, or spread thin across all 5 lanes?" Once in the grapple, skills and loadout combine — high Martial Arts gives better actions, but the player chooses where to use them. A lane loaded with a single Punch is still useful as a bluff or last-ditch defense. The tell system rewards skill investment: a master grappler reads the opponent's intent and counters precisely; a novice is reacting to what's already in flight.

---

### UC5 — SP Attrition and Exhaustion

**Actor:** The system / the player
**Goal:** Create natural grapple duration through SP as the limiting resource
**Trigger:** SP expenditure during any grapple contest

**Step by step:**

1. **Every action costs SP.** The cost varies by action type:
   - Quick, light actions: low SP cost
   - Heavy power actions: high SP cost
   - Defensive counters: moderate SP cost
   - This creates the fundamental resource tension: aggressive play burns SP fast; conservative play conserves SP but concedes lanes

2. **Passive SP drain** — both participants lose SP at a base rate just from maintaining the grapple. Being locked in physical contact is exhausting regardless of specific actions. This prevents infinite stalling — even doing nothing costs SP.

3. **SP reaches zero:**
   - The character can no longer fire new actions
   - Any actions already in flight continue to resolve
   - All lanes are undefended — incoming actions push momentum freely
   - **SP=0 for the defender:** rapid momentum push toward Pin. The defender cannot resist
   - **SP=0 for the attacker:** momentum drifts back toward Escape. The attacker cannot maintain the hold
   - **Both at SP=0:** slow drift toward Escape (gravity favors the defender — holding someone requires sustained effort, letting go does not)

4. **SP as the clock** — grapples don't last forever. The SP pool determines maximum grapple duration. A character who spent SP fighting before the grapple starts with less — grappling a tired opponent is strategically advantageous.

5. **SP does not recover during the grapple.** The only way to get SP back is to break the grapple (escape or release) and recover through normal means. This prevents indefinite grapple cycles.

**What the player experiences:** SP pressure makes every decision matter. Playing expensive actions early can secure a quick pin — but if the opponent counters, you've burned SP for nothing. A grapple that goes long favors whoever entered with more SP — pre-grapple combat matters.

---

### UC6 — Third-Party Intervention and Special Actions

**Actor:** The player / NPCs / the system
**Goal:** Handle outside interference and content-tier-specific actions during the grapple contest
**Trigger:** A third character intervenes, or a special action is initiated during the grapple

**Step by step:**

1. **Third-party attack on either participant:**
   - Another character strikes one of the grappling pair
   - The struck participant suffers a **disruption**: their currently in-flight actions are cancelled, they lose a brief window of action play (stunned), and they take the attack's damage normally
   - The other participant is unaffected and can exploit the disruption
   - A well-timed ally attack can create an opening for escape or a decisive pin push

2. **Third-party assist:**
   - An ally can assist one participant: temporarily adds bonus actions to that participant's available pool, or applies a lane block on the opponent's side (reducing their available lanes temporarily)
   - Assist actions cost the ally's SP

3. **Third-party grapple break:**
   - A strong enough attack or a dedicated break action can forcibly separate the grappling pair
   - Both participants exit the grapple in a recovery state
   - Momentum resets — the grapple must be re-initiated from scratch

4. **Rending (Spicy content):**
   - During grapple, an attacker with the Rending skill can fire **Rend actions** that target clothing instead of grapple momentum
   - Rend actions travel down lanes like normal actions
   - If unmitigated, they deal clothing damage (per CB-clothing) — durability reduction to specific garments
   - Rend actions are contested by the defender's **Wardrobe Warding** (if active) rather than standard grapple defense
   - Rending does not advance grapple state — it is a parallel objective (strip defenses vs advance control)
   - Tactical choice for the attacker: spend actions on Rending (clothing damage, tool denial) or on momentum (advance toward Pin)

5. **Binding attempt (from Pinned state):**
   - When the attacker has pushed momentum to the Pinned zone, the **Bind action** becomes available (requires restraint item + Binding skill)
   - The Bind action has high activation time (slow — the defender sees it coming) and high SP cost
   - If it reaches the defender's side unmitigated: restraint application begins (Binding skill check per binding.md)
   - If the defender counters: binding fails, SP wasted, restraint item not consumed
   - **Binding from Grappled state** (Hojōjutsu 51+ only): Bind action available earlier but with even higher activation time and SP cost

6. **Submit action:**
   - The defender can choose to **Submit** at any time, voluntarily ending the contest
   - Momentum immediately moves to the attacker's current goal state
   - Saves remaining SP for potential restraint escape rather than burning it in a losing grapple
   - Submit is always available; it cannot be forced

**What the player experiences:** Grapples don't happen in a vacuum. Ally intervention creates real openings. Rending adds menace to Spicy encounters. The Bind action is the grapple's endgame — seeing it come down the lane when you're Pinned with limited actions is tense. Submit is a tactical retreat, not a defeat.

---

## Open Items

- Exact lane count (5 is the working number — needs playtesting for feel and readability)
- Lane definitions — what each lane physically represents (arm control, leg sweep, body leverage, head/neck control, hip position are examples; may need adjustment)
- Full action catalog — which Martial Arts and Escape Artist skill-tree unlocks are grapple actions, their state gates, stats, and lane affinities. Needs design + playtesting. See [grapple-minigame.md](../../mgaPriv/mechanics/skills/grapple-minigame.md) for initial examples
- Grapple action bar folder depth — is one folder per lane enough, or should nested folders be supported?
- Visual representation: abstract (action icons in lane UI) vs in-world (camera showing characters with overlay) vs hybrid
- Animation integration — do in-world character animations reflect which lanes are being contested?
- Audio design — grunting, struggling, impact sounds keyed to action resolution
- Whether the minigame is skippable (auto-resolve via skill roll for players who prefer it — accessibility option)
- Exact SP costs per action type and passive drain rate
- Exact momentum values per action type at each skill tier
- Lane affinity bonus/penalty values (how much better is an action in its natural lane vs elsewhere?)
- How Hojōjutsu integrates beyond the Bind action (rope-specific grapple actions?)
- Duration tuning — target grapple duration in seconds (15–30 second range expected; needs playtesting)
- Whether grapple momentum carries over if a third party breaks and the grapple is re-initiated
- Whether environmental factors affect the grapple (near ledge = throw available? slippery ground = sweep bonus?)
- Training integration — grapple practice with Terry using the lane system (CB-training)
- NPC grapple loadout generation — how does the AI build its lane loadout from its skill set?
- Default grapple loadout — what does the player start with before customizing? Auto-populated from available actions?
