# Release Process — Sprint and Milestone Builds

> **Status: decided 2026-11-27 (production phase).** Covers how supporter builds are released and
> debugged during production. The **final / gold release** gets its own, separate QA process,
> written when it's needed.

**Related:** [M1 — Framework and CI](M1-framework-and-ci.md) (the CI publishing this builds on);
build types as players see them in the Discord `#faq`.

**Version scheme** (decided 2026-07-26, implemented in the CI publish script):
`<major>.<minor>.<patch>-<phase>.<build>`, e.g. `0.1.0-pm1.142`. The phase is a lowercase SemVer
pre-release label (`pm1`, `pm2` for production milestones, `rc`); the final release has no suffix.

---

## Build types

| Build | What it is | Who gets it | Fixes after release |
|---|---|---|---|
| **Demo** | Test modes + Prologue + Chapter 1 main quest. Base, Spicy and SuperSpicy archives posted separately. Steam gets the base archive only. | Everyone, free | Rolled in with the next demo build |
| **Sprint build** | The full game at the end of a sprint. | Patreon / SubscribeStar, lowest paid tier and up | One debug week, then A-class only |
| **Milestone build** | A sprint build that marks a milestone, tagged and given the long debug pass below. | Same as sprint builds | Staged bug bar until final, then A-class only |
| **Weekly build** | Untested build straight from CI. Starts after M2. | Higher supporter tier | None; next week's build replaces it |

**Supported at any one time: one sprint build and one milestone build.** A new sprint build retires
the previous one, and a final milestone retires the previous milestone.

---

## Bug classes

| Class | Meaning | Examples |
|---|---|---|
| **A** | Ship-stopper. Stops publishing. | Crash; lost or corrupted save; can't progress; **content showing at the wrong content tier** (a tier leak is a compliance failure, like a console TRC/TCR failure) |
| **B** | A major feature is broken, but there's a workaround. | Quest step only completes after a reload; a job's payout is wrong |
| **C** | Cosmetic or minor. | Typo, clipping, a misaligned UI element, an audio glitch |

The developer assigns the class. Reporters describe the bug, not its severity.

---

## Sprint cycle

1. **Sprint**: variable length; it ends when the sprint's work is done.
2. **Release the sprint build.**
3. **Debug week**: one week fixing that build's bugs, patching as needed.
4. **Next sprint starts.** From then on, the previous sprint build only gets **A-class** patches.
   B and C bugs move to the current sprint.

---

## Milestone cycle — the staged bug bar

A milestone build starts like a sprint build, then gets a longer debug pass. The bar rises in stages:

| Stage | Fixed in this build | Deferred to the next sprint | Moves to the next stage when |
|---|---|---|---|
| 1 | A, B, C | — | **one week with no new C** |
| 2 | A, B | C | **one week with no new B** |
| 3 | A | B, C | **one week with no new A** → **milestone final** |

- **A-class bugs are always fixed first, at every stage.** The stages only decide what is *in scope*.
- **"A week with no new X" only counts if the build was actually played that week.** A quiet week
  with nobody testing isn't evidence. For now, activity in the bug channels is the measure.
  The principle: community play time stands in for a QA department's test hours.
- **Each stage has a cap.** If a stage runs too long, because bugs keep trickling in, its class is
  deferred anyway and the build moves to the next stage. Cap length: TBD; set it with the first
  real milestone.
- **When the milestone is declared final**, work starts on the next sprint. From then on the
  milestone build only gets A-class patches, like a sprint build.

---

## Versions and branching

- **No change to the version scheme.** A milestone is a sprint build with a revision-control
  label. A patch is a later build number in the same phase (`0.1.0-pm1.N` → `0.1.0-pm1.N+k`).
- **Patching a build that's no longer current needs a release branch.** It is cut at that build's
  label, patched there, and merged back to main. Supporting two builds means up to two release
  branches alongside main.
- **Branching is where Perforce is weakest.** The hope is that Lore (Epic's new VCS) is viable by
  the time this matters and branches better. Until then, use Perforce streams.

---

## Bug channels

Every bug channel on Discord is a forum. Reports need a version number, which is how sprint,
milestone and weekly bugs stay apart. Mod-only tags carry the process: **A / B / C** severity and
**Deferred**, alongside the existing status tags.

---

## Open

- Stage cap length for milestones.
- How "played that week" is measured once there's more than bug-channel activity to go on.
- The final / gold release QA process (no cap; separate doc).
