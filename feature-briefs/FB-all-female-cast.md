# Feature Brief — All-Female Cast

---

## Overview

**All-Female Cast** is a new-game option that makes every character the player sees or hears female. The story, the plot and every beat stay exactly as written. Characters who are male in canon are played by their female variant instead, with a name, body, voice and pronouns to match.

This is one of the most requested options in other games. MGA can support it cheaply because of two choices it has already made: a single shared female base body for the cast, and dialogue that records each speaker's and listener's grammatical gender (see [TB-language-settings](../technical-briefs/TB-language-settings.md)).

**Related documents:**

- [Save System](../gdd/save-system.md): the playthrough folder `content_config` that stores the option
- [TB-language-settings](../technical-briefs/TB-language-settings.md): UDialogueWave, `UDialogueVoice` gender, speaker + target contexts
- [Character Pipeline](../gdd/character-pipeline.md): the shared female base body
- [Scope](../gdd/scope.md)

---

## Framing: A Casting Choice, Not an Alternate World

The option changes **who plays the part**, not the world. Nothing in the fiction explains it. The game treats it the way it treats a difficulty preset: a choice the player made about how to experience the story.

- No lore is written to justify an all-female population (reproduction, history, demographics). Terridyn is already about 78% female in canon, so the question doesn't come up.
- Every story fact holds. A character's role, relationships, history and secrets are the same in both casts. Only gendered words change: son becomes daughter, father becomes mother, he becomes she.

---

## The Rule: On Screen Is All-Female, Off Screen Is Unchanged

| Character is... | With All-Female Cast on |
|---|---|
| **Seen**: rendered in the world, in a cutscene, a photo, a portrait or a video feed | Female variant |
| **Heard**: speaks any voiced line, including phone calls, radio and broadcasts | Female variant |
| **Only mentioned**: named or referred to in dialogue, documents, notes or lore entries, but never seen or heard | **Canon gender, unchanged** |

Off-screen characters keep their canon gender, so the world outside the cast is still mixed. A male character who is only mentioned stays male. That includes absent parents, historical figures and people in the news.

A character who is ever seen or heard counts as on screen **everywhere**, including in text that only mentions them. A character is never male in a document and female in person within one playthrough.

---

## Selection

- Chosen on the **new-game screen** and stored in the playthrough's `content_config`, next to the content tier.
- **Locked for the life of the playthrough.** It can't be changed in Settings or mid-save. Each playthrough has one consistent set of names, voice lines and pronouns, and the save system never has to handle a switch.
- **Default: off** (the canon cast).
- New Game Plus and New Game Plus Plus started from a playthrough inherit its setting.
- **Independent of the content tier.** It works with Base, Spicy and Super Spicy, and with streamer presets.

---

## What Changes

| Area | With All-Female Cast on |
|---|---|
| **Named cast** | Every on-screen male character uses their female variant: name, body, voice, pronouns and family terms. The variant roster is kept in the private lore docs. |
| **Ambient population** | Crowds, random NPCs, job clients and enemies are spawned 100% female (canon ratio is ~78%). |
| **Romance** | Romance options with a male character become woman/woman romances with the female variant. Same beats, same gating. |
| **Party members** | Any male AI party member, for example in the Haunted House mode, uses a female variant. |
| **Dialogue** | Lines whose English wording changes get a variant line. Lines that only say the name use the variant's name. Translations are keyed by speaker and listener gender, so every language gets correctly gendered lines. |

## What Does Not Change

- The plot, quests, choices, gating values and outcomes.
- Off-screen characters (see the rule above).
- The canon cast. The male base body and male voices are still needed for every playthrough with the option off.

---

## Open Decisions

- **Sir Gallopington** (a male familiar who appears late in the game). Whether he gets a female variant is deferred until his chapter is in production.

---

## Cost Notes

- **Voice.** The VO is AI-generated ([Audio Direction](../gdd/audio-direction.md)), so a second voice per flipped character is a generation profile, not a recording session.
- **Dialogue.** The real cost is the number of lines that need a variant: lines spoken *about* a flipped character, and lines that use a gendered family term. Each of these is translated twice.
- **Bodies.** Female variants use the shared female base body, so the option needs no new base mesh.
