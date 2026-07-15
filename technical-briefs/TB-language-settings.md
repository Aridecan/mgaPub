# TB — Language Settings (Per-Module Localization Table)

> **Status: evolving draft (started 2026-07-13).** Peter is growing this as the design
> firms up. Sections marked **⚠ HOLE** are known-incomplete and awaiting design. This TB
> **supersedes the Language specifics** of [TB — Settings Menu](TB-settings-menu.md)
> (which modelled Language as one global category panel with live hot-swap); where the two
> disagree, this document wins for Language and the older one should be reconciled.

## Overview

This brief specifies the **Language settings screen** — the UI and backing systems that let
the player choose **text** and **voice** language **per content module** (Core, Spicy, Super
Spicy, and later mods), staged behind an explicit **Apply**.

It is the localization-selection front-end for the DLC/loc-pack architecture
([Content & DLC](../gdd/content-and-dlc.md), the `LocText_<Tier>_<Lang>` /
`LocVoice_<Tier>_<Lang>` packs) and the ABM's boot-time language resolution
([TB — Boot Loader](TB-boot-loader.md), B1–B3).

**Standing rule: reuse UE's own systems** ([reuse-ue](../gdd/boot-manager.md)). The screen is a
thin CommonUI front-end over: `FCulture` display names, `ComboBoxString`, a `ScrollBox`, the
pak/plugin manager for pack enumeration, and `Aridecan.ini` for persistence. We build only the
per-module model + the deferred-apply coordination the engine doesn't provide.

> **Engine-version caveat.** API names below are UE 5.8 (`d:/uegit`). Treat names as
> authoritative, exact signatures as navigational.

---

## The Screen (mockup `mga/Imports/SettingsLanguageMockUp.png`)

A **tabbed Settings shell**:

- Title **"Settings"**, a tab bar (**Language** | Game Play | Video | Audio | Controls |
  **Sensitive Content**), a content area, and shared **Back** (bottom-left) + **Apply**
  (bottom-right) buttons. *(Sensitive Content = the existing "Content" category of
  [TB — Settings Menu](TB-settings-menu.md) surfaced as a tab — adult/sensitive toggles +
  Streamer Mode; not a new system.)*
- The **Language tab** is a **table**:

| Module | Text | Voice |
|---|---|---|
| Core | `English (Canada)` ▾ | `English (Canada)` ▾ |
| Spicy | `English (GB)` ▾ | *(None)* |
| Super Spicy | … ▾ | … ▾ |
| *(enabled mods → rows, deferred)* | | |

- **Rows = content modules.** `Core` always present; `Spicy` / `Super Spicy` when their
  content is installed; **enabled mods appear as new rows** (deferred — see Open Items).
- **Text & Voice cells = `ComboBoxString`** populated with **built-in UE culture names**
  (`FCulture::GetNativeName()` / `GetDisplayName()`). No per-pack name embedding for standard
  cultures — the engine derives the display string from the culture code.
- **Both combos include `None`.** **Text** additionally defaults to **`English (Canada)`
  (en-CA)** as the always-present source/fallback for Core/Spicy/Super Spicy
  ([localization test strategy](TB-settings-menu.md)).
- **`ScrollBox`** — vertical scrollbar auto-enables when rows overflow.

---

## Apply Model — deferred / staged (KEY)

Language changes are **pending only**; nothing takes effect until **Apply** is pressed.
**Back** leaves without applying. This differs from `TB-settings-menu.md`'s "live hot-swap"
and is the authoritative model for Language.

Flow:
1. **On open** — read current applied settings → initialize each combo to its current value
   (pending := current).
2. **On combo change** — update the row's **pending** selection only; mark dirty (drives the
   Apply button enable state, mirroring `UGameUserSettings::Is*Dirty()`).
3. **On Apply** — commit pending → actually apply (see below) → persist to `Aridecan.ini` via
   the ABM config → clear dirty.
4. **On Back** — discard pending, pop the screen. *(Unsaved-changes confirm prompt — Open Item.)*

Mirrors UE's own set-vs-`ApplySettings()` separation; the Video/Audio tabs reuse
`UGameUserSettings` and slot into the same shell Apply.

---

## Reuse-UE Mapping

| Element | UE mechanism |
|---|---|
| Culture display names | `FCulture::GetNativeName()` / `GetDisplayName()` |
| Which cultures exist per module | pack enumeration via `IPluginManager` (the `LocText_*` / `LocVoice_*` plugins), see C++ Model |
| Tab bar + content swap | `CommonActivatableWidgetSwitcher` + tab buttons (or `CommonTabListWidgetBase`) |
| Scrollable table | `ScrollBox` + row widget now; `ListView` if row counts grow |
| Deferred apply / dirty state | pattern of `UGameUserSettings` (set vs `ApplySettings`, `Is*Dirty`) |
| Persistence | `Aridecan.ini` (ABM reads at boot, B1–B3) |
| Text apply (global case) | `UKismetInternationalizationLibrary::SetCurrentLanguage` / `SetCurrentCulture` + string-table reload |
| Voice apply | mount the chosen `LocVoice` pack per module (our system) |

---

## C++ Model

One `BlueprintCallable` builds the whole table; the widget just lays out rows + combos.
Lives in `UAridGLocPackLibrary` (OGMMGA game module — Regime-2 UObject; see
[naming conventions](TB-boot-loader.md)). Reuse-first: `IPluginManager` discovers packs, C++
returns **data**, Blueprint does the display.

```cpp
USTRUCT(BlueprintType)
struct FAridGLocChoice          // one entry in a Text/Voice combo
{
    GENERATED_BODY()
    UPROPERTY(BlueprintReadOnly) FString CultureCode;   // "" == None
    UPROPERTY(BlueprintReadOnly) FText   DisplayName;   // "English (Canada)", "None", "français"
};

USTRUCT(BlueprintType)
struct FAridGLanguageRow        // one row in the table
{
    GENERATED_BODY()
    UPROPERTY(BlueprintReadOnly) FString ModuleId;                     // "Base"/"Spicy"/"SuperSpicy"/mod
    UPROPERTY(BlueprintReadOnly) FText   ModuleName;                   // "Core"/"Spicy"/"Super Spicy"
    UPROPERTY(BlueprintReadOnly) TArray<FAridGLocChoice> TextChoices;  // None + en-CA + installed cultures
    UPROPERTY(BlueprintReadOnly) TArray<FAridGLocChoice> VoiceChoices; // None + installed cultures
};

UFUNCTION(BlueprintCallable, Category="Aridecan|Localization")
static TArray<FAridGLanguageRow> GetLanguageTable();
```

Fill logic:
- **Rows** = **`Core` always** (base game). **`Spicy` / `Super Spicy` rows appear ONLY when that
  DLC is installed** (its GameFeature plugin present/enabled) — never show a row for a tier the
  player doesn't own. Mods (held) follow the same "installed → row" rule later. Enumerate off the
  installed content modules, not a hardcoded list, so ownership drives the rows.
- **TextChoices** = `None` + `en-CA` (default) + distinct cultures from that module's
  `LocText_*` packs.
- **VoiceChoices** = `None` + cultures from its `LocVoice_*` packs.
- **DisplayName** via `FCulture`.

The current session's `GetInstalledTextPacks` / `GetInstalledVoicePacks` become private
helpers (or are removed) — `GetLanguageTable` subsumes them.

---

## ⚠ HOLE — Per-module TEXT language is not native UE

UE text localization has **one global active culture**; every `FText`/`.locres` resolves
against it. The table allows **Core=French while Spicy=English simultaneously**, which UE
**cannot do natively** — it would need a custom **per-namespace text-resolution layer**
(fighting the engine, against the reuse rule).

**Voice is fine** — we mount the chosen `LocVoice` pack per module (our own system), so
per-row voice is natural.

**Decision required before wiring real Apply (UNRESOLVED):**
- **(a) Text effectively global** — the column shows/sets one culture; per-module is
  voice-only for now. Cheap, native.
- **(b) Custom per-module text system** — powerful for partial fan translations; real
  engineering (per-namespace resolution, merged `.locres` load).

The UI + pending model are identical either way, so this only gates the Apply implementation.

---

## Current Implementation Status (2026-07-13)

Built this session as the **checkpoint before this redesign**:
- CommonUI activatable stack: `WBP_BootLayout` (hosts `CommonActivatableWidgetStack`) →
  `BP_BootPlayerController` creates it and pushes `WBP_MainMenu`.
- `WBP_MainMenu` reparented to `CommonActivatableWidget`; **Settings** button pushes
  `WBP_Settings`; **Back** pops via `DeactivateWidget`.
- `WBP_Settings` (CommonActivatableWidget) with a **v1** flat Text + Voice combo pair, labels
  from `ST_Settings`, populated from `UAridGLocPackLibrary::GetInstalledTextPacks/VoicePacks`
  (raw plugin names). **This v1 gets reworked into the table below.**

**Next (this redesign):** grow the C++ to `GetLanguageTable`; build `WBP_LanguageRow`
(Module label + Text combo + Voice combo); reshape `WBP_Settings` into the tabbed shell;
Language tab = `ScrollBox` of rows; pending model + Back(discard) / Apply(stub → real).

---

## Open Items / Holes (Peter growing)

- **⚠ Per-module text global-vs-custom** — the (a)/(b) decision above.
- **Mods as rows — HELD (decided 2026-07-15).** Deliberately **not** supported yet: the table
  is **Core / Spicy / Super Spicy only** for now; get the fixed-tier framework working first,
  then decide how mods fill the table. `GetLanguageTable` should still enumerate defensively so
  the model *can* grow, but no mod-registration path is built. Ties to
  [CB — Mod Manager](../creative-briefs/CB-mod-manager.md) when un-held.
- **Title-menu-only vs in-game** — `TB-settings-menu.md` D1 hides Language in-game. Does the
  per-module table keep that constraint? (Likely yes at boot; confirm.)
- **`None` semantics for Text** — what does Core Text = None render? (No text? source
  fallback anyway?) Probably disallow None for Core, or treat as en-CA.
- **Unsaved-changes prompt** on Back when dirty.
- **Persistence shape in `Aridecan.ini`** — per-module keys (`[Boot.Language] Core.Text=en-CA`,
  `Core.Voice=…`, …) vs a single global pair; reconcile with the ABM's current
  `TextLanguage`/`VoiceLanguage` scalar keys (B2).
- **File-location discrepancy** — `TB-settings-menu.md` D3 says `Documents/My Games/MGA/Config`;
  the implemented ABM writes `%LOCALAPPDATA%\OGMMGA\Config\Aridecan.ini`
  ([writable-data-locations]). Reconcile.
- **Pack display-name override** — optional per-pack `DisplayNameOverride` (data asset) for
  custom/dialect names the engine's CLDR name doesn't cover; hybrid with `FCulture` fallback.
- **Tabs** — which mechanism (`CommonTabListWidgetBase` vs simple button + switcher); who owns
  Apply across tabs (shell coordinates, each tab commits).
- *(add as they surface)*

---

## Related

- [TB — Settings Menu](TB-settings-menu.md) — broader settings; this supersedes its Language section
- [TB — Boot Loader](TB-boot-loader.md) — `Aridecan.ini`, language resolution B1–B3, CommonUI stack
- [CB — Settings Menu](../creative-briefs/CB-settings-menu.md) — the brief
- [Content & DLC](../gdd/content-and-dlc.md) — tiers, `LocText`/`LocVoice` packs, loc independence
- [Boot Manager](../gdd/boot-manager.md) — reuse-UE rule, language fallback
