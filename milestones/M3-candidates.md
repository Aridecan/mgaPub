# CoreX-M3 — Candidates

> **Status: a candidates list, not a committed milestone.** Nothing here is scoped, ordered or
> promised. It exists because M3 items have been accumulating across
> [M1](M1-framework-and-ci.md), [M2](M2-delivery-girl-first-run.md), the GDD and the creative briefs,
> and were becoming hard to see as a set. Scope gets decided when M2 closes.

**What CoreX is for.** CoreX proves out the **risky technology that could kill the development
cycle** — de-risked before content is built on it. That is the filter for what belongs here. A thing
qualifies if getting it wrong late would invalidate a large amount of work, not merely because it is
unbuilt.

---

## X — The facial animation pipeline

**Added 2026-07-30. This is the clearest CoreX-shaped item on the list**, because MGA is a
story-heavy dialogue game and a large volume of content depends on the answer.

**The intended workflow** (Peter): take the dialogue WAV, solve it to facial animation offline,
ship the animation asset, play it alongside the sound at runtime. That shape is correct and is the
standard approach for authored dialogue.

**What needs proving, in order:**

1. **Does the solver's output land on a CUSTOM character?** This is the gate, and everything else is
   downstream of it. Epic describes audio-driven animation as delivering *"animation for all MetaHuman
   facial controls"* — and the MetaHuman facial rig is **not** ARKit 52; it is a much larger control
   set. MGA's characters are custom Blender bodies carrying the **ARKit 52** blendshapes (Faceit
   output, verified present and canonically named). If the solver targets the MetaHuman rig, a
   **conversion layer** sits between it and our characters, and that is real work rather than a
   setting. Epic's documentation does not address custom-character compatibility.
   **Test, do not research:** solve one line, retarget onto the base body, see whether the face moves.
2. **Which tool.** Epic's in-engine audio-driven animation is already installed and is what the ARKit
   set was authored for. **NVIDIA Audio2Face** is the alternative and may fit better — it is
   explicitly blendshape-oriented where Epic's is rig-oriented. Decide after (1).
3. **Line-to-animation pairing.** Dialogue is **one `UDialogueWave` per line**; adding a facial
   animation makes it **two assets per line**, and something must know they belong together —
   a naming convention, a DataTable, or an extension to the dialogue asset. Decide before there are
   hundreds of lines, not after. This compounds an open concern already recorded against
   `UDialogueWave`: whether one-asset-per-line authoring holds at volume.
4. **Playback sync.** The animation and the audio need a single authority for when a line starts —
   an Animation Notify firing the sound, or both driven from one place. Playing them independently
   and hoping is the failure mode.
5. **The community-voice consequence.** MGA ships **en-CA only and the community translates,
   including voice**. If facial animation is solved at authoring time, community packs arrive with
   **no matching lip-sync**. Three routes: lip-sync stays keyed to the shipped en-CA solve and drifts
   for translations; community packs ship their own solved curves (needs editor access or a tool); or
   runtime lip-sync comes from outside MetaHuman.

**Settled already, so not in scope to rediscover:** on UE 5.8 the offline solver is **editor-only**
(`MetaHumanSpeech2Face` is an Editor module), and the "realtime" path is a Live Link **microphone
device** workflow rather than dialogue playback — note the offline solver's *"Realtime Audio Solver"*
option is an algorithm choice that Epic explicitly states is **still an offline process**. For
authored dialogue the offline path is the better fit regardless: deterministic, no runtime GPU cost,
no platform risk.

---

## X — The third override mechanism (animation)

M1 proved override on **C++ behaviour** (a polymorphic provider). M2 proves it on **assets** (a
skeletal mesh). **SuperSpicy needs it on animation** — linked anim layers, montage sets, or an
anim-BP override registered at activation. **None of the three share a code path**, and this is
recorded so it is not mistaken for free once the mesh swap works. See
[Content Architecture & DLC](../gdd/content-and-dlc.md).

Carries a matching CI item: the acceptance matrix needs an **animation-set assertion** so config 3
(SuperSpicy) is distinguishable from config 2 by something other than the content-advisory rank.

---

## Motion Matching and traversal — Epic's Game Animation Sample

**Added 2026-07-30. Explicitly not for M2.** Peter: look at Epic's demo where stride length adapts to
running speed, and the parkour-style movement demo.

**Already in the design, not new scope.** [CB — Traversal](../creative-briefs/CB-traversal.md)
specifies Motion Matching in three places: gait selection and blending as speed changes (UC1),
procedural hand/foot placement while climbing (UC2), and takeoff/airborne/landing selection on jumps
(UC3). It is also what the brief's "movement is analog, controlled by stick deflection, not by
toggling between walk and run" requires — Motion Matching picks poses matching current velocity
rather than blending fixed loops, so stride adapts continuously.

**Naming:** the system is **Motion Matching** (Epic's **Pose Search** plugin, with **Chooser**), not
"motion morphing". The two demos are most likely one download — Epic's **Game Animation Sample
Project** (free, UE 5.4+) carries both the motion-matched locomotion *and* the traversal set (vault,
mantle, hurdle, climb).

**The integration risk, and it splits unevenly against [GMC](M2-delivery-girl-first-run.md):**

- **Locomotion should port cheaply.** Motion Matching is an *animation* layer — it selects poses to
  match whatever the movement component already did. GMC moves the capsule, Motion Matching picks the
  pose. Different layers; they compose.
- **Traversal/parkour will not.** Epic's sample builds vaults and mantles on
  `CharacterMovementComponent` plus **Motion Warping**, and that coupling is the substance of it: the
  system has to *move* the character along a warped trajectory to meet a ledge, which is movement, not
  animation. Lifting it onto GMC means rewiring that.

**Do not assume the sample drops in whole.** Expect the gait half cheap and the parkour half a real
port. Unlike GMC, the plugins involved are all Epic's, so none of the precompiled-binary and
engine-version problems that GMC's marketplace packaging raised should apply — but pose databases add
package size and the search adds runtime cost, both worth measuring when it happens.

## Carried forward from M2

| Item | Note |
|---|---|
| **Hair simulation** | Chaos Cloth or bone-based. M2 ships hair rigid with the bone chain authored as insurance, so this becomes "turn on a physics asset" rather than "redo the hair" |
| **Clothing + the clothing pipeline** | Marvelous Designer → UE. M2 leaves an empty clothing step in the spawn order as the insertion point |
| **The Spicy body override** | M2's stretch goal; falls here if not reached. Cheaper than it was — the fork in M2 Pillar A means the Spicy body already exists as an asset |
| **The drone, and the widget system it justifies** | M2 ships a plain third-person camera and does not render the drone. The drone lands with the HUD widget budget it exists to motivate — see [CB — Delivery Girl](../creative-briefs/CB-delivery-girl.md) |

## Carried forward from M1

| Item | Note |
|---|---|
| **#27 — M1/M2 input toggle behaviour** | Enhanced Input persists a key per mapping **name** and has no notion of a mapping changing context. Nothing consumes bindings until an action bar exists |

## Deferred here 2026-07-30

| Item | Note |
|---|---|
| **The almanac + day/night cycle** | Peter: *"go through all the code again there and convert it over UE and check to make sure it all works properly."* A **review-and-port**, not a drop-in. It is **not in Perforce** — the source lives in the `mga` meta repo, and nothing was lost. **ASCII-sweep it before it enters P4**: some source is already U+FFFD-corrupted, and filetype `unicode` corrupts on the build agent only. ~580 LOC of existing tests make the port verifiable. The real argument for care is not the visuals — the **time service** will be consumed by job schedules, the school calendar, CP fatigue, weather, lighting and NPC routines, and that many consumers make the interface expensive to change later |

---

## Related

- [CoreX-M1 — Front-End Framework + DLC Load/Override + CI Pak Pipeline](M1-framework-and-ci.md)
- [CoreX-M2 — Delivery Girl: First Run](M2-delivery-girl-first-run.md)
- [CB — Delivery Girl](../creative-briefs/CB-delivery-girl.md) · [CB — Weather & Environment](../creative-briefs/CB-weather-environment.md)
- [Character Pipeline](../gdd/character-pipeline.md) · [Content Architecture & DLC](../gdd/content-and-dlc.md)
