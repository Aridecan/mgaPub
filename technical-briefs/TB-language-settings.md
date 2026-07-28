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

**CONFIRMED IN A PACKAGED BUILD (2026-07-27, `0.1.0-cxm1.36`).** With the culture set to French and
only the Core target translated, the content-advisory screen renders the base warning in **French**
and both tier warnings in **English** - because Spicy/SuperSpicy have no `fr` data and UE falls back
to their en-CA source per string. That is the partial-translation experience this decision predicted,
happening natively. **Option (b) stays unbuilt and is not needed for this case.**

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

## Implementation Status

### SHIPPED — the screen is built + verified (P4 changes 102, 104 & 105, 2026-07-16/18)

**C++ (`OGMMGA` game module):**
- `UAridGLocPackLibrary::GetLanguageTable()` → `TArray<FAridGLanguageRow{ModuleId, ModuleName,
  TextChoices[], VoiceChoices[]}>`, each choice `{CultureCode, DisplayName}` (`""` = None) via
  `FCulture`. Rows: **Core always**, Spicy/Super Spicy gated on `IPluginManager::FindEnabledPlugin`.
  **en-CA** synthesised as the head of each Text column (content-pack source); per-tier cultures parsed
  from the installed `LocText_` / `LocVoice_` packs (split on the **last** `_`, so `en-US` / hyphenated
  codes survive).
- `UAridGLanguageSettingsLibrary` — deferred-Apply persistence: `SaveLanguageSelections` /
  `LoadLanguageSelections` over the ABM's `Aridecan.ini` (local `FConfigFile`, **`Combine`-first to
  preserve other sections**, `bCanSaveAllSections`; path duplicated with a "must match" comment, no
  cross-module dep). `FAridGModuleSelection{ModuleId, TextCode, VoiceCode}` is the payload.

**Blueprints:**
- **`WBP_LanguageRow`** (UserWidget): `SetupRow` fills a Module label + two `ComboBoxString`;
  `IndexOfCulture` selects the en-CA default; `OnRowChanged` dispatcher fired by both combos'
  `OnSelectionChanged`; `GetRowSelection` returns the chosen codes. **`RestoreSelection(SavedSelections[])`**
  (P4 105) finds this row's entry by `ModuleId` (ForEach-with-Break) and sets `Cmb_Text`/`Cmb_Voice` via
  `IndexOfCulture`. *(Dropdown item text hand-set white — interim; real fix = the style guide.)*
- **`WBP_Settings`** (CommonActivatableWidget): **tabbed shell** (Language | Game Play | Video | Audio |
  Controls | Sensitive Content) = button bar + `WidgetSwitcher`; Language tab = `Scroll_Lang` filled in
  Construct (**`LoadLanguageSelections`** once → `GetLanguageTable` → CreateWidget → `SetupRow` → AddChild →
  **`RestoreSelection(row, saved)`** → bind `OnRowChanged`). **Restore runs before the bind** so re-selecting
  the saved combo value on open fires the row dispatcher into no listener → **Apply stays disabled on open**
  (same guard as `SetupRow`'s default-select). **Deferred Apply:** any *user* row change enables **Apply**;
  Apply gathers rows → `SaveLanguageSelections` → live `SetCurrentLanguage(Core.Text, SaveToConfig=false)` →
  clears dirty. **Back** = `DeactivateWidget`.

**Verified (PIE, P4 105):** apply Core Text → Back → reopen ⇒ combos show the persisted choice **and Apply is
disabled**; deleting the ini ⇒ combos fall back to en-CA text / None voice. Earlier Apply verification
(Standalone): change Core Text + Apply → UI switches live *and* writes `[Boot.Language] TextLanguage` (from
Core) + `[Settings.Language]` per-module keys, `[Boot.Content]` preserved.

### Remaining Work (what's left)

1. ~~**On-open init**~~ — **DONE (P4 105, 2026-07-18).** `WBP_Settings.Construct` loads the saved
   selections and each row restores its combos before binding. **Edge (documented, not special-cased):** a
   saved culture whose pack was *uninstalled* since resolves via `IndexOfCulture` to index 0 = **None**
   (not the en-CA default) — rare, and entangled with the still-open "None-semantics for Text" question;
   fold the fallback policy into that decision rather than branching here now.
2. **Voice apply (our system)** — mount the chosen `LocVoice` pack per module. Voice is currently
   **persisted but not acted on**; needs the runtime voice-pack mount + selection.
3. ~~**ABM applies the persisted language at boot**~~ - **DONE 2026-07-27 (P4 145), but NOT in B2.**
   `UAridGBootLanguageSubsystem` (a `UGameInstanceSubsystem` in the OGMMGA module) reads
   `FAridGBootSettings` and applies the persisted text language.
   **B2 cannot do this, and the original instruction here was wrong.** B1/B2 run at
   `PostConfigInit`; the engine initialises its own text localization much later
   (`PreObjectSystemReady`) and overwrites anything set before it - observed as B1 at frame 0 and
   the engine's culture decision ~1.6s afterwards. A GameInstance subsystem is the earliest
   correct point: after localization init and after loc packs mount, but before the boot map and
   any widget, so screens come up already translated with no flash of English.
   **Gotcha it must work around:** `InitGameTextLocalization` runs on every game/PIE start and,
   when the language has NOT changed, reloads with `ELocalizationLoadFlags::Game` only - omitting
   `Additional`, which is where plugin (loc-pack) data lives. A game starting *already* set to a
   pack-delivered language therefore drops that pack and falls back to source text. The subsystem
   calls `FTextLocalizationManager::RefreshResources()` (which does include `Additional`) even
   when the language is already active. Symptom if this regresses: choose French, restart, and the
   game is English while the editor stays French.
4. **⚠ Voice None-vs-unset wrinkle** — `FAridGBootSettings::LoadOrCreate` re-defaults an *empty*
   `VoiceLanguage` to `TextLanguage` **every boot**, clobbering an explicit **None** voice next launch
   (global `[Boot.Language]` scalar only — the `[Settings.Language]` per-module section is untouched, so
   the table still round-trips). Fix = a None sentinel (empty = None vs unset) + don't re-default when
   the file already exists. See the None-semantics + persistence open items.
5. **Unsaved-changes prompt** on Back when dirty (deferred from the pending model).
6. **Packaged-build visibility** — loc choices only appear where pack content is mounted; a packaged
   build shows only en-CA until the loadable loc packs are cooked into paks (deferred `TB-ci-cook`).
   See "Runtime visibility" open item.
7. **Visual** — header column alignment, tab-label colours, and the combo colour hand-patch all fold
   into [TB — UI Style Guide](TB-ui-style-guide.md) (palette-bound CommonUI style assets). No ad-hoc
   polish; conform the widgets to the guide.
8. **Other tabs** — Game Play / Video / Audio / Controls / Sensitive Content are placeholder pages.
   Video/Audio reuse `UGameUserSettings`; Sensitive Content = the Content category of
   [TB — Settings Menu](TB-settings-menu.md).

---

## Localization delivery - settled 2026-07-27 (P4 145)

**A LocText pack carries native `.locres`, not replacement StringTable assets.** UE has a purpose-
built path: the pack declares `LocalizationTargets` in its `.uplugin`, `FPluginManager` gathers
`<Pack>/Content/Localization/<Target>/`, and `HandleLocalizationTargetsMounted()` fires on mount
(with a matching unmount). The target NAME is only a folder label and need not match the declaring
plugin - which is what lets `LocText_Spicy_fr` deliver the Spicy tier's translations without the
Spicy tier carrying them. Replacement StringTables would be same-path shadowing, which
TB-boot-loader R10 rules out.

**The base game ships its SOURCE culture as a real localization target.**
`[Internationalization] +LocalizationPaths=%GAMEDIR%Content/Localization/Core` in `DefaultGame.ini`.
Not cosmetic: with no resource for en-CA, selecting French loaded the pack and replaced the display
strings, and selecting en-CA afterwards loaded *nothing*, so there was nothing to overwrite them
with - the UI stayed French while the active culture really was en-CA. A one-way door.

**...and only that culture is staged.** `[/Script/UnrealEd.ProjectPackagingSettings]
+CulturesToStage=en-CA`. The Core target folder holds working data for every culture we translate,
so without this the base build would swallow French instead of shipping it as a download. **Adding a
language to `CulturesToGenerate` must NOT add it here.** Note the leak-audit gate would not catch a
mistake: it asserts on DLC-delivered *plugin* content, and this is project content.

**Language names are ENDONYMS.** `FCulture::GetNativeName()`, not `GetDisplayName()` - "English
(Canada)" and "francais", each in its own language regardless of the active UI culture. Convention
everywhere (Steam, Windows, browsers) and for a reason: a player stranded in a language they cannot
read still has to find their way out. Side benefit - the labels no longer depend on the active
culture, so they cannot go stale when it changes. "None" still localizes; it is a UI word.

**A culture is only offered if the engine can actually resolve text for it.** The table intersects
each tier's pack-derived cultures with
`FTextLocalizationManager::GetLocalizedCultureNames(Game | Additional)`. A pack name is a claim;
localization data is proof. The `LocText_*_en-US` packs are the live example - a PrimaryAssetLabel
asset and zero translations, so they pass any file-based test while changing nothing on screen.
`Engine` is excluded from the flags on purpose: the engine ships its own fr/de/ja for engine
strings, which says nothing about whether MGA is translated. The source culture (en-CA) is always
offered regardless, so there is always a way back to the language the game is authored in.

### String-table NAMESPACES must be unique across ALL plugins (found 2026-07-28, P4 164)

A localized string's identity is `(namespace, key)` - nothing else. Not the asset, not the plugin,
not the pak it shipped in. Two string tables in two different plugins that share a namespace and a
key are **the same string** as far as the localization manager is concerned, and only one of their
translations can survive.

That is exactly what happened the first time a tier was translated. Base, Spicy and SuperSpicy each
had an asset called `ST_ContentAdvisory`, each with namespace `ST_ContentAdvisory` and a `Warning`
key. In English everything looked perfect - the advisory screen composed 1 -> 2 -> 3 lines through
every acceptance run and every packaged test, because a collision costs nothing while nothing is
translated. The moment French existed for the tiers, the engine said:

```
LogTextLocalizationResource: Warning: Text translation conflict for
                             namespace "ST_ContentAdvisory" and key "Warning".
```

and the screen showed the base line in French with both tier lines still in English.

**Rule:** a string table's namespace must be unique **project-wide**, including across plugins. The
base game's convention (namespace = asset name, e.g. `ST_AgeVerification`) is only safe while asset
names are unique; a tier reusing a base asset name silently breaks it. Tier tables are therefore
suffixed - `ST_ContentAdvisory_Spicy`, `ST_ContentAdvisory_SuperSpicy`.

**Two traps worth knowing when fixing one of these:**
- Renaming the asset leaves a **redirector**, and `FText::FromStringTable` takes a **path string** -
  so the editor's *Fix Up Redirectors* cannot see the reference and will not update it. The C++ path
  must be edited by hand.
- Changing the namespace changes the PO `msgctxt`, which **orphans existing translations** - the
  archive keys them to the old identity. Expect to re-apply them after any namespace change.

**How to check:** the PO's `msgctxt` is the namespace. After a gather, `msgctxt "<Namespace>,<Key>"`
should differ for every target. At runtime, grep the log for `Text translation conflict` - that
warning is the whole diagnosis, and it is the only thing that catches a collision an English build
cannot show you.

### Who translates: the COMMUNITY, not us (Peter, 2026-07-28)

**We ship en-CA only.** MGA does not ship translations - it ships the *pipeline and the format* that
let the community produce them, plus enough documentation to follow it (#13). Same for voice: English
from us, possibly some AI-generated samples in other languages, but never the finished article.

This is a design constraint, not a resourcing note, and it reorders the priorities:

- **Key stability is the headline requirement.** When we own the translations an orphaned string costs
  an afternoon. When a volunteer has translated thousands of lines and a typo fix silently orphans
  them, they do not come back - and they are under no obligation to. Nothing about our convenience
  justifies spending their time twice.
- **The PO file is the product.** The `.locres` is just what our pipeline compiles; what a translator
  actually receives is a PO. Its quality - stable keys, and enough context to translate correctly -
  IS the deliverable.
- **Format stability across versions matters** more than it would for an in-house pipeline.
- MT en->fr is a **test instrument**, not a product: something Peter can half-read to confirm the
  machinery still works. It is not on a path to shipping.

### Dialogue: UDialogueWave (decided 2026-07-28, provisional)

Chosen over string tables or a data table. Provisional by explicit intent - "we'll go with
DialogueWave for now and see how it goes, but the pipeline will get changed as needed."

Why it fits the community-translation constraint better than anything we would build:

- **Keys are GUID-derived, not text-derived** (`ContextMapping.GetLocalizationKey(LocalizationGUID)`),
  so **editing the spoken text does not orphan the translation**. A string table cannot do this - its
  key is whatever was typed, which is exactly how two translations were orphaned on 2026-07-28.
- **Grammatical context is modelled, not commented.** `UDialogueVoice` carries `Gender`
  (Neuter/Masculine/Feminine/Mixed) and `Plurality`, and a context is *speaker + targets*, so a line's
  key varies by who addresses whom. That is the `tu`/`vous` and adjective-agreement problem solved
  structurally. A PO line without it is a line a translator has to guess at.
- **The metadata reaches the translator.** The gather writes `Speaker`, `Targets`, `Gender`,
  `Plurality`, `TargetGender`, `TargetPlurality`, `VoiceActorDirection` and `AudioFile` into the PO.
- **`VoiceActorDirection` is the common source for voice.** The same free-text acting direction that
  would tell a human VA "weary, trailing off" is what feeds an emotion-controllable TTS (Peter is
  leaning toward indexTTS2), and it ships to voice-pack makers in the same PO the translators get.
  One field, three consumers: the game, the translator, the voice-pack maker.
- **`SoundWave` is nullable** in a context mapping, so dialogue with context and no audio is
  structurally fine - which is our case.

**Known cost, accepted for now:** one `DialogueWave` asset per line. Ten thousand lines is ten
thousand assets. If the authoring ergonomics do not hold at volume, the fallback is to author
externally and GENERATE the assets - not to hand-roll the metadata model above.

**Consequence for namespaces:** dialogue is out of scope for the naming scheme. The engine fixes the
namespace to `Dialogue` / `DialogueNotes` and guarantees uniqueness through the GUIDs. The scheme only
needs to cover UI and reference content.

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
  fallback anyway?) Probably disallow None for Core, or treat as en-CA. **Related (open):** an empty
  code currently doubles as both **None** (user choice) and **unset** (first launch); the ABM can't
  tell them apart and re-defaults empty voice — needs a sentinel decision (see Remaining Work #4).
- **Unsaved-changes prompt** on Back when dirty.
- ~~**Persistence shape in `Aridecan.ini`**~~ — **RESOLVED (shipped, P4 104):** BOTH — per-module keys
  under `[Settings.Language]` (`Base.Text`, `Base.Voice`, `Spicy.Text`, …; `""` = None) for the table
  round-trip, PLUS the ABM's existing global `[Boot.Language] TextLanguage`/`VoiceLanguage` scalar pair
  driven from the **Core** row (Option a). `Combine`-first preserves other sections. *(Boot-apply of the
  scalar + the None/unset sentinel are Remaining Work #3/#4.)*
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
