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

## JSON Manifest Schema

Each entry in the mod index JSON represents one mod. Fields:

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

Each mod can have up to three separate download packages:

| Package | Purpose | Required |
|---------|---------|----------|
| **mod** | Core mod content — gameplay, assets, scripts | Yes |
| **localization_text** | Text strings, subtitles, UI text for supported languages | No |
| **localization_vocals** | VO audio for supported languages — large; optional download | No |

Separating these allows players to download only what they need. A player running the game in English does not need to download Japanese VO packs. The mod browser shows which packages are available and lets the player choose which to install.

---

## Dependency System

Each mod declares only its **direct dependencies** (one level). The game resolves the full tree recursively from these declarations.

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

## The Mod Manager Screen

The mod manager screen has three views and a server configuration panel. It is accessible from the main menu.

### Server Configuration

A panel accessible from the mod manager screen.

- **Catalog URL field** — points to a server hosting the catalog JSON (the mod index). The game fetches this file to populate the Available view
- Multiple server URLs are supported (add/remove list)
- Each server is fetched independently; results are merged in the Available view
- **Refresh** button — re-fetches all configured server URLs on demand
- Last refresh timestamp shown
- The game does not validate what is on the server beyond schema conformance — community servers are community responsibility

---

### View 1 — Available

Lists all mods found across all configured server URLs, excluding mods already installed.

**Filters and search:**
- Text search by mod name or author
- Filter by language support
- Filter by compatibility with current game version
- Filter: show incompatible versions (hidden by default)

**Mod card shows:**
- Thumbnail
- Name, author, version
- Compatibility indicator: ✓ compatible / ⚠ untested / ✗ incompatible with current build
- Languages supported
- Download size (mod package; text and vocals shown separately if available)
- Short description

**Install flow:**
1. Player clicks Install on a mod card
2. Game resolves the full dependency tree recursively
3. **Dependency validation:**
   - All dependencies available and compatible → confirmation screen lists: the selected mod + all dependencies to be installed, total download size, which packages (mod / text / vocals) will be downloaded
   - One or more dependencies are incompatible with the current game version, or the mod itself is not listed as compatible → **incompatibility warning popup** (see below)
   - A required dependency is not available on any configured server → installation is blocked, missing GUID reported
4. Player confirms → downloads begin (mod + all uninstalled prerequisites)
5. On completion, mod appears in the Installed view (disabled by default — player must enable it)

**Incompatibility warning popup:**

When the dependency chain includes a mod not validated for the current game version, a popup is displayed:

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

### View 2 — Installed

Lists all locally installed mods with their current state.

**Mod card shows:**
- Name, author, installed version
- **Enabled / Disabled** toggle
- Update available indicator (if server has a newer version)
- Which packages are installed (mod / text / vocals) — allows downloading missing packages after the fact
- Compatibility indicator with current game build
- Dependency status: all dependencies met / missing dependency (with GUID)

**Actions per mod:**

- **Enable / Disable** toggle — enables or disables the mod for the current playthrough. Disabling a mod does not uninstall it
- **Update** — downloads the newer version; shown only when an update is available
- **Delete** — permanently removes the mod from disk (see dependency cascade below)
- Download missing **text** or **vocals** packages if not previously installed

**Enable/disable behaviour:**

- Enabling a mod that has disabled dependencies will prompt: *"This mod requires [Mod X, Mod Y]. Enable them as well?"* — player can enable all or cancel
- Disabling a mod that other enabled mods depend on will prompt: *"The following mods depend on [this mod] and will also be disabled: [Mod A, Mod B]. Continue?"* — player confirms or cancels

**Delete behaviour — dependency cascade:**

Deleting a mod that other installed mods depend on triggers a cascade warning:

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

---

### View 3 — Load Order

Displays the current mod load order — the pak search order that the MBM will use when the session starts. This mirrors the search order system documented in [Boot Manager — Pak Search Order](../gdd/boot-manager.md).

**What the player sees:**

A vertical list of all enabled mods in their resolved load order, highest priority at the top. Mods higher in the list shadow assets from mods lower in the list — if two mods provide the same asset, the higher one wins.

The load order is **derived from the dependency graph**, not manually arranged. A mod that depends on another mod is always searched before its dependency (the dependent shadows the dependency). Mods with no dependency relationship to each other are ordered alphabetically or by install date (TBD).

**What the player can do:**

- View the resolved order to understand which mods take priority
- Identify potential conflicts — two mods at similar priority levels that may override each other's assets

The load order updates in real time as mods are enabled, disabled, or deleted.

---

## Per-Playthrough Activation

Installing a mod makes it available globally. **Activation is per-playthrough** — a mod must be enabled for a specific playthrough to affect it.

Mod activation for a playthrough is managed:
- During **New Game** creation (step 3 of the new playthrough flow — see [CB-main-menu](CB-main-menu.md))
- From the **Installed** view on the mod manager screen (enable/disable toggles apply to the active playthrough)

This allows a player to have the same mod installed but active only in one playthrough — e.g. active in a personal run, inactive in a streaming run.

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
