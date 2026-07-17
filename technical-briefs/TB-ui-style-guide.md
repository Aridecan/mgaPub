# TB — UI Style Guide

> **Status: skeleton (started 2026-07-17).** Structure + locked constraints are in place;
> the concrete **values** (palette assignments, fonts, spacing numbers) are `TBD` and are
> Peter's art-direction calls. Fill the `TBD`s as decisions are made. Sections marked
> **⚠ DECIDE** gate implementation.

## Purpose & Scope

Single source of truth for the **look** of all in-game UI — main menu, settings, HUD, dialogs,
prompts, tooltips. Defines **colour, typography, spacing, and component states**, and the
**CommonUI style assets** that encode them so every widget conforms without per-widget hand-styling.

**In scope:** 2D/UI presentation (UMG / CommonUI / Slate widgets).
**Out of scope:** 3D / world / character rendering — that's [Art Direction](../gdd/art-direction.md)
(cel shader, materials, outlines). This brief *draws from* that document's palette; it does not
restate it.

**Why this exists:** it's the root-cause fix for the "raw default-Slate on a black background" class
of bug (e.g. the black-on-black combo dropdown and dark-on-dark tab labels we hand-patched in
`WBP_LanguageRow` / `WBP_Settings`). Once widgets consume shared styles, those symptoms can't recur.

**Standing rule — reuse UE:** encode the tokens as CommonUI style assets (`CommonButtonStyle`,
`CommonTextStyle`, `CommonBorderStyle`, a shared style set) and have widgets *reference* them. We build
only the token→palette binding the engine doesn't provide.

---

## Colour — palette-bound (LOCKED CONSTRAINT)

**Every UI colour is a named token that resolves to a specific entry in the fixed 98-entry palette**
([Art Direction → Colour Palette](../gdd/art-direction.md), `palette.png`). **No UI colour is ever a
raw hex value or a colour outside the palette** — this is the existing palette discipline ("Characters,
environments, and UI all speak the same colour language"; a new colour requires a palette discussion,
not a local override).

- **Palette source rows** (from Art Direction): Base chromatic (64), Signature (6), Flesh (8),
  **Greyscale (8 — explicitly designated "shadows, metals, neutral elements, UI")**, Blush (12).
  Expect UI **surfaces/text to map to Greyscale**, with **accents** from Base chromatic. **⚠ Signature
  colours are character/faction-reserved** — UI must not co-opt them except for deliberately
  character-themed surfaces.
- **⚠ DECIDE — palette addressing convention:** how a token cites a palette entry (grid `(row,col)` on
  the 16×7 grid? a named index? a palette-derived colour asset per entry?). Pick one; every table below
  uses it. A **palette-derived colour asset** per entry is preferred so a palette edit propagates to all
  UI automatically (see CommonUI Implementation).

### Semantic colour tokens (assign each to a palette entry — `TBD`)

| Token | Palette entry | Usage |
|---|---|---|
| `surface/app-bg` | TBD (greyscale) | root background behind everything |
| `surface/panel` | TBD (greyscale) | cards, menu panels, list backgrounds |
| `surface/raised` | TBD (greyscale) | popups, dropdown lists, tooltips |
| `surface/scrim` | TBD (greyscale + alpha) | modal dim behind dialogs |
| `border/divider` | TBD (greyscale) | separators, panel edges, table rules |
| `text/primary` | TBD | default readable text |
| `text/secondary` | TBD | labels, captions, less-emphasis |
| `text/disabled` | TBD | disabled controls' text |
| `text/inverse` | TBD | text on accent/light fills |
| `accent/interactive` | TBD (chromatic) | primary buttons, links, selection |
| `accent/hover` | TBD | hover state of interactive |
| `accent/pressed` | TBD | pressed/active state |
| `state/focus` | TBD | gamepad/keyboard focus ring (CommonUI nav) |
| `state/selected` | TBD | selected row / active tab |
| `status/danger` | TBD | destructive, errors |
| `status/warning` | TBD | cautions (e.g. B6 streamer advisory) |
| `status/success` | TBD | confirmations |

- **Contrast:** define a minimum contrast ratio for `text/*` on each surface (dark-background legibility
  — the failure mode we already hit). **⚠ DECIDE** the ratio target. Base-tier UI must read cleanly on
  stream (ties to Streamer Mode / content tiers).

---

## Typography

- **⚠ DECIDE — UI font family(ies).** Must cover the **shipped localization scripts** (ties to
  [TB — Language Settings](TB-language-settings.md) / the `LocText` packs) with a defined **fallback
  font** for scripts the primary doesn't cover (CJK, etc.). Record licence.
- **Type scale** — assign size / weight / line-height (`TBD`):

  | Level | Size / Weight / Line-height | Usage |
  |---|---|---|
  | Display | TBD | title screens, big moments |
  | Title | TBD | screen titles ("Settings") |
  | Header | TBD | section / tab headers |
  | Body | TBD | default paragraph / option text |
  | Label | TBD | control labels, combo items |
  | Caption | TBD | hints, footnotes |

- **Localization tolerance:** layouts must survive **text expansion** (some languages run longer) and
  RTL if ever in scope — favour wrap/auto-size over fixed widths.

---

## Spacing & Sizing

- **⚠ DECIDE — base unit** (e.g. a 4 px grid) and a **spacing scale** (`TBD`: xs/s/m/l/xl).
- Tokens (`TBD`): control height, list-row height, min gamepad/touch target, panel padding, gutter,
  corner radius, border width. All spacing in widgets references these — no ad-hoc pixel values
  (fixes the "header labels jammed together / misaligned columns" issue).

---

## Component States

For each component, map every visual state to the tokens above. States: **normal / hover / pressed /
focused / disabled** (+ selected where relevant). `TBD` fills.

- **Button** (`CommonButtonStyle`) — fill, text, border per state.
- **ComboBox** — closed control + **dropdown list item** (bg + `textColor`/`selectedTextColor`); the
  black-on-black we hand-fixed becomes `surface/raised` + `text/primary` here.
- **Tab** (tab bar buttons) — inactive / hover / **active** (selected); label colour per state (the
  dark-on-dark tab labels get fixed here).
- **ScrollBar** — track + thumb.
- **Text block** — maps to the Typography levels + `text/*` tokens.
- **List row** — normal / hover / selected (even/odd if used).
- **Panel / Border** — `surface/panel`, `border/divider`.
- **Tooltip** — `surface/raised` + `text/primary`.
- **Focus indicator** — `state/focus` ring; must be clearly visible for gamepad/keyboard nav.

---

## CommonUI Implementation

Encode the tokens as **reusable style assets** and have widgets reference them — never inline brushes
or per-widget colours.

- **Style assets:** `CommonButtonStyle`, `CommonTextStyle`, `CommonBorderStyle`, plus a shared
  **style/theme data asset** holding the token→palette values.
- **Palette binding:** each colour token resolves to a **palette-derived colour asset** (one per used
  palette entry) so editing the palette repaints all UI automatically — no per-widget re-tint. **⚠ DECIDE**
  the mechanism (colour data assets vs a runtime style subsystem).
- **Convention:** style assets live under a single UI style folder (propose `/Game/MGA/UI/Style/`);
  naming `Style_<Component>_<Variant>`. Widgets set their style from these; PRs that hardcode a colour
  or font get bounced to a token.
- **Adding a component:** define its states here → add/extend a style asset → widgets reference it.

---

## Accessibility & Streamer-Safety

- Minimum text contrast (see Colour) enforced on the dark surfaces.
- Colourblind consideration for `status/*` (don't rely on hue alone — pair with icon/shape/text).
- Focus visibility for controller/keyboard nav.
- Base-tier UI is **stream-safe by design**, consistent with the content-tier standard (Base = Twitch-safe).

---

## Adoption / Migration

Retrofit the existing widgets to the styles once tokens are assigned:
`WBP_BootLayout`, `WBP_MainMenu`, `WBP_Settings`, `WBP_LanguageRow`. The `WBP_LanguageRow` combo
colour hand-patch (white `itemStyle.textColor`) is replaced by the styled ComboBox spec above.

---

## Related

- [Art Direction](../gdd/art-direction.md) — **the fixed 98-entry palette (colour source)**, cel shader
- [TB — Settings Menu](TB-settings-menu.md) · [TB — Language Settings](TB-language-settings.md) — first consumers
- [Boot Manager](../gdd/boot-manager.md) — reuse-UE rule; [TB — Boot Loader](TB-boot-loader.md) — CommonUI stack
- Content-tier / Streamer-Mode standard — Base = stream-safe (visual too)

## Open Items

- **⚠ Palette addressing convention** + the palette-derived colour-asset mechanism.
- **⚠ Token → palette assignments** (every row in the colour table).
- **⚠ Font selection** + loc script coverage + fallback + licence.
- **⚠ Spacing base unit** + scale.
- Type scale values; component-state token maps.
- Runtime-swappable themes in scope? (e.g. high-contrast accessibility mode) — likely later.
- *(add as they surface)*
