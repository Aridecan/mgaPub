# MGA — Audio Direction

---

## Philosophy

Music in MGA follows emotional truth, not scene-type labels. The question for every cue is not "what kind of scene is this?" but "what is Meghan actually feeling right now, and what does the player need to feel alongside her?" That question produces different answers in the same location at different story moments.

Three musical pillars cover the majority of the game. They are distinct enough to be legible, flexible enough to blend at the moments that demand it.

---

## The Three Pillars

### 1. Rock / Metal — Combat

Combat music has two modes that share a DNA but operate at different intensities.

**Meghan fighting** — Hard rock. Grittier, more grounded, less orchestral. This is a person scrapping, not a legend doing what she was built for. The music reflects competence and effort, not transcendence. Driving rhythm, distorted guitar, contained energy.

**Ava fighting** — Higher energy, fuller sound, more headroom. When Meghan transforms, the music earns the transformation — the same underlying feel, but expanded. The ceiling is higher. The stakes are audible.

**Peak moments** — The largest Ava sequences (major boss encounters, Chapter 6 climax, team-coordinated battles) escalate to **symphonic metal**: full orchestra running alongside full band. This is the point where the rock and orchestral pillars meet. The guitar and the strings are both doing real work simultaneously. Reference territory: Nightwish, Epica, the heavier end of Within Temptation.

The two combat modes should feel continuous — a player should be able to hear that Meghan and Ava share a musical identity — but the difference in ceiling should be unmistakable.

### 2. Orchestral — Emotional / Stealth

The orchestral pillar covers everything that needs warmth, weight, or quiet tension.

**Home and domestic scenes** — Chamber-scale, piano-forward. The apartment is small and warm; the music should fit inside it. No swells, no grandeur. The emotional work here is in the detail, not the volume.

**Stealth and infiltration** — Sparse orchestral with low strings carrying the tension. Deliberate pacing; silence used as a compositional element. The music creates unease without resolving it — the player should always feel one decision away from something going wrong.

**Emotional peaks** — Full orchestral for the moments that earn it: significant relationship developments, story revelations, loss. These are rare enough that the full orchestra carries weight when it arrives. Overuse destroys the effect.

### 3. Electronic / Synthwave — World

Terridyn is a sci-fi city three centuries from now, half-empty, corporate-run. It should sound like it.

**City exploration** — Synthwave and ambient electronic. Something that feels like walking through a city that is functional but not quite full — the infrastructure is there, the people are fewer than the architecture expects. Atmospheric rather than propulsive; exploration music, not chase music.

**Corporate environments** — Cold minimalist electronic. MD's offices, boardrooms, the Machina Dynamics internship day-to-day. Sterile, precise, slightly uncomfortable. The music should make the player feel like they are in a space that was designed to be efficient rather than human.

---

## Specific Contexts

### Burton's Bar

The bar is a working-class space with warmth and regulars and occasional chaos. Its ambient music is Irish/Celtic pub style — fiddles, bodhrán, tin whistle, the sound of a place that has been a bar for a long time and knows exactly what it is. Low energy during quiet service hours; the room pushes louder during Friday and Saturday night rushes when the crowds come in. The music at Burton's should feel like it belongs to the place, not like a soundtrack.

### Hell Environments

The 7th Circle and the Pride demon territories visited in the late game require a completely separate palette from anything on Terridyn. Ancient, vast, and alien. Choral elements work here — voices without words, or with words in no language the player knows. The frozen landscapes of the 7th Circle specifically should feel like standing inside something enormous and old. No rock, no electronic: this is orchestral and vocal, with significant use of silence and environmental sound.

### Character Themes

Key characters have leitmotifs that recur across the game in different arrangements as circumstances change. A theme introduced in a quiet domestic context may appear later in a different form — same harmonic identity, different register. Players on a second playthrough will recognise what they missed the first time.

Thematic design for specific characters is documented in private design notes. The general principle: character themes should carry subtext that only becomes legible in retrospect. The theorist who goes back to listen will find it.

---

## Sound Design

### Principle

Where possible, keep sound design separate from music — the game should function if the music is muted. Sounds that are critical to gameplay (enemy telegraphs, incoming attacks, environmental cues, UI feedback) must be readable through audio alone, without the music layer.

### Magical Effects

Magical ability sounds should feel distinct from physical sounds. Ice magic specifically — Ava's element — should have a recognisable signature: clean, sharp, with a resonant quality to sustained effects. Other magical girls' elements have their own signatures. No two elements should be confusable at a distance.

### The City

Terridyn's ambient soundscape reflects its half-empty state. The city is functional but quieter than its architecture expects. Distant city sounds, occasional vehicle traffic, wind through spaces that used to hold more people. The ghost-city quality should be felt in the environment audio even when the music is doing something else.

### UI

UI audio is minimal and non-intrusive. Menus, notifications, and system feedback use short, clear tones that do not compete with the game's music. The skill system does not make noise when a level increases during active gameplay — that information is available in a non-interruptive log rather than an audio event mid-encounter.

---

## Voice Acting

### The Dialogue Document

All dialogue is written with accompanying emotional context: the line, the character's internal state, the intent of the delivery, and any relevant scene context. This is not supplementary annotation — it is part of writing the line. A line without its emotional context is incomplete.

This document is the single source of truth for all VO work, regardless of who or what performs it. It serves three purposes simultaneously:

| Use | How the document is used |
|-----|--------------------------|
| **AI VO generation** | Dialogue and emotional context fed directly into the AI pipeline to automate performance generation |
| **Fan localisation** | Handed to localizers alongside the script — they have the character register, the emotional beat, and the intent, not just the words to translate |
| **Human VA direction** | If human actors replace the AI VO, the document is the director's brief — casting reference, session breakdown, and performance notes in one |

Writing the emotional context is writing the performance. The document format makes that work reusable across every downstream use without additional effort.

### Development and Launch

Voice overs are AI-generated using tools that accept dialogue and emotional context as input, automating the generation pipeline directly from the dialogue document. This keeps VO unblocked during writing and iteration — dialogue can be revised freely without recording cost, and performances can be reviewed in-context throughout production.

AI VO is the shipped product until replacement is feasible. It is not a temporary stopgap; it is the practical approach for a solo developer with a story-heavy game and a large script.

### Replacement Plan

Once the game is feature-complete and story-complete — script locked, no more dialogue changes — human voice actors are the target if budget allows. The replacement is a full cast swap. The AI performances serve as casting reference; the dialogue document serves as the director's brief. No additional preparation is needed at that point beyond scheduling.

---

## Open Items

- Reference tracks for each context — to be compiled and reviewed
- Specific character theme compositions — documented in private design notes
- Whether the bar has licensed music, original compositions, or in-world diegetic performances
- Hell environment audio — confirm choral vs. purely orchestral vs. combination
- Whether environmental audio (city ambience, hell glaciers) is composed or designed — or both layered
