# Creative Brief — Mod Manager

---

## Overview

MGA provides in-game mod management tooling but does not host a mod server. The community provides servers. The game connects to any server URL pointing to a valid JSON manifest and handles discovery, dependency resolution, download, and per-playthrough activation.

Modelled on Factorio's in-game mod manager.

**Related documents:**

- [Boot Manager](../gdd/boot-manager.md) — ABM/MBM lifecycle, pak search order, dependency resolution at boot
- [Manifesto](../gdd/manifesto.md) — Principles 3 and 4 (mod freedom)
- [Main Menu](CB-main-menu.md) — New Game flow (mod activation at step 3)
- [Settings Menu](CB-settings-menu.md) — in-game settings access

### Design Philosophy — Mod Freedom

The game provides tools to load and run mods correctly. What those mods contain is the player's business. See [Manifesto](../gdd/manifesto.md) — Principles 3 and 4 for the full statement. This brief does not add infrastructure to police or classify mod content; that decision is made at the manifesto level and applies here.

---

## Use Cases

### UC1 — Adding a Mod Server

**Actor:** The player
**Goal:** Configure one or more community server URLs so the mod browser can discover available mods
**Trigger:** Player opens the Server Configuration panel from the mod manager screen

**Step by step:**

1. **Player opens the server configuration panel.** A Catalog URL field points to a server hosting the catalog JSON (the mod index).
2. **Player adds a server URL.** Multiple server URLs are supported (add/remove list). Each server is fetched independently; results are merged in the Available view.
3. **Player clicks Refresh** to fetch all configured server URLs on demand. Last refresh timestamp is shown.

**The game does not validate what is on the server beyond schema conformance** — community servers are community responsibility.

---

### UC2 — Browsing and Installing a Mod

**Actor:** The player
**Goal:** Find a mod in the Available view and install it with all dependencies resolved
**Trigger:** Player opens the Available view in the mod manager

**Step by step:**

1. **The Available view lists all mods found across all configured servers,** excluding mods already installed.
2. **Player searches and filters.** Text search by mod name or author; filter by language support, compatibility with current game version, or show incompatible versions (hidden by default).
3. **Each mod card shows:** thumbnail, name, author, version, compatibility indicator (✓ compatible / ⚠ untested / ✗ incompatible), languages supported, download size (mod package; text and vocals shown separately if available), and short description.
4. **Player clicks Install on a mod card.** The game resolves the full dependency tree recursively.
5. **Dependency validation determines the next step:**
   - **All dependencies available and compatible** → confirmation screen lists the selected mod + all dependencies to be installed, total download size, and which packages (mod / text / vocals) will be downloaded.
   - **One or more dependencies are incompatible with the current game version** → incompatibility warning popup (see below).
   - **A required dependency is not available on any configured server** → installation is blocked, missing GUID reported.
6. **Player confirms.** Downloads begin (mod + all uninstalled prerequisites).
7. **On completion, mod appears in the Installed view** (disabled by default — player must enable it).

**Incompatibility warning popup:**

> **Potential Incompatibility**
>
> One or more mods in this install chain have not been validated for your current game version (v X.Y.Z):
>
> - Mod Name (validated for v X.Y.0)
>
> Installing may cause instability or unexpected behaviour.
>
> - **Install Anyway** — proceed at your own risk
> - **Cancel** — do not install

The game does not block installation — it warns and lets the player decide.

---

### UC3 — Managing Installed Mods

**Actor:** The player
**Goal:** Enable, disable, update, or delete installed mods while respecting dependency chains
**Trigger:** Player opens the Installed view in the mod manager

**Step by step:**

1. **The Installed view lists all locally installed mods with their current state.** Each mod card shows: name, author, installed version, enabled/disabled toggle, update available indicator, which packages are installed, compatibility indicator, and dependency status.

2. **Enable/Disable toggle** — enables or disables the mod for the current playthrough. Disabling does not uninstall.
   - Enabling a mod with disabled dependencies prompts: *"This mod requires [Mod X, Mod Y]. Enable them as well?"*
   - Disabling a mod that other enabled mods depend on prompts: *"The following mods depend on [this mod] and will also be disabled: [Mod A, Mod B]. Continue?"*

3. **Update** — downloads the newer version; shown only when an update is available.

4. **Delete** — permanently removes the mod from disk. Deleting a mod that other installed mods depend on triggers a cascade warning:

   > **Dependency Warning**
   >
   > The following installed mods depend on [Mod Name] and will also be deleted:
   >
   > - Dependent Mod A
   > - Dependent Mod B
   >
   > - **Delete All** — delete this mod and all dependents
   > - **Cancel** — keep everything

   The game will not leave orphaned mods with broken dependency chains. If a prerequisite is deleted, everything that requires it goes with it.

5. **Download missing packages** — text or vocals packages can be downloaded after the initial install.

---

### UC4 — Reviewing Load Order

**Actor:** The player
**Goal:** Understand which mods take priority and identify potential asset conflicts
**Trigger:** Player opens the Load Order view in the mod manager

**Step by step:**

1. **A vertical list shows all enabled mods in their resolved load order,** highest priority at the top. Mods higher in the list shadow assets from mods lower in the list — if two mods provide the same asset, the higher one wins.
2. **The load order is derived from the dependency graph, not manually arranged.** A mod that depends on another mod is always searched before its dependency (the dependent shadows the dependency). Mods with no dependency relationship to each other are ordered alphabetically or by install date (TBD).
3. **The player can identify potential conflicts** — two mods at similar priority levels that may override each other's assets.
4. **The load order updates in real time** as mods are enabled, disabled, or deleted.

This mirrors the search order system documented in [Boot Manager — Pak Search Order](../gdd/boot-manager.md).

---

### UC5 — Per-Playthrough Mod Activation

**Actor:** The player
**Goal:** Enable a mod for one playthrough while keeping it inactive in another
**Trigger:** Player creates a new game or changes mod activation for the active playthrough

**Step by step:**

1. **Installing a mod makes it available globally.** Activation is per-playthrough — a mod must be enabled for a specific playthrough to affect it.
2. **Mod activation is managed in two places:**
   - During **New Game** creation (step 3 of the new playthrough flow — see [CB-main-menu](CB-main-menu.md))
   - From the **Installed** view on the mod manager screen (enable/disable toggles apply to the active playthrough)
3. **This allows a player to have the same mod installed but active only in one playthrough** — e.g. active in a personal run, inactive in a streaming run.

---

## Reference

### JSON Manifest Schema

Each entry in the mod index JSON represents one mod:

```json
{
  "guid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "Mod Display Name",
  "version": "1.2.0",
  "author": "Author Name",
  "description": "Short description shown in the mod browser.",
  "thumbnail": "https://example.com/mod-thumbnail.png",

  "downloads": {
    "mod":                 "https://example.com/mod.zip",
    "localization_text":   "https://example.com/mod-text.zip",
    "localization_vocals": "https://example.com/mod-vocals.zip"
  },

  "languages": ["en", "fr", "de"],

  "compatible_versions":   ["0.3.0", "0.4.0"],
  "incompatible_versions": ["0.1.0", "0.2.0"],

  "dependencies":        ["guid-of-required-mod-a", "guid-of-required-mod-b"],
  "incompatible_mods":   ["guid-of-incompatible-mod"]
}
```

### Field Reference

| Field | Required | Notes |
|-------|----------|-------|
| `guid` | ✓ | Universally unique identifier for this mod. UUID v4 recommended. Never changes after publication — this is how dependency chains and compatibility records reference the mod permanently. |
| `name` | ✓ | Display name shown in the UI |
| `version` | ✓ | Semantic versioning recommended: `major.minor.patch` |
| `author` | ✓ | Display name of the author |
| `description` | ✓ | Short plain-text description; shown in the mod browser |
| `thumbnail` | — | URL to a preview image; placeholder shown if absent |
| `downloads.mod` | ✓ | URL for the main mod package |
| `downloads.localization_text` | — | URL for a text localisation package for this mod |
| `downloads.localization_vocals` | — | URL for a VO/vocals localisation package for this mod |
| `languages` | ✓ | Array of BCP 47 language codes the mod supports (e.g. `"en"`, `"fr"`, `"ja"`) |
| `compatible_versions` | ✓ | Game versions this mod is confirmed to work with |
| `incompatible_versions` | — | Game versions this mod is known to break on; prevents installation on affected builds |
| `dependencies` | — | Array of GUIDs of mods that must be installed for this mod to function. Direct dependencies only — one level. The game builds the full tree recursively. |
| `incompatible_mods` | — | Array of GUIDs of mods known to conflict. Installing one warns about the other. |

### Download Packages

| Package | Purpose | Required |
|---------|---------|----------|
| **mod** | Core mod content — gameplay, assets, scripts | Yes |
| **localization_text** | Text strings, subtitles, UI text for supported languages | No |
| **localization_vocals** | VO audio for supported languages — large; optional download | No |

Separating these allows players to download only what they need. A player running the game in English does not need to download Japanese VO packs.

### Dependency System

Each mod declares only its **direct dependencies** (one level). The game resolves the full tree recursively.

**Example:**
- Mod A declares: depends on [B, C]
- Mod B declares: depends on [D]
- Installing A → system resolves: needs B, C → B needs D → install order: D, B, C, A

**Behaviour:**
- When a player selects a mod to install, the dependency tree is resolved before any download begins
- If any dependency is not available on any configured server, installation is blocked and the missing GUID is reported
- The resolved tree is shown to the player as a confirmation list before downloading: *"Installing this mod will also install: Mod B, Mod C, Mod D"*
- **Circular dependency detection:** if resolution finds a cycle (A → B → A), installation is blocked and the cycle is reported
- Removing a mod that other installed mods depend on shows a warning listing the dependents and asks for confirmation

---

## Open Items

- GUID format: UUID v4 is recommended but not enforced by the schema; whether the game enforces a specific format or accepts any unique string is an implementation decision
- Whether the game provides any official starter server URL or leaves that entirely to the community
- Mod update behaviour: does updating a mod mid-playthrough require a save restart, or can it be applied immediately?
- Whether localization packages are tied to the mod's GUID or have their own GUIDs
- Whether the Available view paginates or loads all entries (depends on expected server index size)
- Conflict resolution UI: if two installed mods are flagged as incompatible with each other, where and how is that surfaced beyond the `incompatible_mods` field?
- Load order tiebreaker for mods with no dependency relationship — alphabetical, install date, or manual player ordering?
- Whether the player can manually override the derived load order for mods at the same dependency level
- Whether mod enable/disable state is per-playthrough or global (current design says per-playthrough, but the Installed view needs to know which playthrough is "active")
- Whether the mod manager is accessible during gameplay (from the phone/pause menu) or only from the main menu
- Download progress UI — progress bars, queue management, cancel/pause individual downloads
- Whether the game snapshots the mod state at session start or allows mid-session enable/disable (current boot-manager design implies session-start only)
- ~~Content mod gating~~ — explicitly out of scope; see Design Philosophy above
- ~~Per-playthrough activation path~~ — RESOLVED: New Game step 3 + Installed view enable/disable toggles
