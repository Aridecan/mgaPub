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

### Where the source lives (2026-07-15 refinement)

**The en-CA source string table ships *inside* each tier's own content pack** — Core's en-CA
lives with the Core content pack, Spicy's en-CA with the Spicy content pack, Super Spicy's with
its own. So en-CA is present exactly when that tier's row is shown (the row only appears when the
tier is installed). This may differ from the original design that treated loc as separate from
content; it is now the authoritative model.

**The `LocText_<Tier>_<Lang>` packs are translations layered *on top* of that en-CA source.**
That is why `GetLanguageTable` synthesises en-CA as the head of every module's Text column (it is
the content-pack source, not a `LocText_` pack) and then appends the installed `LocText_` cultures.

**Pack naming (updated 2026-07-15):** text packs renamed `LocText_<Tier>_EN` →
**`LocText_<Tier>_en-US`** (a real culture code that `FCulture` resolves to "English (United
States)"; a US-English *translation* distinct from the en-CA source). **Voice packs stay
`LocVoice_<Tier>_EN`** — no meaningful en-US/en-CA audio difference, and a future
`LocVoice_<Tier>_en-US` would slot in with no code change. Plugin names may contain hyphens
(`-` is not in `INVALID_LONGPACKAGE_CHARACTERS`), so the culture-code token in the fan-loc
template is safe. *(Cosmetic follow-up: each pack's `PAL_LocText_<Tier>_EN` label asset kept its
old name through the move — rename in-editor when convenient.)*

**Open question — voice source parity:** if each content pack also ships a default en-CA
voiceover, the Voice column should get the same always-present en-CA head as Text. Currently Voice
is `None` + installed `LocVoice_` cultures only (no forced default). Decide when VO lands.

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

## Per-module TEXT language is not native UE — RESOLVED: Option (a) for now (2026-07-15)

UE text localization has **one global active culture**; every `FText`/`.locres` resolves
against it. The table *displays* a Text culture per module, but UE cannot natively hold
**Core=French while Spicy=English simultaneously** — that would need a custom per-namespace
text-resolution layer (fighting the engine, against the reuse rule).

**Decision (Peter, 2026-07-15): go with Option (a) — Text is effectively global — and reuse
the engine.** Rationale: UE already falls back to the **en-CA source string** for any `FText`
a module hasn't translated, so a global culture of French with an untranslated Spicy shows
Spicy's en-CA source automatically — which *is* the "Core in French, Spicy in English"
partial-translation experience, natively and for free. Option (b)'s only unique power (forcing
a module to a *different already-available* language than the global one) is a niche not yet
justified.

**Implementation of (a):**
- Keep the per-module Text column in the UI **and persist every row's Text choice** to
  `Aridecan.ini` (so the model/UI never change if we later adopt (b)).
- **Apply drives one global culture from the Core row** via
  `UKismetInternationalizationLibrary::SetCurrentLanguage` / `SetCurrentCulture`.
- **Voice stays genuinely per-module** — we mount the chosen `LocVoice` pack per module (our
  own system), so per-row voice is natural.

### Option (b) — custom per-module text system (DOCUMENTED for later pickup, not built)

Recorded so a future switch is a pickup, not a from-scratch rebuild. The UI + pending model +
`Aridecan.ini` per-module persistence are **already (b)-ready** — only the Apply *commit* changes.
To adopt (b):
1. **Per-namespace/-module text source.** Give each content module its own text namespace (Core /
   Spicy / SuperSpicy already map to modules). Resolve `FText` per namespace against that module's
   chosen culture instead of the single global culture.
2. **Merged `.locres` load.** At Apply, load each module's `.locres` for its chosen culture and
   register them together (per-culture, per-namespace), rather than swapping one global culture.
   Likely a custom `ILocalizedTextSource` (`FTextLocalizationManager::RegisterTextSource`) that
   routes lookups by namespace → module → chosen culture, with en-CA source fallback.
3. **Boot path (ABM B2)** reads the per-module keys and registers the same per-module sources at
   startup instead of setting one culture.
Cost: real engineering (custom text source, per-namespace routing, fallback ordering). Value:
partial fan translations that *downgrade* an available language per module — niche today.

**Expect rework:** Peter's standing note — many of these designs will be reworked as development
progresses; (a) is the low-cost path that keeps (b) cheap to reach.

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
- **Runtime visibility = mounted content, not just "enabled" (2026-07-16).** `GetLanguageTable`
  enumerates via `IPluginManager::GetEnabledPluginsWithContent()`, which only surfaces loc plugins
  whose **content is mounted in that runtime**. In PIE / Standalone-from-editor the loc plugins are
  mounted as loose content, so en-US text + English voice appear. In a **packaged/cooked build they
  will NOT appear until the loadable loc plugins are cooked into paks/chunks** (deferred `TB-ci-cook`
  Stage 2–5 + chunkId numbering; currently `chunkId = -1`). Until then a packaged build correctly
  shows only the always-present **en-CA** source on the Core row. This is the "installed/mounted →
  choice" gating degrading gracefully, not a bug. Ties the language screen's packaged-build
  completeness to the DLC cook work.
- *(add as they surface)*

---

## Related

- [TB — Settings Menu](TB-settings-menu.md) — broader settings; this supersedes its Language section
- [TB — Boot Loader](TB-boot-loader.md) — `Aridecan.ini`, language resolution B1–B3, CommonUI stack
- [CB — Settings Menu](../creative-briefs/CB-settings-menu.md) — the brief
- [Content & DLC](../gdd/content-and-dlc.md) — tiers, `LocText`/`LocVoice` packs, loc independence
- [Boot Manager](../gdd/boot-manager.md) — reuse-UE rule, language fallback
