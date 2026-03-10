# Creative Brief — Skills Menu

---

## Overview

The Skills section of the phone menu is Meghan reading her own **S.T.A.L.K.E.R. profile**. It is not a player stat screen — it is a corporate AI's assessment of her capabilities, inferred from observed behaviour and certified through its licence system. The distinction matters for presentation: the display should feel like a government database or HR system, not an RPG character sheet. Clean, institutional, slightly cold.

S.T.A.L.K.E.R. background: [S.T.A.L.K.E.R.](../../mgaPriv/mechanics/stalker.md) *(private repo — full lore detail)*.

**Related documents:**

- [Skills Overview](../../mgaPriv/mechanics/skills.md) — full skill list, attribute pairing, progression model
- [CB-hud-modification](CB-hud-modification.md) — widget configuration (where purchased widgets are equipped)

---

## Use Cases

### UC1 — Viewing Meghan's STALKER Profile

**Actor:** The player
**Goal:** Review Meghan's current attributes, skill ranks, and progression toward next ranks
**Trigger:** Player opens the Skills section from the phone menu

**Step by step:**

1. **Profile header displays at the top.** Who STALKER thinks Meghan is:

   | Field | Content |
   |-------|---------|
   | Name | Meghan Higgins |
   | STALKER ID | Assigned on arrival to Terridyn |
   | Assessment status | Active / Under review / Flagged |
   | Last updated | Near-real-time timestamp — STALKER updates continuously |
   | Overall classification | Labour tier / skill bracket — changes as she advances |

2. **Attributes section shows twelve attributes** as current values with a trend indicator (rising / stable / declining) based on recent activity. Attributes are STALKER's high-level read on her capability domains — physical, mental, social, technical, and others.

3. **Skills section shows fifty-plus skills grouped by category.** Each skill entry shows:

   | Element | Description |
   |---------|-------------|
   | **Skill name** | Plain name — no flavour text in the list view |
   | **Current rank** | Numerical rank or named tier |
   | **Progress bar** | How far to the next rank; driven by relevant in-game activity |
   | **Licence badge** | One of four states — see below |
   | **Cap indicator** | Shown when rank ceiling is reached and a licence is needed to continue |

4. **All skills appear in one unified view** — civilian and magical girl alike. There is no separate magical girl section. Every skill has a progress bar showing how close it is to the next rank.

**Licence badge states:**

| State | Meaning |
|-------|---------|
| *(none)* | Below licence threshold; progress normally |
| **Eligible** | Rank threshold met; licence available to purchase |
| **Licensed** | Licence purchased; skill cap extended |
| **Cap reached** | At rank ceiling; licence required to continue — shown prominently |

**Skills are grouped into logical categories** (combat, technical, social, physical, magical, etc.). Categories are collapsible. A filter or search field allows finding a specific skill without scrolling the full list.

**The "last updated" timestamp** reinforces the fiction: STALKER is always watching. Every skill-relevant action she takes updates this number.

---

### UC2 — Purchasing a Licence

**Actor:** The player
**Goal:** Purchase a licence to extend a skill cap or unlock widget eligibility
**Trigger:** Player navigates to the Licences & Widgets section of the skills screen and selects an available licence

**Step by step:**

1. **Player browses the licence list.** Three states are visible:

   | State | Display |
   |-------|---------|
   | **Locked** | Skill threshold not met; shows required rank |
   | **Available** | Threshold met; shows purchase price and what it unlocks |
   | **Purchased** | Active; shows what it has unlocked |

2. **Player selects an available licence.** Each entry shows clearly what it unlocks — a skill cap increase, widget purchase eligibility, or both.
3. **Transaction completes as a digital purchase through STALKER.** Cost deducted in in-game currency, licence applied immediately to her profile.

**Widget entries in the skills menu** show widget licences Meghan holds and which widgets they make available for purchase. The skills menu does **not** configure widgets — that is done in the HUD Modification menu (see [CB-hud-modification](CB-hud-modification.md)).

Widget entries show:

| Element | Description |
|---------|-------------|
| Widget name | Plain name |
| Licence source | Which licence unlocks it |
| Status | Available to purchase / Purchased / Requires licence not yet held |

This gives the player a complete picture of what they have access to and what is still gated, without requiring them to navigate to the HUD menu to find out.

---

### UC3 — Viewing an Ally's STALKER Profile

**Actor:** The player
**Goal:** Review a party member's attributes, skills, and licence status via phone-to-phone share
**Trigger:** Meghan is physically close to Nisa, Nagi, or Arri and opens their profile tab

**Step by step:**

1. **First use: Meghan sends a share request from her phone.** The ally accepts. This is the moment of established trust — a brief interaction that plays out once per ally.
2. **Subsequent uses: no prompt.** The earlier acceptance implies standing permission. Meghan opens the ally's profile tab directly; the game treats it as understood that she asks and they hand it over.
3. **The ally profile view uses the same institutional layout** as Meghan's own readout, with a clear header identifying whose profile is displayed. A visual indicator distinguishes it from Meghan's own profile — the view is borrowed, not owned.
4. **Everything visible on their own skills screen is shown:** attributes, skill ranks, progress bars, and licence status. The full picture of how STALKER currently assesses them.

**The profile is read-only.** Meghan cannot purchase licences on their behalf, trigger any transactions, or save a copy of their data. When she closes the view or moves out of range, she sees nothing. There is no persistent record of what she looked at.

**The fiction:** All four of them have the same problem: STALKER profiles that don't match their civilian cover. A courier with maxed tactical skills. A student with combat certifications that shouldn't exist. Seeing each other's profiles is a moment of recognition — they can see exactly what STALKER sees, and it looks just as strange on Nagi and Nisa as it does on Meghan. Whether that's reassuring or alarming is up to the player to decide.

**The anomaly:** Meghan's STALKER profile carries a skill set that does not match her civilian employment history. A university student with maxed combat, magical, and tactical skills is an anomaly in the system. STALKER records this dispassionately. Nobody can pull her profile directly — the corporate pact means STALKER shares no personal data. But eligibility queries are a different matter. If someone queries whether she qualifies for a security contractor licence, or a combat specialist contract, STALKER will answer yes — and that answer is visible. The anomaly is not readable as a file, but it can be inferred from what she is eligible for. Whether anyone is running those queries is a separate question.

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
