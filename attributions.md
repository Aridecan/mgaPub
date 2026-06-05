# Attributions & Sources

This document lists real-world authors, works, and analyses whose material has been adapted into *Ordinary Girl Meghan / Magical Girl Ava* (MGA). It is maintained continuously — every commit that adapts external material adds an entry here, and the file feeds the in-game *Inspirations & Sources* credits-roll section that ships with the final game.

The principle that motivates this file is documented as [Manifesto Principle 15 — Source Transparency](gdd/manifesto.md#15-source-transparency). The short version: when we build on someone else's work, we credit them. Hiding the source in a private design doc is not enough; the credit lives where the public can see it.

---

## Scope

Two distinct levels of acknowledgement:

- **Adapted** — we used specific numbers, calculations, names, text, concepts, or images from a source. These get a full citation entry below.
- **Influenced by** — broader genre fit, tone reference, atmospheric inspirations. These are typically acknowledged in dev logs or interviews rather than formal citation here. Listing every influence is impossible; listing every adaptation is not.

---

## Categories

Entries are filed under the category that best fits their primary use. Multi-category material appears once, under primary use.

- [Academic & Technical](#academic--technical)
- [Literary & Narrative](#literary--narrative)
- [Visual & Artistic](#visual--artistic)
- [Music & Audio](#music--audio)
- [Game Design](#game-design)
- [Real Places](#real-places)

---

## Academic & Technical

### Yanagita Rikao — *Kuusou Kagaku Dokuhon* and associated lectures

**Citation.** Yanagita Rikao (柳田理科雄). *Kuusou Kagaku Dokuhon* (空想科学読本, *Fantasy Science Reading*) book series, Kadokawa Publishing (originally Media Factory), Tokyo, 1996–present. Lecturer at **Meisei University** (明星大学) Faculty of Science and Technology; founder and director of **Kuusou Kagaku Kenkyusho** (空想科学研究所, *Fantasy Science Research Institute*). His pen name's kanji 理科雄 (*rikao*) literally translates as "science guy."

**What was adapted.** Yanagita's physical analysis of magical-girl transformation sequences from *Futari wa Pretty Cure* — specifically:
- The radiation-pressure lift model treating the transformation pillar of light as physically supporting the girls' bodies via radiation pressure.
- The 50 kg mass assumption and the P = m·g·c calculation yielding ≈ 150 GW per single girl.
- The 3 m-diameter pillar extension yielding ≈ 10.4 TW total power.
- The 71,000 °C / K thermal-field temperature derived from blackbody radiance at that power density.
- The conclusion that any antagonist who entered the field during the lift phase would be instantly vaporised, explaining the genre convention as an emergent physical property rather than a narrative cheat.

**Where it appears in-game.** As an in-fiction Calibran academic paper authored by "Dr. Ilse Kaupff" (Sole Federal University of Calibran, Department of Theoretical Physics, 2298) — a historical-physics exercise re-deriving Yanagita's calculation, found by the player as a Notes pickup at MCI library or Burton's Tavern. The Kaupff paper is **side lore, not canonical to Magical Girl Ava's actual transformation mechanism.** Yanagita is cited diegetically inside the in-fiction paper itself as footnote ¹ (visible to the player); the citation chain inside the game leads back here.

**Design doc (private):** [mgaPriv/world/calibran-magical-girl-physics.md](https://github.com/Aridecan/mgaPriv/blob/main/world/calibran-magical-girl-physics.md) *(private repository — link requires access)*

---

## Literary & Narrative

*(no entries yet)*

---

## Visual & Artistic

*(no entries yet)*

---

## Music & Audio

*(no entries yet)*

---

## Game Design

*(no entries yet)*

---

## Real Places

*(no entries yet)*

---

## How to add an entry

When adapting external material into MGA content:

1. Determine the category that best fits the source's primary contribution.
2. Add an entry under that category with the four sub-headings:
   - **Citation** — full formal citation: author, work, publisher, date, identifier (ISBN / DOI / URL) where possible.
   - **What was adapted** — specific numbers, concepts, ideas, or text taken from the source.
   - **Where it appears in-game** — the in-game surfacing (Notes pickup, NPC dialogue, environment, mechanic, etc.).
   - **Design doc** — link to the design / lore doc that uses the source.
3. If the in-game text itself cites the source, add a diegetic footnote inside the in-game text per [Manifesto Principle 15.4](gdd/manifesto.md#15-source-transparency).
4. Commit the attribution alongside the content commit so the two land together.

---

## Notes

- This file is **public from day one** — it lives in `mgaPub/` and is visible to anyone tracking development, including readers of the dev logs.
- The file is **living** — sources are added as content is created. Removals are rare and require a corresponding content removal.
- The in-game credits roll at game ship will be **compiled from this file**. Format and section headings may shift to fit the rolling-credits format, but every entry here is intended to land there.

---

*Last updated: 2026-06-04.*
