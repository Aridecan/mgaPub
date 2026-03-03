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

## UC1 — Picking a Simple Lock

**Context:** The player encounters a basic mechanical lock — a padlock on a storage locker, a cheap door lock, a desk drawer.

**Player experience:**

- The lock opens into the concentric ring minigame: 2–3 rings displayed, each with a target spot and a few blocks
- A ball bounces inside the innermost ring, ricocheting off walls and blocks
- The player rotates the rings to reposition targets and blocks, guiding the ball's bounce path toward each target
- **Tap** the ring's target and that ring is solved — it disappears, exposing the next ring
- If the ball misses the target on the current ring, the lock **resets** — all solved rings reappear, ball returns to centre
- On a 2–3 ring lock with few blocks and mostly independent rings, this is a brief puzzle — a few seconds for a skilled player

**What the player learns:** The core mechanic. Rotate rings, read the bounce, hit the targets in sequence. Reset on miss means precision matters even on easy locks.

---

## UC2 — Picking a Complex Lock (Static Solving)

**Context:** The player encounters a high-quality lock — a corporate safe, a secure door, high-security restraints. 5–6 rings with dense blocks and heavy ring interaction.

**Player experience:**

- Many rings, many blocks, full interconnection — rotating one ring shifts blocks on other rings
- The player studies the geometry before letting the ball move, using the **bounce preview** (if Lock Picking skill is high enough) to trace the full chain
- On advanced locks, **block phasing** is active — blocks phase in and out as rings are solved, changing the geometry at each transition
- The player plans separate ring configurations for each phase, sets the rings, and lets the ball run
- If the chain completes — all targets hit in sequence — the lock opens
- If the ball misses at any point, full reset — back to the initial phase state

**What the player feels:** A genuine puzzle. The phasing means the solution isn't one static configuration — it's a sequence of configurations. The bounce preview is essential for planning. Solving a complex lock cleanly in one run is deeply satisfying.

---

## UC3 — Picking a Lock Reactively (Dynamic Solving)

**Context:** Same complex lock as UC2, but the player uses a reactive approach instead of pre-planning.

**Player experience:**

- The ball is moving. The player watches its trajectory and rotates rings in real-time to intercept the ball with each target
- Less planning, more reflexes — read the current bounce, rotate the next ring to catch the ball
- When a ring solves and blocks phase, the player must read the new geometry quickly and adapt
- Mistakes mean reset — and the phase state returns to the start, so the player can't just retry from where they failed
- This approach is faster when it works but riskier — one slow reaction means a full reset

**What the player feels:** A reactive skill challenge rather than a puzzle. The same lock, but a completely different experience from static solving. Players who are good at reading physics and reacting quickly may prefer this approach.

---

## UC4 — Escaping Degradable Restraints (Tape, Rope, Cloth)

**Context:** Meghan is restrained with tape, rope, or cloth bindings. No lock to pick — the escape path is weakening the material.

**Player experience:**

- Escape Artist assessment tells the player what they're bound with and (at higher skill) the optimal approach
- The player enters a **tension management** interaction: apply sustained force to degrade the material
- Too much force too fast risks tightening the restraint — a brief lockout before the player can resume
- Steady, measured effort is more efficient than thrashing
- **Tape/cloth** degrades quickly — a few seconds of controlled effort
- **Rope** takes longer — the player may also need to work knots loose (Escape Artist skill reveals which point to target)
- Once the material weakens enough, the restraint breaks and the character is free
- No tools required — this is pure physical effort guided by technique

**What the player understands:** Not all restraints need lock picks. Material matters. The tension management is about patience and control, not button mashing.

---

## UC5 — Escaping Locked Restraints (Handcuffs, Mechanical)

**Context:** Meghan is restrained with handcuffs or mechanical restraints. The material can't be weakened — she needs to pick the lock.

**Player experience:**

- Assessment identifies the restraint type and lock
- **Recover tools:** The player must first access lock picks. Where they're stored matters:
  - Jacket pocket — accessible if the jacket is intact; **lost if the jacket was destroyed in combat**
  - Boot compartment — harder for enemies to deny; harder to reach while bound
  - Hidden wristband — accessible even with hands behind back; enemy must specifically search to confiscate
- **If tools are inaccessible** (clothing destroyed, confiscated, wrong storage location for this bind configuration) — the character cannot pick the lock without outside help or finding another approach
- **Position tools:** Brief transition — manoeuvre picks to the lock. Hands bound in front = trivial. Hands bound behind = slower, scaled by Escape Artist skill.
- **Pick lock:** The concentric ring minigame begins, with lock complexity matching the restraint type (3–4 rings for handcuffs, more for advanced restraints)
- Throughout all stages, the fight or situation continues around the character — time spent escaping is time not spent fighting

**What the player understands:** Preparation matters. Where you store your lock picks is a decision you make before you get captured. An enemy who destroys your clothes before binding you is tactically denying your escape tools. The clothing damage system and the restraint system are mechanically connected.

---

## UC6 — Escaping Electronic Restraints (Energy Tethers)

**Context:** Meghan is restrained with energy tethers — electronic restraints that can't be weakened or picked.

**Player experience:**

- Assessment identifies energy tethers — Lock Picking does not apply
- The escape path routes through [Hacking](../../mgaPriv/mechanics/skills/hacking.md) — the player must access the tether's electronic control system
- Alternatively, an ally can disable the tether externally, or the power source can be destroyed
- If Meghan has no Hacking capability and no ally can reach her, she cannot escape energy tethers on her own — this is the one restraint type with a potential hard block

**What the player understands:** Different restraints, different skills. Energy tethers are the high-end restraint — they can't be brute-forced or finessed with picks. Hacking investment or ally support is the answer.

---

## UC7 — Lock Picking Skill Progression (Soft Gate in Action)

**Context:** The player encounters a lock well above their current Lock Picking skill level.

**Player experience:**

- The lock opens into the minigame — it is not blocked or greyed out
- The targets on each ring are **tiny** (low skill = small targets)
- There is **no bounce preview** (or minimal preview at low-mid skill)
- **All rings are active** — no pre-solved rings
- On advanced locks, block phasing is in effect with no preview of phase transitions
- The player can still attempt the lock. The physics are the same. The targets exist. A player with exceptional spatial reasoning and reflexes can chain through every ring
- Most players will fail repeatedly on locks far above their skill — but each attempt teaches the mechanic and the resets let them try again immediately

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
