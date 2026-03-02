# Creative Brief — Studying & Academics

---

## Overview

Studying is the system that makes time matter. Meghan arrives on Terridyn to get into school — if she fails the MCI entrance exam within four months, the game is over. After enrollment, ongoing academic performance must be maintained or expulsion follows. Every hour spent studying is an hour not spent earning rent money, building relationships, or training combat skills. Every hour not spent studying is an hour closer to failing.

The studying system is not a side activity. It is one of the two structural pressures (alongside the economy) that drive the core loop. The player must decide, day by day, how to split Meghan's time between study, work, social investment, and everything else the game demands. The Studying skill rewards this investment by compressing study time — a high-Studying Meghan gets more out of less, freeing hours for the rest of her life.

**Related documents:**

- [Core Loop — School Calendar](../gdd/core-loop.md#the-school-calendar--primary-structural-pressure) — time pressure, exam milestones, game-over conditions
- [Studying Skill](../../mgaPriv/mechanics/skills/studying.md) — skill progression, time reduction tiers, training methods
- [MCI](../../mgaPriv/education/mci.md) — entrance exam structure, curriculum tracks, campus layout
- [Education System](../../mgaPriv/education/education-system.md) — school year structure, dual intake, university
- [Meditation](../../mgaPriv/mechanics/skills/meditation.md) — partial CP recovery between activities
- [Skills Overview](../../mgaPriv/mechanics/skills.md) — full skill list including Knowledge Recall (WIS) and Research (INT), both synergistic with Studying

---

## Use Cases

### UC1 — Solo Study Session

**Actor:** The player
**Goal:** Spend time studying to improve exam readiness, build INT/WIS, and progress the Studying skill
**Trigger:** Player chooses to study from the activity menu at a valid location

The player has two ways to study, available at any time:

#### Option A — Time-Skip Study

The player tells Meghan to study. Time passes, results are applied.

1. **Player initiates study** at a location with study materials (dorm room, library, common room — whether study is location-restricted or available anywhere with materials is an open item)
2. **Player chooses subject** — one of the six MCI prerequisite subjects (Mathematics, Science, Language, Colony History, and two TBD), or general study for broader INT/WIS gains without single-subject focus
3. **Player chooses study duration** — selectable in increments (e.g. 1 hour, 2 hours, 4 hours). Actual clock cost is modified by Studying skill tier:

   | Studying Level | Time Cost | Effective Study |
   |---------------|-----------|-----------------|
   | 1–20 (Basic) | 100% of selected duration | Baseline retention |
   | 21–50 (Intermediate) | 75% | Improved pattern recognition |
   | 51–80 (Advanced) | 50% | Rapid absorption, strong retention |
   | 81–100 (Expert) | 25% | Exceptional comprehension |

   A 4-hour focused session at Expert level costs 1 hour of clock time. The time savings are the primary reward for Studying skill investment.
4. **Clock advances** by the modified duration. Meghan's schedule shifts accordingly
5. **Results applied:**
   - INT/WIS experience gained (scaled by duration and material difficulty)
   - Studying skill experience gained
   - Subject readiness score improved (focused study only)
   - CP drained slightly — studying is mentally taxing. Studying while CP is low reduces the quality of the session (less INT/WIS gain, less subject readiness improvement). The player feels the cost of pushing through exhaustion
6. **Session ends.** Meghan returns to free time

#### Option B — Player-Driven Study (Textbooks and Homework)

The player opens in-game textbooks and studies the material themselves.

1. **Player opens a textbook** from the inventory or study menu. Each of the six MCI subjects has its own textbook
2. **Player reads through the material.** Textbooks contain actual readable content — full articles, chapters, diagrams, and explanations written as in-world academic material. Colony History reads like a history textbook set in the MGA universe. Mathematics presents problems and worked examples. Science covers crystal technology, physics, and planetary science. These are real content, not UI summaries
3. **Player encounters homework questions** at the end of sections. These are comprehension and application questions drawn from the material just read
4. **Player answers the questions.** Correct answers improve subject readiness more efficiently than time-skip study — the player is demonstrating actual understanding, not just investing time
5. **Time still advances** while the player reads and works through questions, but the readiness gain per hour is higher than time-skip study for a player who engages with the material and answers correctly. A player who reads poorly and answers wrong gets less than time-skip would have given
6. **No Studying skill gate.** Any player can open a textbook at any time. The textbooks do not require minimum Studying skill — they require the player's attention. A player who reads the Colony History textbook on day one and understands it has genuinely studied, regardless of Meghan's skill level

**The two options are not exclusive.** A player can time-skip study for Mathematics (letting Meghan's skills handle it) and personally read through Colony History (because they find the lore interesting). The system supports mixing approaches per subject and per session.

**What the player feels:** A deliberate trade-off either way. Time-skip study is efficient but passive. Player-driven study is engaging but demands real attention. Both advance the clock; both improve readiness. The difference is whether the player wants to invest their own effort or Meghan's stats.

---

### UC2 — Attending Class

**Actor:** The player
**Goal:** Attend a scheduled class at MCI for academic credit and skill progression
**Trigger:** Class session is available during MCI operating hours; player chooses to attend

The player has two ways to attend class:

#### Option A — Time-Skip (Base Game)

The default class experience. Available in the base game without additional content.

1. **Class is scheduled** during MCI's academic day. The schedule is known to the player (visible on the phone calendar). Classes occur on fixed days of the week at fixed times
2. **Player travels to MCI main campus** and enters the classroom before the session starts. Travel time from the residential campus (30 minutes by foot, shorter by monorail) is part of the time cost
3. **Class runs as a time-skip.** Clock advances by the class duration. A brief summary screen shows what was covered and what Meghan gained
4. **Results applied:**
   - INT/WIS experience gained — slightly faster progression than solo study for the same time investment
   - Subject readiness improved for the class's subject
   - Studying skill experience gained
   - Attendance record updated — consistent attendance contributes to ongoing academic performance (UC5)
5. **Class ends.** Meghan is on campus and free to continue her day

#### Option B — Sit-Through Class (DLC / Mod Content)

When the class content DLC is installed (or equivalent mod content is present), the player can choose to sit through the class in real time.

1. **Player enters the classroom** as in Option A
2. **The class plays out.** A teacher stands at a lectern beside a slideboard. The class is presented as: voiceover lecture, slideboard visuals (diagrams, text, images), and limited teacher gestures. This is not a cutscene — it is a presentational format designed to deliver actual subject content
3. **The player watches and listens.** The material presented is the same content found in the subject's textbook (UC1 Option B), delivered in lecture format. Players who prefer listening to reading can absorb the same information this way
4. **The player can leave at any time.** Leaving mid-class reverts to the time-skip result for the remaining duration — partial attendance credit, partial XP
5. **Results are the same as Option A** — the DLC does not provide a mechanical advantage. The benefit is presentational: players who want the school atmosphere get it; players who don't lose nothing

**DLC/Mod architecture:** The sit-through class system is designed as a content slot. The base game defines the class schedule, subject, and XP rewards. The DLC (or a mod) fills the slot with the lectern/slideboard/voiceover presentation. If no content is installed for a given class, Option A (time-skip) is the only available mode. This means modders can create class content for subjects the base DLC does not cover, or create entirely new lecture series.

**Missing class:** The player can skip any class session regardless of which option is available. There is no hard lock forcing attendance. The cost is indirect — missed classes reduce the attendance component of academic performance, missed material makes exams harder, and the time pressure tightens. The player who skips class to take a bounty contract is making a real trade-off.

**Classroom events:** Before and after class (both options), the player may encounter social moments — a question from a classmate, a comment from a lecturer, a hallway interaction. These are lightweight relationship-building opportunities with MCI NPCs that do not occur elsewhere.

**Robot encounters:** The Straton Technologies security robots patrol campus. Arriving late, leaving early, or being caught in a corridor between classes can trigger a robot interaction — forced escort, clothing confiscation, or invasive search. These encounters are comic but have real consequences (lost items, lost time, embarrassment). Terry's rescue responses build the dependency/obligation dynamic. See [MCI — Corporate Connections](../../mgaPriv/education/mci.md#corporate-connections).

---

### UC3 — Study Groups

**Actor:** The player
**Goal:** Study with NPCs for improved learning and relationship building
**Trigger:** Player initiates a study group session when eligible NPCs are available

**Step by step:**

1. **Player invites available NPCs to study together.** Availability depends on the NPC's schedule (they have their own commitments) and relationship state (strangers don't study together)
2. **Study group convenes** at a valid location (dorm common room, library, campus study area)
3. **Session runs.** Clock advances as per solo study, modified by Studying skill. Duration is negotiated at start (NPCs may have time constraints)
4. **Results applied:**
   - INT/WIS and Studying skill experience gained — comparable to solo study
   - **Information surfacing:** Group study can reveal information that solo study misses. An NPC who has already passed a subject may share insights that improve Meghan's readiness faster than solo study for that subject. This is the primary mechanical benefit
   - **Relationship experience** with participating NPCs. Shared study is a bonding activity — not as direct as social interaction, but it builds familiarity and trust over time
5. **Session ends.** NPCs return to their schedules

**Available study partners (progression):**

| NPC | Available from | Notes |
|-----|---------------|-------|
| **Celeste** | Ch.1 | Meghan's first study partner; already enrolled; familiar with MCI material |
| **Daisy** | Ch.3 (post-TMS transfer) | Different academic background (mining); complements Meghan's knowledge gaps |
| **Other MCI students** | Varies | Background NPCs with subject specialties; unlocked through campus social interactions |

**Terry does not study with Meghan.** Terry lives on the residential campus and is a constant social presence, but her academic involvement is at a different level (university program). She helps Meghan in other ways — providing materials, explaining concepts in conversation — but is not a study group participant.

---

### UC4 — Taking the MCI Entrance Exam

**Actor:** The player
**Goal:** Pass all required subjects within the 4-month exam window to secure MCI enrollment
**Trigger:** Monthly exam period arrives; player chooses to sit one or more subject exams

**Structure:**

The entrance exam is not a single test. It is a set of **subject exams** taken across a **4-month window**. An exam period occurs at the end of each month, giving Meghan up to four attempts per subject.

Six subjects:
- Mathematics
- Science
- Language
- Colony History
- TBD
- TBD

Once a subject is passed, it does not need to be retaken. Meghan must pass all six subjects within the window.

**Exam scoring foundation:**

Every exam starts with a **calculated score** derived from Meghan's current state:

```
Base score = INT + Studying skill + subject readiness (from study time invested)
Variance   = ±RNG (reduced by Studying skill's stress resistance)
Final score = Base score + Variance + Player modifier (from options below)
Pass threshold = fixed per subject
```

More study = higher base score and lower variance. A well-prepared Meghan has a high floor and a narrow band of outcomes. A poorly-prepared Meghan has a low floor and a wide band — she might get lucky, but she might not.

**The player has three options for each subject exam, chosen at exam time:**

#### Option 1 — Auto-Roll

Meghan takes the exam. Time advances. The calculated score resolves automatically with no player input.

This is for players who have invested in study time and trust Meghan's preparation. It is also the fallback for players who want to move past the exam interaction entirely. The result is purely mechanical: stats + study + RNG.

#### Option 2 — Short Exam

The player answers a small set of questions (5–10) drawn from the subject's material. The player's performance on these questions generates a **modifier** that is applied to the calculated score.

- Correct answers → positive modifier (bonus on top of the calculated score)
- Incorrect answers → negative modifier (penalty against the calculated score)

The modifier adjusts the outcome but does not replace it. A player who answers every question correctly but never had Meghan study still has a low base score — the bonus may not be enough. A player who studied extensively but answers poorly takes a hit but has a high base score to absorb it.

This is for players who want some engagement with the exam without sitting through the full question pool.

#### Option 3 — Full Exam

The player takes the complete exam: a full set of multiple-choice questions, randomized from a larger question pool. The question pool is large enough that retaking the exam in a later period presents different questions.

- The player can **bail out at any point** during the full exam. On bail-out, the exam falls back to Option 2 behaviour — the questions answered so far generate a modifier, weighted by how much of the exam was completed. Answering 80% of the questions before bailing gives a stronger modifier (positive or negative) than answering 20%
- Completing the full exam gives the **maximum player modifier weight** — the player's performance has the strongest possible effect on the final score

This is for players who want the full exam experience, whether for immersion, challenge, or confidence that they know the material.

**The cascade:** Option 3 contains Option 2, which contains Option 1. The player chooses their engagement level. Bailing from 3 drops to 2. Choosing 2 is a pre-shortened version of 3. Choosing 1 skips player input entirely. The underlying calculated score is always the foundation.

#### Question Formats

Exams use three question formats, mixed per subject as appropriate:

| Format | Example | Validation |
|--------|---------|------------|
| **Multiple choice** | "Which corporation founded the Terridyn colony?" with 5 options | Select one answer |
| **Numeric entry** | "What is the value of x in 3x + 7 = 22?" → player types a number | Exact match (with tolerance for rounding where appropriate) |
| **Keyword entry** | "What major event caused the depopulation of Terridyn's surface?" → player types an answer | Keyword lookup — accepts "the exodus", "exodus", and reasonable variants. No complex parsing; the system checks for the presence of expected keywords |

Multiple choice is the primary format across all subjects. Numeric entry appears in Mathematics and Science where calculation is involved. Keyword entry appears in Colony History and Language for factual recall questions where the answer is a specific term or name. There are no essays, no free-form writing, and no questions requiring complex natural language evaluation.

#### Skill Aids

When taking an exam (Options 2 or 3), the player chooses one of two modes:

**Skill Aided (default):** Meghan's skills help the player during the exam. For multiple-choice questions (5 options), the system crosses out incorrect answers before the player chooses:

| Condition | Answers eliminated |
|-----------|-------------------|
| Low skill relative to question difficulty | 0 (no help) |
| Moderate skill | 1 eliminated |
| High skill | 2 eliminated |
| Very high skill | 3 eliminated (only 2 options remain) |

The number of eliminations is determined per question by comparing Meghan's relevant skill (INT + Studying + subject-specific skills) against the question's difficulty rating. Easy questions on a well-studied subject may show 3 eliminations; hard questions on a weak subject may show none.

For numeric entry and keyword entry questions, skill aids do not eliminate answers (there are no options to cross out). Instead, high skill may provide a hint — a formula reminder for math, a partial keyword for history — displayed as a subtle UI element the player can use or ignore.

**No Skill Aids (Challenge Mode):** All 5 options are visible on every multiple-choice question. No eliminations, no hints. The player's knowledge is the only factor. This is for players who want the full challenge of taking the exam themselves.

Challenge Mode does not change the scoring formula — the player modifier is calculated the same way regardless of whether skill aids were active. The difference is entirely in how much help the player receives during the exam. A player who aces the exam in Challenge Mode gets the same modifier as one who aces it with Skill Aided — but they did it without Meghan's help.

**Step by step:**

1. **Exam period begins** at the end of the month. The player is notified in advance (phone calendar, NPC reminders)
2. **Player chooses which subjects to attempt** this period. Taking multiple subjects in one period is possible but each costs time and mental energy (CP)
3. **Player chooses exam option** (1, 2, or 3) per subject
4. **Exam resolves.** Calculated score + player modifier (if any) = final score. Pass or fail
5. **Results:**
   - **Pass:** Subject marked complete. One step closer to full enrollment
   - **Fail:** Subject remains open. Can reattempt next month's exam period. Study readiness carries forward — time invested is not lost. The question pool randomizes, so retaking presents different questions

**Strategy:** The player can front-load easy subjects and save difficult ones for later periods when more study time has accumulated. A player who read the Colony History textbook cover to cover (UC1 Option B) can confidently take the full exam for that subject on month one, while time-skip studying Mathematics and auto-rolling it on month three once Meghan's INT is higher.

**Stakes:** Failure to pass all six subjects by the end of the fourth month is a **game over**. Meghan cannot enrol; her stated reason for being on Terridyn collapses. This is one of only two academic game-over conditions (the other being post-enrollment expulsion).

---

### UC5 — Ongoing Academic Performance

**Actor:** The player
**Goal:** Maintain sufficient academic standing after enrollment to avoid expulsion
**Trigger:** Post-enrollment (Ch.3 onward); assessed at scheduled exam events

**How it works:**

Enrollment is not the finish line. Once Meghan is an MCI student, her academic performance is tracked continuously and assessed at scheduled exam events throughout the school year.

**Performance inputs:**

| Input | Effect |
|-------|--------|
| **Class attendance** | Consistent attendance builds a baseline. Frequent absences drag performance down |
| **Study investment** | Time spent studying between exam events improves marks |
| **Exam event results** | Periodic exams test accumulated knowledge; same three resolution paths as the entrance exam |
| **Practical application** | Using academic knowledge in jobs and missions contributes to understanding (see UC6) |

**Consequence escalation:**

| Academic state | Trigger | Consequence |
|---------------|---------|-------------|
| **Good standing** | Marks above threshold | No issues; full access to MCI content |
| **Academic warning** | Marks slip below threshold at an exam event | NPC dialogue reflects concern; player warned |
| **Probation** | Marks remain below threshold at the next exam event | Restricted extracurricular access; increased pressure |
| **Expulsion** | Marks below threshold at a third consecutive exam event | **Game over** — Meghan is expelled |

The escalation gives the player time to recover. A single bad exam period is a warning, not a death sentence. Two bad periods is serious. Three consecutive is terminal. The player always has at least one full exam cycle to course-correct.

**Ch.3 shift:** The Machina Dynamics internship (Ch.3) provides stable income, easing economic pressure. This frees time for study — but the player may be tempted to use that time for other priorities now that survival is less urgent. The academic performance system ensures that school remains a constraint even after the economy softens.

**TMS influx (Ch.3):** When TMS closes and its students transfer to MCI, the academic environment changes. New students, new social dynamics, new competition. This is narrative texture, not a mechanical change to the performance system.

---

### UC6 — Practical Application

**Actor:** The player
**Goal:** Train the Studying skill and reinforce academic knowledge through real-world application
**Trigger:** Player uses knowledge gained from study in a job, mission, or problem-solving context

**How it works:**

Practical application is not a dedicated activity — it is a training method that occurs within other activities. When Meghan applies academic knowledge in the field, the Studying skill gains experience more efficiently than from dedicated study sessions.

**Examples:**

| Activity | Knowledge applied | Studying benefit |
|----------|------------------|-----------------|
| **Hacking contract** | Mathematics, applied reasoning, pattern recognition | Problems that mirror studied material train Studying faster |
| **Mechanic repair** | Science (physics, materials), engineering principles | Diagnosing faults using studied knowledge reinforces retention |
| **Crystal work** (Machina Dynamics) | Science (crystal technology), chemistry | Direct application of Meghan's primary academic interest |
| **Bounty investigation** | Planetary History, social knowledge, deduction | Research and deduction draw on studied material |
| **Conversation** | Any subject | Discussing studied topics with knowledgeable NPCs reinforces understanding |

**The Razor holds:** Studying is not isolated busywork. The INT/WIS built through study directly improves hacking contract performance, mechanic diagnostic accuracy, and combat strategy. A player who studies extensively arrives at Chapter 6 with cognitive tools that a combat-focused player does not have — and vice versa. Neither path is wrong; both produce a different Meghan.

**Skill synergy loop:**

```
Study → INT/WIS improve → Job performance improves
  ↑                                    ↓
  └── Practical application ←── Job experience
```

Studying feeds into jobs; jobs feed back into Studying. The player who recognises this loop and actively seeks practical application opportunities progresses faster than one who treats study and work as separate activities.

---

## Design Intent — Education as a Platform

The academic system is designed to be a **content platform**, not a closed feature. The unofficial goal is to provide players with a genuinely enjoyable way to engage with educational material — a study aid wrapped in a game. The in-game subjects are science-based (real physics, real mathematics, real-world-applicable reasoning) and fiction-based (Colony History is entirely MGA lore). But the system is built so that anyone with the drive to create educational content can contribute.

**This is not accredited education.** Nothing in MGA claims to be a real-world qualification. It is recreational learning — the same impulse that makes people watch documentaries, read Wikipedia rabbit holes, or revisit university textbooks years after graduating. The game provides the structure (classes, textbooks, exams, grades); the content can be anything the creator wants to teach.

### What Ships from the Developer

The base game provides:

- **Six MCI entrance exam subjects** with textbooks, homework, and exam question pools (four confirmed: Mathematics, Science, Language, Colony History; two TBD)
- **Time-skip class attendance** for all subjects (XP applied, no presentation content)
- **The full exam system** (auto-roll, short exam, full exam, skill aids, challenge mode)
- **The academic performance tracking system** (attendance, marks, warning/probation/expulsion)

The base game's academic content is complete and playable without any DLC or mods.

**First-party DLC class content** will focus on fiction-specific subjects where the developer is the authority on the material:

- **Colony History / Terridyn History** — the founding, the TMC era, the Exodus, the recovery. Content that only exists in the MGA universe
- **Interstellar History** — the Alliance, the Twelve Systems, humanity's expansion, first contact precedents
- **Crystal Science** — the fictional science of Terridyn's energy crystals, their properties, applications, and the technology built around them. Meghan's primary academic interest and the reason she came to Terridyn

Basic 100-level science and math courses are a possibility but not a commitment. The priority is shipping the framework and the content only the developer can write. Real-world academic content (calculus, physics, chemistry, programming) is ideal community territory — subject matter experts in those fields will produce better educational material than a game developer would.

### DLC / Mod Content Architecture

The sit-through class system (UC2 Option B) is designed as a **content slot**. A subject's class schedule, XP rewards, and academic integration are defined by the base game. DLC or mods fill the slot with presentational content. If no content is installed for a given class session, the time-skip (UC2 Option A) is the only option.

A complete subject content pack provides:

| Component | Description | Required? |
|-----------|-------------|-----------|
| **Class scripts** | Written scripts for each lecture session. Define what the teacher says, in what order, with what emphasis | Yes |
| **Voice-over recordings** | Audio for the teacher's lecture, recorded from the class scripts. One voice per professor per subject | Yes |
| **Slideboard visuals** | Slide decks displayed on the in-class slideboard alongside the lecture. Diagrams, text, images, equations — whatever the subject demands | Yes |
| **Reference material** | In-game textbook content (readable articles, chapters, diagrams). Supports UC1 Option B (player-driven study). Can expand or replace the base game textbook for that subject | Optional |
| **Homework questions** | Practice questions attached to textbook sections. Supports UC1 Option B | Optional |
| **Exam questions** | Additional questions added to the subject's exam pool. Supports UC4 Options 2 and 3. More questions = more variety across attempts | Optional |

**Minimum viable content pack:** Class scripts + voice-over + slideboard visuals. This is enough to fill the class slot and give the player a sit-through experience. Reference material, homework, and exam questions are optional additions that deepen the subject.

### Adding New Subjects

Modders can add **entirely new subjects** — not just class presentations for existing ones. A new subject content pack provides everything above plus:

| Component | Description |
|-----------|-------------|
| **Subject definition** | Subject name, MCI track association, skill XP category |
| **Textbook** | Full readable reference material for the subject |
| **Exam question pool** | Minimum pool size to support randomization across exam attempts |
| **Class schedule** | How many class sessions, when they occur in the academic calendar |

A new subject integrates into the existing academic systems: it appears in the study menu, contributes to academic performance, and can be examined using the same three-option cascade. It does not affect MCI entrance exam requirements (those are fixed at six base-game subjects) but contributes to ongoing academic performance post-enrollment.

### Examples of Possible Community Content

The system is deliberately open. Some possibilities:

- **Real-world mathematics** — algebra, calculus, statistics, presented as MCI coursework. Players review or learn real math
- **Real-world physics or chemistry** — adapted to the MGA setting (Terridyn's orbital mechanics, crystal energy science with real thermodynamics underneath)
- **Programming fundamentals** — presented as a hacking-track elective at MCI
- **Real-world history** — adapted as "Comparative Colonial History" or similar MCI elective, drawing parallels between Terridyn's colonisation and real historical events
- **Language courses** — the Language subject slot could be expanded with actual foreign language instruction
- **Lore deep-dives** — extended MGA universe content (demon species taxonomy, Alliance political history, interstellar economics) for players who want more world-building

The system does not gate what subjects can be about. If someone can write a script, record a voice-over, make slides, and author questions, they can teach anything through MGA's academic system.

---

## Open Items

- Two of the six exam subjects are TBD — Mathematics, Science, Language, Colony History confirmed; two more needed
- Whether studying is location-restricted (library, dorm, campus) or can be done anywhere with materials (phone-based study, park bench with a textbook)
- Consequence for failing all four attempts at a single subject — game over, or extended window with penalty?
- Post-enrollment marks thresholds — exact numbers for warning, probation, and expulsion triggers
- Whether class attendance is mechanically mandatory (hard marks penalty for absence) or soft-gated (missing class means missing material, which makes exams harder, but no direct penalty)
- Meghan's specific MCI curriculum track
- Celeste's specific MCI track
- Which background MCI NPCs are available for study groups, and what subject specialties they offer
- Meditation interaction — can Meditation recover enough CP mid-day to extend a study session, or is its effect too small to matter for academic purposes?
- Adaptive learning mechanic at high Studying levels — how does "identifies the most efficient study method" manifest mechanically? Reduced variance? Bonus readiness per session?
- Study group information surfacing — is the bonus deterministic (Celeste always reveals X about subject Y) or dynamic (group composition affects what surfaces)?
- Robot encounter frequency and consequences — how often do campus robots disrupt academic activities? Is this a nuisance or a genuine time sink?
- Whether Daisy's TMS background gives her unique study group contributions (mining knowledge, applied sciences) that MCI students cannot provide
- Textbook content scope — how much written content per subject? Number of chapters, estimated word count, homework question count per section
- Exam question pool size per subject — how many questions needed to support randomization across four potential attempts?
- Player modifier weighting — exact formula for how Option 2/3 answers translate to score modifiers; how bail-out completion percentage scales the weight
- DLC class content scope — how many classes per subject? How long is each class session? How many slideboard slides per lecture?
- Content pack validation — what checks (if any) run on mod-added subjects to ensure question pools are large enough, scripts are complete, etc.?
- Professor assignment — does each subject have a named NPC professor, or is the teacher a generic character? Does a mod-added subject need a new character?
- Slideboard format specification — what file format for slides? PNG sequences? A custom markup? PDF-like pages?
- Voice-over pipeline for mod content — what audio format, what quality bar, any tools provided to help modders record and package VO?
- Whether mod-added subjects can contribute to MCI entrance exam requirements in a modded playthrough, or are always post-enrollment electives only
