# Creative Brief — Delivery Girl (Game Mode)

---

## Overview

Delivery Girl is a shipping game mode reached from the main menu under **Game Modes**. It stars
**Nessa**, a character who appears in this mode and nowhere else. The mode is also the project's
**feature incubator** — features roll out here first, are tested by real play, and are then pulled
into the campaign. Anything built for it must be built to be reusable, in shared modules, not in a
mode-private silo.

This brief covers the mode's premise and its acquisition loop. The milestone that builds the ground
the mode stands on is [CoreX-M2](../milestones/M2-delivery-girl-first-run.md), which also carries the
content-tier boundary and the compliance reasoning. Neither is repeated here.

**The premise is deliberately thin.** Its job is to give direction — a reason for the subsystems the
mode exists to exercise — not to carry dramatic weight. It should be read as scaffolding, and it is
expected to stay light.

**Related documents:**

- [CoreX-M2 — Delivery Girl: First Run](../milestones/M2-delivery-girl-first-run.md) — the milestone, the tier boundary, the compliance rule
- [CB-jobs](CB-jobs.md) — UC2 is the courier loop the day channel is built from
- [CB-camera](CB-camera.md) — third-person camera, drone battery management
- [CB-hud-modification](CB-hud-modification.md) — widget point budget, the highlight widget
- [CB-notes-and-discovery](CB-notes-and-discovery.md) — search and discovery systems
- [Core Loop](../gdd/core-loop.md) — economy, time system, the legal/illegal income split

---

## Canon status

**Delivery Girl is non-canon.** It takes place in Terridyn and uses the world's corporations and
technology, but its events do not constrain the campaign, and Nessa does not appear in it.

A canon reading is available if it is ever wanted: the prototype arrives as an interstellar delivery
of Calibran technology consigned to a Big 20 recipient. Nothing in the mode as designed forecloses
that. It is recorded here so the option is not lost, not because it is planned.

---

## Premise

Nessa is a tertiary student working as a courier to make ends meet.

One of her deliveries is a prototype drone. An accident during the run opens the package, and the
drone **binds to her** — the mode's version of the per-operator calibration the CAMERA system already
uses, where a unit calibrated to one person will not interface correctly with another. The binding is
the load-bearing beat: dropping the package would not stop her handing it back, but a drone that has
locked to her cannot simply be re-consigned.

The recipient does not accept the loss. Nessa owes them the value of the prototype, and the mode is
about paying that off.

### The drone is not a CAMERA

The prototype is **SMU-lineage personal-companion technology**, not Calibran CAMERA hardware. SMU
already produces small, silent, camera-equipped drones marketed as personal companions; the prototype
is a next-generation unit from that line.

This is deliberate. CAMERA is Calibran technology that Meghan brought to Terridyn along with its
source code, and the Chapter 3–4 arc with Imara is explicitly about upgrading and extending that
hardware rather than inventing it. A working CAMERA prototype circulating on Terridyn would undercut
that arc, and nobody on Terridyn should know the acronym. Sourcing the drone from an existing Terridyn
product line avoids the collision entirely rather than working around it.

The drone provides the mode's third-person camera, and its battery is the limiter on the highlight
widget (see below).

### Who Nessa owes, and who pays her

These are **two different parties**, and keeping them separate is the point.

| Role | Party | Function |
|------|-------|----------|
| **Creditor** | Mautorium | Lost the prototype. Wants it back or paid for. The pressure the mode runs on |
| **Client** | SMU | One buyer among several for night work. Pays for the risky runs |

Splitting them keeps the night runs Nessa's **choice** — she takes them because they pay, not because
a corporation is holding a debt over her and directing how she settles it. It also puts two factions
in play for a reputation portfolio later, and it keeps the mode's premise light, which is what a
feature incubator wants.

---

## Use Cases

### UC1 — Working the Day Channel (Legitimate Deliveries)

**Actor:** The player
**Goal:** Earn steady, low-risk income through ordinary courier work
**Schedule:** On demand via the delivery app
**Available from:** Mode start

The day channel is [CB-jobs](CB-jobs.md) UC2 unchanged — the courier loop that brief calls *"the city
exploration engine."* Jobs come from the delivery app. There is no searching: the player opens the
app, accepts a job, and runs it.

Day work **covers survival and does not clear the debt.** This mirrors the campaign's own economic
engine, where legal work covers roughly 75–90% of monthly costs and the shortfall is what pushes the
player toward risk. Here the shortfall is a debt rather than rent, but the pressure is identical and
the resolution is the same: the safe channel is not sufficient, so the player goes out at night.

---

### UC2 — Working the Night Channel (Dead Drops)

**Actor:** The player
**Goal:** Acquire and run high-value off-network delivery work
**Schedule:** Night only
**Available from:** Mode start

Night work is not offered through the app. Off-network courier work is distributed **physically, as
dead drops**, and the drops are made at night — which is why the keys exist, and why they are
night-only. The player does not receive a mission; the player **finds** one.

**Acquiring a night job, step by step:**

1. **Search the district for crypto keys.** Keys are placed in the world and must be found. Early on
   this is done by eye, which is how the player learns the geography of the district
2. **Collect the required number of keys**
3. **Locate the container** holding the job
4. **Open the container with the keys** and receive the delivery
5. **Run the delivery.** From this point the run is a courier job, with the night's risk profile

Night runs are made naked or in otherwise compromised attire, trading cover for money and exercising
the stealth and exposure systems. That layer is documented in
[CoreX-M2](../milestones/M2-delivery-girl-first-run.md), including the rule that the mechanics are
identical at every content tier and only the body, attire and animation set are overridden.

Because keys are night-only and night is when the exposure systems are live, **the risky layer sits
on the critical path to getting a night job at all.** It is not optional flavour running beside the
mode; it is how the mode's paying channel is reached.

**Why this is the right loop for a small map.** A delivery consumes geography — a route is run once
and that street is spent. A search loop reuses it, because the keys move. The mode's home district
is a small hand-baked area, and a mechanic whose replay value does not scale with map size is what
makes that area go further.

---

### UC3 — The Highlight Widget

**Actor:** The player
**Goal:** Reduce the cost of searching by having keys and containers marked in the HUD
**Available from:** Purchased/unlocked upgrade

The highlight widget marks crypto keys and job containers in the world. It is a HUD widget and it
consumes points from the widget budget documented in
[CB-hud-modification](CB-hud-modification.md) — it is not free and it competes with everything else
the player wants on screen.

**It is a difficulty ramp, not a convenience.** Before the upgrade the player searches by eye and
learns the district. After it, the player searches by highlight and the mode stops charging them for
knowledge they already have.

**It runs off the drone, so it costs battery.** Sweeping for keys drains charge against the battery
model in [CB-camera](CB-camera.md) UC3. This is what stops the widget trivialising the search once
acquired, and it uses an existing system rather than introducing a new limiter. It also promotes
battery management from the soft background consideration it is in the campaign into something the
player actively spends.

---

## Relationship to CoreX-M2

**Almost nothing in this brief is M2 content.** Missions, economy, day/night cycle, stealth and NPCs
are all explicit M2 non-goals. UC1 through UC3 are M3 and later.

What M2 takes from the premise is narrow:

- **Who Nessa is**, for the character work in WS1
- **Which district gets baked** — the mode's home area
- **What the Game Modes entry says**, for WS6

**One decision here does land on M2.** M2 Pillar C ships a third-person camera, and this brief makes
that camera diegetically a drone — a physical object in the world, per [CB-camera](CB-camera.md).

**Decision: M2 ships a plain third-person camera and does not render the drone.** The drone is
fiction-only until the milestone that builds the widget system it exists to justify. The camera
behaves identically either way, and M2 is the milestone that currently holds zero character assets —
adding a second mesh, actor and spawn path to it buys atmosphere at the cost of focus.

---

## Open Items

- **Key-to-container structure** — do the collected keys *reveal* the container's location, making the
  hunt a trail with a payoff, or are the keys and the container independent finds? The trail reading
  ramps better; two independent searches risk tedium. Undecided
- **Key count** — how many keys per job, and does the count scale with job value or with progression?
- **Key placement** — fully random within the district, hand-placed from a pool, or weighted toward
  areas the player has not recently searched?
- **Does the debt terminate?** A fixed sum gives the mode a completion state and a payoff; an open
  balance makes it an endless incubator sandbox. These are different modes and the choice affects what
  gets built
- **How exposure converts to money** — the night runs pay more, but the mechanism is unspecified.
  Client demand, a risk multiplier on the job, or something the drone's recording capability mediates
- **Whether found keys persist** across sessions, which is a save-system question
- **Whether the day channel has its own progression** (vehicle unlocks per [CB-jobs](CB-jobs.md), courier
  reputation) or stays deliberately flat so the night channel carries progression alone
- **Nessa's characterisation** beyond the premise — she currently has an occupation and a debt, and no
  personality, history or relationships. Whether she needs any is itself open
- **Whether the drone has a personality or voice.** It is a bound personal companion that follows her
  everywhere, which is an obvious hook, and equally an obvious way to give a thin premise more weight
  than it was meant to carry
