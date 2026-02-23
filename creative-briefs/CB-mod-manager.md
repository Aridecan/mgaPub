# CB — Mod Manager

## Overview

MGA provides in-game mod management tooling but does not host a mod server. The community provides servers. The game connects to any server URL pointing to a valid JSON manifest and handles discovery, dependency resolution, download, and per-playthrough activation.

Modelled on Factorio's in-game mod manager.

### Design Philosophy — Mod Freedom

The game provides tools to load and run mods correctly. What those mods contain is the player's business.

MGA will not police mod content, enforce content tier compliance on mods, or add infrastructure to classify mods by content level. Any such system would only be as reliable as mod authors choosing to tag their work accurately — which cannot be enforced — making it a false assurance rather than a real one. Players installing mods take on responsibility for what those mods contain. The game's role ends at providing the tools to do it cleanly.

---

## Server URL Configuration

The mod manager has an input field for the mod server URL. This points to a JSON file — the mod index — hosted by a community server. The game fetches this file to populate the Available tab.

- Multiple server URLs are supported (add/remove list)
- Each server is fetched independently; results are merged in the Available tab
- A **Refresh** button re-fetches all configured server URLs on demand
- The game does not validate what is on the server beyond schema conformance — community servers are community responsibility

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

## UI — Tabs

The mod manager has two tabs and a server configuration section.

---

### Server Configuration

Accessible from both tabs via a settings icon or a dedicated sub-section.

- URL input field with Add / Remove controls
- Refresh button — re-fetches all server URLs and updates Available tab
- Last refresh timestamp shown

---

### Tab 1 — Available

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
2. Game resolves dependency tree
3. Confirmation screen lists: the selected mod + all dependencies to be installed, total download size, which packages (mod / text / vocals) will be downloaded
4. Player confirms → downloads begin
5. On completion, mod moves to the Installed tab

---

### Tab 2 — Installed

Lists all locally installed mods.

**Mod card shows:**
- Name, author, installed version
- Update available indicator (if server has a newer version)
- Which packages are installed (mod / text / vocals) — allows downloading missing packages after the fact
- Compatibility indicator with current game build
- Dependency status: all dependencies met / missing dependency (with GUID)

**Actions per mod:**
- **Update** — downloads the newer version; shown only when an update is available
- **Remove** — uninstalls the mod; warns if other installed mods depend on it
- Download missing **text** or **vocals** packages if not previously installed

---

## Per-Playthrough Activation

Installing a mod makes it available globally. **Activation is per-playthrough** — a mod must be enabled for a specific playthrough to affect it.

Mod activation for a playthrough is managed:
- During **New Game** creation (step 3 of the new playthrough flow — see [CB-main-menu](CB-main-menu.md))
- From within a playthrough via the phone's settings or a dedicated playthrough management screen (TBD)

This allows a player to have the same mod installed but active only in one playthrough — e.g. active in a personal run, inactive in a streaming run.

---

## Open Items

- GUID format: UUID v4 is recommended but not enforced by the schema; whether the game enforces a specific format or accepts any unique string is an implementation decision
- Whether the game provides any official starter server URL or leaves that entirely to the community
- Mod update behaviour: does updating a mod mid-playthrough require a save restart, or can it be applied immediately?
- Whether localization packages are tied to the mod's GUID or have their own GUIDs
- Whether the Available tab paginates or loads all entries (depends on expected server index size)
- Conflict resolution UI: if two installed mods are flagged as incompatible with each other, where and how is that surfaced?
- ~~Content mod gating~~ — explicitly out of scope; see Design Philosophy above
