# Creative Brief — Lock Picking & Restraint Escape

---

## Overview

Lock picking and restraint escape are interconnected systems covering how the player bypasses mechanical locks and escapes from physical restraints. Lock picking uses a **concentric ring minigame** — a physics puzzle where the player guides a bouncing ball through tumbler rings by rotating the rings and their attached blocks. Restraint escape is a multi-stage flow that may require weakening the restraint material, recovering tools, and picking locks depending on what the character is bound with.

Both systems use soft skill gates — any lock is attemptable at any skill level, and any restraint can be worked. Higher skill makes the task more forgiving, not possible.

**Related documents:**

- [Lock Picking](../../mgaPriv/mechanics/skills/lock-picking.md) — concentric ring minigame detail, skill progression, tool requirements
- [Escape Artist](../../mgaPriv/mechanics/skills/escape-artist.md) — restraint escape flow, material branching, tool recovery
- [Binding](../../mgaPriv/mechanics/skills/binding.md) — grapple chain, restraint application, restraint effects
- [Capture and Restraint](../../mgaPriv/mechanics/capture-restraint.md) — offensive/defensive capture, clothing destruction as tool denial
- [CB-clothing](CB-clothing.md) — clothing damage/destruction system (affects tool accessibility)
- [CB-combat](CB-combat.md) — combat system (grapple chain context)
- [Hacking](../../mgaPriv/mechanics/skills/hacking.md) — electronic lock/energy tether bypass (separate from Lock Picking)

---

## Use Cases

### UC1 — Picking a Simple Lock

**Actor:** The player
**Goal:** Learn the concentric ring mechanic by picking a basic mechanical lock
**Trigger:** Player encounters a basic mechanical lock — a padlock on a storage locker, a cheap door lock, a desk drawer

**Step by step:**

1. **The lock opens into the concentric ring minigame.** 2–3 rings displayed, each with a target spot and a few blocks.
2. **A ball bounces inside the innermost ring,** ricocheting off walls and blocks.
3. **The player rotates the rings** to reposition targets and blocks, guiding the ball's bounce path toward each target.
4. **Tap the ring's target and that ring is solved** — it disappears, exposing the next ring.
5. **If the ball misses the target on the current ring, the lock resets** — all solved rings reappear, ball returns to centre.
6. **On a 2–3 ring lock** with few blocks and mostly independent rings, this is a brief puzzle — a few seconds for a skilled player.

**What the player learns:** The core mechanic. Rotate rings, read the bounce, hit the targets in sequence. Reset on miss means precision matters even on easy locks.

---

### UC2 — Picking a Complex Lock (Static Solving)

**Actor:** The player
**Goal:** Pre-plan and execute a full bounce chain through a high-security lock
**Trigger:** Player encounters a high-quality lock — a corporate safe, a secure door, high-security restraints. 5–6 rings with dense blocks and heavy ring interaction.

**Step by step:**

1. **Many rings, many blocks, full interconnection** — rotating one ring shifts blocks on other rings.
2. **The player studies the geometry before letting the ball move,** using the bounce preview (if Lock Picking skill is high enough) to trace the full chain.
3. **On advanced locks, block phasing is active** — blocks phase in and out as rings are solved, changing the geometry at each transition.
4. **The player plans separate ring configurations for each phase,** sets the rings, and lets the ball run.
5. **If the chain completes — all targets hit in sequence — the lock opens.**
6. **If the ball misses at any point, full reset** — back to the initial phase state.

**What the player feels:** A genuine puzzle. The phasing means the solution isn't one static configuration — it's a sequence of configurations. The bounce preview is essential for planning. Solving a complex lock cleanly in one run is deeply satisfying.

---

### UC3 — Picking a Lock Reactively (Dynamic Solving)

**Actor:** The player
**Goal:** Solve a lock through real-time reflexes rather than pre-planning
**Trigger:** Player chooses (or is forced by time pressure) to solve a complex lock reactively instead of statically

**Step by step:**

1. **The ball is moving.** The player watches its trajectory and rotates rings in real-time to intercept the ball with each target.
2. **Less planning, more reflexes** — read the current bounce, rotate the next ring to catch the ball.
3. **When a ring solves and blocks phase,** the player must read the new geometry quickly and adapt.
4. **Mistakes mean reset** — and the phase state returns to the start, so the player can't just retry from where they failed.
5. **This approach is faster when it works but riskier** — one slow reaction means a full reset.

**What the player feels:** A reactive skill challenge rather than a puzzle. The same lock, but a completely different experience from static solving. Players who are good at reading physics and reacting quickly may prefer this approach.

---

### UC4 — Escaping Degradable Restraints (Tape, Rope, Cloth)

**Actor:** The player
**Goal:** Weaken and break restraints made of degradable material through sustained physical effort
**Trigger:** Meghan is restrained with tape, rope, or cloth bindings — no lock to pick

**Step by step:**

1. **Escape Artist assessment tells the player what they're bound with** and (at higher skill) the optimal approach.
2. **The player enters a tension management interaction.** Apply sustained force to degrade the material.
3. **Too much force too fast risks tightening the restraint** — a brief lockout before the player can resume. Steady, measured effort is more efficient than thrashing.
4. **Material determines duration.** Tape/cloth degrades quickly — a few seconds of controlled effort. Rope takes longer — the player may also need to work knots loose (Escape Artist skill reveals which point to target).
5. **Once the material weakens enough, the restraint breaks** and the character is free. No tools required — this is pure physical effort guided by technique.

**What the player understands:** Not all restraints need lock picks. Material matters. The tension management is about patience and control, not button mashing.

---

### UC5 — Escaping Locked Restraints (Handcuffs, Mechanical)

**Actor:** The player
**Goal:** Recover tools and pick the lock on mechanical restraints while the situation continues around Meghan
**Trigger:** Meghan is restrained with handcuffs or mechanical restraints — the material can't be weakened

**Step by step:**

1. **Assessment identifies the restraint type and lock.**
2. **Recover tools.** The player must first access lock picks. Where they're stored matters:
   - **Jacket pocket** — accessible if the jacket is intact; lost if the jacket was destroyed in combat
   - **Boot compartment** — harder for enemies to deny; harder to reach while bound
   - **Hidden wristband** — accessible even with hands behind back; enemy must specifically search to confiscate
3. **If tools are inaccessible** (clothing destroyed, confiscated, wrong storage location for this bind configuration) — the character cannot pick the lock without outside help or finding another approach.
4. **Position tools.** Brief transition — manoeuvre picks to the lock. Hands bound in front = trivial. Hands bound behind = slower, scaled by Escape Artist skill.
5. **Pick lock.** The concentric ring minigame begins, with lock complexity matching the restraint type (3–4 rings for handcuffs, more for advanced restraints).
6. **Throughout all stages, the fight or situation continues** around the character — time spent escaping is time not spent fighting.

**What the player understands:** Preparation matters. Where you store your lock picks is a decision you make before you get captured. An enemy who destroys your clothes before binding you is tactically denying your escape tools. The clothing damage system and the restraint system are mechanically connected.

---

### UC6 — Escaping Electronic Restraints (Energy Tethers)

**Actor:** The player
**Goal:** Identify that Lock Picking does not apply and route to the correct escape method
**Trigger:** Meghan is restrained with energy tethers — electronic restraints that can't be weakened or picked

**Step by step:**

1. **Assessment identifies energy tethers** — Lock Picking does not apply.
2. **The escape path routes through [Hacking](../../mgaPriv/mechanics/skills/hacking.md)** — the player must access the tether's electronic control system.
3. **Alternatively, an ally can disable the tether externally,** or the power source can be destroyed.
4. **If Meghan has no Hacking capability and no ally can reach her,** she cannot escape energy tethers on her own — this is the one restraint type with a potential hard block.

**What the player understands:** Different restraints, different skills. Energy tethers are the high-end restraint — they can't be brute-forced or finessed with picks. Hacking investment or ally support is the answer.

---

### UC7 — Lock Picking Skill Progression (Soft Gate in Action)

**Actor:** The player
**Goal:** Attempt a lock well above current skill level and experience the soft gate system
**Trigger:** Player encounters a lock significantly above their Lock Picking skill

**Step by step:**

1. **The lock opens into the minigame** — it is not blocked or greyed out.
2. **The targets on each ring are tiny** (low skill = small targets).
3. **There is no bounce preview** (or minimal preview at low-mid skill).
4. **All rings are active** — no pre-solved rings.
5. **On advanced locks, block phasing is in effect** with no preview of phase transitions.
6. **The player can still attempt the lock.** The physics are the same. The targets exist. A player with exceptional spatial reasoning and reflexes can chain through every ring.
7. **Most players will fail repeatedly** on locks far above their skill — but each attempt teaches the mechanic and the resets let them try again immediately.

**What the player understands:** Skill doesn't gate access — it gates difficulty. "I can't pick this yet" really means "I can't pick this easily yet." A determined player can punch above their weight. Investing in the skill makes the same lock dramatically easier through larger targets, bounce preview, and pre-solved rings.

---

## Open Items

- Lock picking input mapping — ring rotation and selection need to be comfortable for both deliberate static adjustment and rapid dynamic rotation; needs UX testing on both controller and keyboard
- Whether the ball is always bouncing or launched by the player — always-bouncing creates urgency; player-launched allows static setup time
- Lock pick durability — do picks degrade with use, or are they permanent tools?
- Whether improvised tools (hairpin, wire) function with a mechanical penalty beyond fragility
- Visual/audio design — ring appearance, ball physics feel, target hit/miss feedback, reset feedback, phase transition visual cues
- Block phasing visual language — how does the player know which blocks will phase when the next ring is solved? Transparency, colour coding, preview on hover?
- Whether Engineering skill provides a visible hint about lock structure before the minigame starts
- Tension management minigame detail — exact input scheme for the weakening stage (sustained hold with timing? analog pressure? rhythm?)
- Whether locks can be re-locked after picking (stealth implications)
- How lock picking interacts with the alarm/security system — does a failed attempt (reset) trigger an alarm on some locks?
