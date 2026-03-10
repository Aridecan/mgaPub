# Creative Brief — Restraint Escape

---

## Overview

Escaping restraints is a physical puzzle. The player's body is the tool. The core loop is **position your bound limbs, then apply a sharp impulse** — and what that achieves depends on what you are bound with. Deformable restraints (tape, rope) weaken and break. Rigid restraints (leather, steel) do not break — the player must reach the fastener and undo it.

This is not a reflex challenge. It is a problem-solving exercise about leverage, reach, and body mechanics, with the Escape Artist skill providing forgiveness on execution and efficiency on results.

**Related documents:**

- [Escape Artist Skill](../../mgaPriv/mechanics/skills/escape-artist.md) — full escape flow, skill progression, material table, tool accessibility
- [Restraint Escape Minigame Notes](../../mgaPriv/mechanics/skills/restraint-escape-minigame.md) — detailed mechanics, SP costs, environmental tools
- [Lock Picking Creative Brief](CB-lock-picking.md) — concentric ring minigame (used for locked restraints)
- [Hacking Creative Brief](CB-hacking.md) — the Cylinder (used for energy tethers)
- [Combat Creative Brief — UC6](CB-combat.md) — grapple chain, binding application
- [Failure Pipeline Creative Brief](CB-failure-pipeline.md) — capture scenarios, pipeline escape
- [Clothing Creative Brief](CB-clothing.md) — clothing damage as tool-denial strategy
- [Capture and Restraint](../../mgaPriv/mechanics/capture-restraint.md) — restraint mechanics, cursed items

---

## Use Cases

### UC1 — Escaping Deformable Restraints (Tape, Rope, Cloth)

**Actor:** The player
**Goal:** Break free from restraints that can be weakened through physical force
**Trigger:** Meghan is bound with a deformable material and attempts to escape

**Step by step:**

1. **Assess** — automatic. Escape Artist skill determines how much information the player receives about the restraint:
   - Skill 1–10: material type only ("this is rope")
   - Skill 11–25: material + weak points vaguely indicated
   - Skill 26–50: difficulty estimate + optimal approach highlighted
   - Skill 51–75: full detail including weak points and estimated effort
   - Skill 76–100: complete assessment with time estimate

2. **Position** — the player selects a position for their bound limbs. The goal is to create maximum leverage against the weakest point of the material:
   - Where the tape overlaps least
   - Where the rope wraps are loosest
   - Where the cloth fibres are thinnest
   - **Escape Artist effect:** low skill = narrow positioning window (must find the correct position precisely); high skill = wider window (approximate positioning still works — the character's technique compensates)

3. **Impulse** — the player triggers a sharp burst of force in the positioned direction. A single input, not sustained pressure:
   - **Correct position + good timing:** full effect — the restraint takes maximum stress
   - **Correct position + poor timing:** reduced effect
   - **Wrong position:** minimal or no effect — SP spent, but no progress
   - **Escape Artist effect on timing:** low skill = narrow timing window; high skill = wide timing window with more force per impulse

4. **SP cost** — each position-and-impulse cycle costs a flat amount of SP, regardless of whether it succeeded. Escaping is physically exhausting
   - **No CP cost** from normal restraints — straining, not injured
   - **Exception:** special restraints (taser collar, shock cuffs) may inflict CP per attempt

5. **Integrity reduction** — a successful impulse reduces the restraint's structural integrity. The amount depends on:
   - STR (raw force)
   - Escape Artist (technique multiplier)
   - Position quality (correct vs approximate vs wrong)

6. **Repeat** until the restraint's integrity reaches zero and it breaks, or SP runs out and the player must rest before continuing

7. **Material pacing:**
   - Tape / cloth: low integrity — a few successful impulses
   - Rope: moderate — more cycles, knot identification matters
   - Cable ties: high — impulse is inefficient; better escaped via high-STR snap or cutting tool
   - Leather: very high — impractical to weaken through force alone; seek the buckle instead (UC2)

**What the player experiences:** Escaping tape is quick — a couple of well-positioned pulls and it gives. Rope takes longer and rewards reading the knot work (which wrap is loosest?). Leather barely budges — the player quickly learns to look for the buckle rather than trying to brute-force it. SP management matters: thrashing wildly with wrong positions burns SP for nothing. Deliberate, correct positioning is faster and cheaper.

---

### UC2 — Escaping Rigid Restraints (Buckles, Latches)

**Actor:** The player
**Goal:** Reach and undo a fastener on a rigid restraint that cannot be weakened through force
**Trigger:** Meghan is bound with leather cuffs, straps, or similar restraints that have a buckle or latch

**Step by step:**

1. **Assess** — same as UC1. Escape Artist skill reveals the fastener type and location

2. **Position to reach the fastener** — the player must arrange their bound limbs to bring their fingers within reach of the buckle or latch. This is a reach/dexterity puzzle:
   - **Hands bound in front, wrist cuffs:** fingers can reach the mechanism relatively easily. Fewer positioning steps
   - **Hands bound behind, wrist cuffs:** must rotate wrists and extend fingers to reach by feel. More steps, harder positions
   - **Multiple restraints:** must be freed in a viable order — cannot reach ankle buckles if hands are bound behind the back
   - **Escape Artist effect:** low skill = must find exact correct position; high skill = wider window, character finds the reach naturally

3. **Undo the fastener** — once the player's fingers reach the buckle or latch:
   - AGI check to undo it. Escape Artist skill widens the success window
   - Quick resolution — no sub-minigame. The positioning was the puzzle; undoing the buckle is the payoff

4. **SP cost** — positioning attempts cost SP (same flat cost as UC1). The AGI check to undo the buckle is a single action, not a sustained drain

5. **The player must be able to access the fastener.** If the restraint is configured so the fastener is physically unreachable (buckle is behind the back and hands are bound in front, pointing away), the player needs an alternative:
   - Environmental tool (hook, edge to catch the strap)
   - Outside help (ally reaches the buckle)
   - Weaken the material instead (slow, but possible for leather — UC1)

**What the player experiences:** Rigid restraints are a different puzzle than deformable ones. The player is not trying to break anything — they are trying to reach a specific point on their body. Hands behind the back makes everything harder. The player learns to think about their body spatially: "if I rotate my left wrist and extend my index finger, can I reach the buckle on the right cuff?" AGI and Escape Artist determine whether the reach works. The moment the buckle clicks open is satisfying because the player solved a spatial problem, not a timing challenge.

---

### UC3 — Escaping Locked Restraints (Handcuffs, Mechanical)

**Actor:** The player
**Goal:** Pick the lock on a rigid restraint, managing SP drain from holding an awkward position
**Trigger:** Meghan is bound with locked restraints and has access to lock picks

**Step by step:**

1. **Assess** — same assessment as UC1/UC2. Reveals lock type and complexity

2. **Recover tools** — lock picks must be physically accessible. Where the player stored them before capture determines availability:
   - Jacket pocket: accessible if garment intact; lost if garment was destroyed
   - Boot compartment: hardest for enemies to find; hardest to reach while bound
   - Hidden wristband: accessible even with hands behind back; enemy must specifically search to confiscate
   - Tools confiscated: no picks available — must find another approach or wait for outside help

3. **Position to reach the lock** — same positioning mechanic as UC2, but the goal is to bring lock picks to the lock mechanism:
   - Hands in front + wrist cuffs: comfortable reach, minimal difficulty
   - Hands behind + wrist cuffs: must work picks blind, by feel. More positioning steps, harder

4. **Transition to lock-picking minigame** — once positioned, the concentric ring lock-picking minigame begins (see [CB-lock-picking](CB-lock-picking.md)). The lock's complexity is determined by the restraint:
   - Standard handcuffs: 3–4 rings
   - Quality handcuffs: 4–5 rings
   - Mechanical restraints: 4–6 rings
   - High-security restraints: 6+ rings

5. **SP drain during lock-picking** — while the lock-picking minigame is active, the player is holding an awkward position to keep their fingers on the lock. SP drains continuously:
   - **Drain rate depends on position stability:**
     - Hands in front, comfortable reach: slow drain
     - Hands behind back, contorted: fast drain
   - **Escape Artist effect:** higher skill = more stable hold = slower drain rate. A master can hold an awkward position almost indefinitely

6. **SP reaches zero → lock-picking stops, puzzle does not reset:**
   - The character can no longer hold the position — the minigame pauses
   - **Tumblers stay where they are.** All progress on the concentric ring puzzle is preserved
   - The player must recover SP (rest, wait, receive aid)
   - When ready, re-enter the positioning stage (costs the standard positioning SP)
   - The lock-picking puzzle resumes exactly where it left off
   - This creates natural cycles: position → pick for a while → SP runs out → rest → reposition → continue

7. **Lock picked → restraint opens.** The cuff/mechanism releases. If multiple restraints are applied, each must be escaped separately (but with hands free, remaining restraints are typically easier)

**What the player experiences:** Locked restraints are a multi-system challenge. First, did you plan ahead — are your picks accessible? Then, can you reach the lock? Then, can you pick it before your SP runs out? The lock-picking puzzle itself is familiar (same concentric ring system), but the SP pressure makes it tense — you are racing your own stamina, not a timer. Running out of SP is frustrating but not devastating: the progress is saved. You catch your breath, get back into position, and continue. A high Escape Artist character holds the position longer and picks faster; a low-skill character does it in more cycles. Either way, you eventually get free — the question is how long it takes and how much SP it costs.

---

### UC4 — Escaping Energy Tethers (Electronic Restraints)

**Actor:** The player
**Goal:** Disable electronic restraints through hacking rather than physical force
**Trigger:** Meghan is bound with energy tethers and has access to hacking tools or capability

**Step by step:**

1. **Assess** — Escape Artist skill reveals the restraint type. Energy tethers are identified as electronic — physical weakening is not viable

2. **Access the hacking interface** — the player must reach the tether's control module:
   - If hands are free enough to reach a connected terminal or the tether's access port → proceed
   - If fully immobilised → requires external access (ally hacks the system, or the player reaches a terminal through positioning)

3. **Transition to hacking minigame** — the Cylinder hacking minigame begins (see [CB-hacking](CB-hacking.md)). The tether's security level determines puzzle complexity

4. **SP drain** applies the same way as UC3 if the player is holding an awkward position to maintain access. Same rules: SP zero pauses the puzzle, progress preserved, resume after recovery

5. **Hack complete → tether deactivates.** The restraint powers down and releases

**What the player experiences:** Energy tethers cannot be forced. The player recognises the material type and switches to a technical solution. If they have Hacking skill and access, it is a solvable problem. If they do not, they need help — or they need to reach a terminal, which is its own spatial puzzle while restrained.

---

### UC5 — Environmental Aid During Escape

**Actor:** The player
**Goal:** Use features of the environment to assist with restraint escape
**Trigger:** Meghan is restrained and within reach of a useful environmental feature

**Step by step:**

1. The player identifies an environmental feature that could assist escape:
   - **Sharp edge** (broken furniture, rough wall, exposed bolt): can rub deformable restraints against it. Acts as a force multiplier — faster integrity degradation per impulse
   - **Narrow gap** (pipe, railing, door frame): can wedge restraint into the gap and lever against it — alternative leverage point for the impulse
   - **Heat source** (radiator, exposed pipe, electrical element): some deformable materials weaken faster when warm (tape adhesive softens, rope fibres become brittle)
   - **Hook or protrusion:** can catch a strap or buckle, providing leverage to undo a fastener that fingers alone cannot reach

2. **Reaching the feature** is a positioning challenge — the player must move their restrained body to the feature's location. Movement while restrained is limited (shuffling, rolling, dragging)

3. **Using the feature** modifies the escape loop:
   - For weakening: the environmental tool acts as a multiplier on impulse effectiveness. Fewer cycles needed
   - For fastener access: the feature provides an alternative reach path when fingers alone cannot get there
   - **Escape Artist skill** affects how efficiently the player uses environmental tools — a high-skill character identifies the best tool quickly and uses it effectively

4. **Discovery:** Perception and Search skills help identify usable environmental features. A high-Perception character notices the exposed bolt behind them; a low-Perception character might not

**What the player experiences:** The environment is not decoration — it is a toolbox. A sharp edge behind the chair makes rope escape faster. A pipe under the table provides leverage for a buckle that fingers cannot reach. The player learns to scan the environment when restrained, looking for anything that helps. This rewards spatial awareness and connects to the broader signal parity principle — the player reads the same environment the AI reads.

---

## Open Items

- Visual representation of the positioning puzzle — abstract UI (directional indicators, body silhouette) or in-world camera showing Meghan's body position
- Exact SP costs per positioning attempt and per impulse — needs playtesting
- SP drain rates during lock-picking at various position difficulties — needs tuning
- Structural integrity values per material type — needs playtesting for pacing
- Whether partially weakened restraints retain degradation if the character is re-restrained with the same material
- Whether enemies observe escape attempts and intervene (re-tighten, add restraints, punish)
- Sound design — do impulses make noise? Can NPCs hear escape attempts? (Stealth interaction)
- Whether cursed restraints (magical girl content) have unique escape mechanics beyond CP damage
- Tool confiscation mechanics — under what circumstances do enemies search for and remove hidden tools?
- Whether the positioning puzzle changes based on what Meghan is wearing (more clothing = more padding = different leverage points?)
