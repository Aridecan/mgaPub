# CB — Skills Menu

## Overview

The Skills section of the phone menu is Meghan reading her own **S.T.A.L.K.E.R. profile**. It is not a player stat screen — it is a corporate AI's assessment of her capabilities, inferred from observed behaviour and certified through its licence system. The distinction matters for presentation: the display should feel like a government database or HR system, not an RPG character sheet. Clean, institutional, slightly cold.

S.T.A.L.K.E.R. background: [S.T.A.L.K.E.R.](../../mgaPriv/mechanics/stalker.md) *(private repo — full lore detail)*.

---

## What the Menu Shows

Three linked sections:

1. **Profile header** — who STALKER thinks Meghan is
2. **Attributes & Skills** — STALKER's current assessment of her capabilities
3. **Licences & Widgets** — what her assessment qualifies her to unlock and purchase

---

## Section 1 — Profile Header

Displayed at the top of the skills screen:

| Field | Content |
|-------|---------|
| Name | Meghan Higgins |
| STALKER ID | Assigned on arrival to Terridyn |
| Assessment status | Active / Under review / Flagged |
| Last updated | Near-real-time timestamp — STALKER updates continuously |
| Overall classification | Labour tier / skill bracket — changes as she advances |

The "last updated" timestamp reinforces the fiction: STALKER is always watching. Every skill-relevant action she takes updates this number.

---

## Section 2 — Attributes & Skills

### Attributes

Twelve attributes displayed as current values with a trend indicator (rising / stable / declining) based on recent activity. Attributes are STALKER's high-level read on her capability domains — physical, mental, social, technical, and others. Full attribute list in the mechanics documentation.

### Skills

Fifty-plus skills grouped by category. Each skill entry shows:

| Element | Description |
|---------|-------------|
| **Skill name** | Plain name — no flavour text in the list view |
| **Current rank** | Numerical rank or named tier |
| **Progress bar** | How far to the next rank; driven by relevant in-game activity |
| **Licence badge** | One of four states — see below |
| **Cap indicator** | Shown when rank ceiling is reached and a licence is needed to continue |

#### Licence Badge States

| State | Meaning |
|-------|---------|
| *(none)* | Below licence threshold; progress normally |
| **Eligible** | Rank threshold met; licence available to purchase |
| **Licensed** | Licence purchased; skill cap extended |
| **Cap reached** | At rank ceiling; licence required to continue — shown prominently |

Skills are grouped into logical categories in the display (combat, technical, social, physical, magical, etc.). Categories are collapsible. A filter or search field allows finding a specific skill without scrolling the full list.

### All Skills — Including Magical Girl Skills

All skills appear in the STALKER readout — civilian and magical girl alike. Every skill has a progress bar showing how close it is to the next rank. There is no separate magical girl section; the single view covers everything.

The implication for the fiction: Meghan's STALKER profile carries a skill set that does not match her civilian employment history. A university student with maxed combat, magical, and tactical skills is an anomaly in the system. STALKER records this dispassionately.

Nobody can pull her profile directly — the corporate pact means STALKER shares no personal data. But eligibility queries are a different matter. If someone queries whether she qualifies for a security contractor licence, or a combat specialist contract, STALKER will answer yes — and that answer is visible. The anomaly is not readable as a file, but it can be inferred from what she is eligible for. Whether anyone is running those queries is a separate question.

---

## Section 3 — Licences & Widgets

### Licences

Licences are purchased directly from this screen — a digital transaction through STALKER; cost deducted in-game currency, licence applied immediately to her profile.

Three states:

| State | Display |
|-------|---------|
| **Locked** | Skill threshold not met; shows required rank |
| **Available** | Threshold met; shows purchase price and what it unlocks |
| **Purchased** | Active; shows what it has unlocked |

Each licence entry shows clearly what it unlocks — a skill cap increase, widget purchase eligibility, or both. Players should never need to guess whether a licence is worth buying.

### Widgets

The Skills menu shows widget licences Meghan holds and which widgets they make available for purchase. It does **not** configure the widgets — that is done in the HUD Modification menu (see [CB-hud-modification](CB-hud-modification.md) *(forthcoming)*).

Widget entries in the skills menu show:

| Element | Description |
|---------|-------------|
| Widget name | Plain name |
| Licence source | Which licence unlocks it |
| Status | Available to purchase / Purchased / Requires licence not yet held |

This gives the player a complete picture of what they have access to and what is still gated, without requiring them to navigate to the HUD menu to find out.

---

## Presentation Notes

- The display reads as a **corporate system readout**, not a game UI. Typography, spacing, and colour treatment should feel institutional.
- Meghan is not the author of this document — STALKER is. The data is presented as an external assessment she is reading, not a menu she is filling out.
- Progress bars are present but understated — the screen is not celebratory. STALKER does not care that she levelled up; it recorded that her metrics changed.
- Licence eligibility is the one moment the screen can afford a clear highlight — it is actionable and represents a real decision point for the player.

---

## Open Items

- ~~Whether magical girl skills appear in STALKER~~ — confirmed: all skills including magical girl appear in one unified view with progress bars
- Full attribute list and groupings — in mechanics documentation
- Skill category groupings for display purposes
- Licence pricing — what currency, what scale
- Whether any licences are obtained through gameplay events (certifications from MCI/TMS/STCU) rather than purchased directly — the lore supports both; certifications from educational institutions feed into STALKER automatically
- Whether STALKER's "incomplete view" of Meghan is something she can observe — does she notice that her profile looks odd compared to what she knows she can do?
