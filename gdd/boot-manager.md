# MGA — Boot Manager

---

## Overview

MGA replaces Unreal Engine's default pak file loader with two custom boot managers that share the same phased lifecycle but operate in different contexts with different naming conventions.

| | ABM (Aridecan Boot Manager) | MBM (Mod Boot Manager) |
|---|---|---|
| **Runs during** | Boot lifecycle (before main menu) | Session lifecycle (game start / save load) |
| **Looks in** | Official data directory | Mod directory |
| **Package IDs** | Predefined names | GUIDs |
| **Dependency refs** | Predefined names | GUIDs |
| **Missing dependency** | Automatically excluded (known topology) | Warning dialog with player choice |

The ABM must complete successfully before the main menu becomes available. The MBM runs when a new game is started or a save is loaded. See [Manifesto — Boot Lifecycle](manifesto.md#10-the-boot-lifecycle-is-supported).

---

## ABM Package Names

Each official pak file registers with a predefined name. The ABM uses these names to resolve dependencies and build the search order.

### Core

| Name | Contents |
|------|----------|
| `Main` | Base game — full story, systems, Steam-ready content |
| `Spicy` | Mature content tier (clothing destruction, nudity) |
| `SuperSpicy` | Explicit content tier |

### Demo Variants

| Name | Contents |
|------|----------|
| `Demo` | Base game demo (Chapter 1 + 3 side jobs) |
| `SpicyDemo` | Demo with Spicy-tier content |
| `SuperSpicyDemo` | Demo with Super Spicy-tier content |

Demo builds ship **only** the Demo variant paks — `Main`, `Spicy`, and `SuperSpicy` are not included. This keeps the demo distribution small and prevents the demo from being hacked into the full game, since the full game assets are not present.

### Localization — Text

| Pattern | Example |
|---------|---------|
| `LocText-<language>` | `LocText-en`, `LocText-ja` |
| `LocTextSpicy-<language>` | `LocTextSpicy-en` |
| `LocTextSuperSpicy-<language>` | `LocTextSuperSpicy-en` |

### Localization — Voice

| Pattern | Example |
|---------|---------|
| `LocVoc-<language>` | `LocVoc-en`, `LocVoc-ja` |
| `LocVocSpicy-<language>` | `LocVocSpicy-en` |
| `LocVocSuperSpicy-<language>` | `LocVocSuperSpicy-en` |

### Region Overrides

| Pattern | Example |
|---------|---------|
| `regionOverride-<region>` | `regionOverride-americas`, `regionOverride-japan` |

---

## Pak Search Order

After all packages register and declare their dependencies, the boot manager builds a **search order** based on the dependency graph. Packages higher in the dependency chain are searched first — their assets shadow those in packages lower in the chain.

This means a region override's version of an asset is found before the base game's version, and a Spicy-tier asset overrides a base-tier asset, all without conditional logic. The pak file system handles the layering naturally.

### Localization Independence

Text and voice localization packages are **independent** — they do not depend on each other. A player can select English text with Japanese vocals, or any other combination of installed languages. The two selections are made separately in the settings menu (see [UI/UX — Localization Settings](ui-ux.md#localization-settings)).

Each localization package depends only on its content tier (e.g. `LocTextSpicy-ja` depends on `Spicy`, not on `LocVoc-ja` or `LocText-ja`).

### Example search order (full install, Americas, English text, Japanese vocals)

```
regionOverride-americas         ← searched first
LocVocSuperSpicy-ja
LocTextSuperSpicy-en
SuperSpicy
LocVocSpicy-ja
LocTextSpicy-en
Spicy
LocVoc-ja
LocText-en
Main                            ← searched last
```

The exact order is derived from the dependency declarations in each pak, not from a hardcoded list.

---

## Phased Lifecycle

### ABM — Bootstrap Before Main Lifecycle

The ABM has an additional bootstrap sequence that runs **before** the main phased lifecycle. Any screen shown to the player during boot — loading screens, age gates, content warnings — must display localized text, so the base text localization package must be available first.

**ABM bootstrap sequence:**

1. Read the **global config file** (persisted outside any save — language selection, graphics settings, audio settings)
2. Determine the **text language** using the following fallback chain:
   1. Player's saved language selection from the global config
   2. If no global config exists (first launch): detect the **host OS locale**
   3. If no localization package exists for the detected language: fall back to **English**
   4. If the English localization package is not installed: load the **first available** `LocText-*` package found
3. Load `Main` and the selected `LocText-<language>` package
4. Begin the **loading screen sequence** — all loading screen text is now localized

The loading screen sequence includes any required pre-menu gates. For example, the mature audience confirmation ("This game is intended for a mature audience. If you are 18 or older, please select Yes.") must be displayed in the player's language, not hardcoded in English. This is why the text localization package loads before anything else is shown.

After the bootstrap and loading screen sequence completes, the ABM proceeds with the main phased lifecycle for all remaining packages.

### Main Phased Lifecycle

Both ABM (post-bootstrap) and MBM use the same four-phase lifecycle. Each phase completes across all registered packages before the next phase begins.

### Phase 1 — Load

The boot manager scans its data directory and opens each pak file. Inside each pak it finds the **load vector** (a blueprint/entry point) which registers the package with the event system. Registration includes:

- The package's **name** (predefined for ABM, GUID for MBM)
- The package's **dependency list** (names or GUIDs it requires)

After all paks are registered, the boot manager resolves the dependency graph and builds the pak search order. For the ABM, packages already loaded during bootstrap (`Main` and the base `LocText-*`) participate in the lifecycle but skip re-loading.

### Phase 2 — Init

The boot manager broadcasts an **Init** event to all registered packages. Each package performs basic initialization — primarily registering itself with its declared dependencies so they know it exists. When a package has completed its init work, it replies to the boot manager with an **Inited** signal.

The boot manager waits until all registered packages have replied before proceeding.

### Phase 3 — Config

The boot manager broadcasts a **Config** event to all registered packages. During this phase:

1. Each package that has **no dependencies** begins configuring immediately
2. Each package that **has dependencies** waits until it receives a **Configed** signal from every dependency it declared
3. Once a package finishes configuration, it sends a **Configed** signal to any packages that declared it as a dependency, and replies to the boot manager

The boot manager waits until all registered packages have replied before proceeding. This phase ensures configuration happens in dependency order without the boot manager needing to know the order itself — the packages self-coordinate through their dependency signals.

### Phase 4 — Ready

The boot manager broadcasts a **Ready** event to all packages, then transitions to **BeginPlay** (or the next required UE sequence).

For the ABM, Ready means the main menu becomes available. For the MBM, Ready means the gameplay session begins.

---

## Missing Dependency Handling

### ABM — Known Topology

The ABM knows the full dependency graph of official packages. If a package is missing, the ABM automatically excludes everything that depends on it.

Examples:
- `Spicy` not installed → `LocTextSpicy-*`, `LocVocSpicy-*`, `SuperSpicy`, and all Super Spicy localizations are excluded
- `LocVoc-ja` not installed → Japanese voice-over is unavailable; text localization still functions independently

No user interaction is required. The ABM handles missing official packages silently because the dependency topology is known and finite.

### MBM — Unknown Topology

The MBM cannot predict what mods exist or what they depend on. If a mod declares a dependency on a GUID that is not registered:

The game displays a warning dialog:

> **Missing Mod Dependencies**
>
> One or more mods require packages that are not installed.
>
> - **Continue** — proceed without the missing packages (may cause instability)
> - **Return to Main Menu** — cancel session load

The MBM does not attempt to resolve or work around missing mod dependencies. The player is informed and makes the choice.

---

## Related

- [Manifesto — Boot Lifecycle](manifesto.md#10-the-boot-lifecycle-is-supported)
- [Content Architecture & DLC](content-and-dlc.md) — content tier definitions and load stack
- [Save System — Extension-Safe Persistence](save-system.md#extension-safe-persistence) — how mods persist data through the same GUID namespace

---

## Open Items

- Exact load vector format inside pak files (blueprint class, interface, or convention)
- Whether the ABM search order is logged to a file for debugging
- Whether the MBM allows partial loading (load the mod but disable features that depend on the missing package) or treats it as all-or-nothing per mod
- Whether demo variant paks use the same dependency chain as the full game (e.g. `SpicyDemo` depends on `Demo` the same way `Spicy` depends on `Main`)
- Config phase timeout — what happens if a package never sends its Configed signal (infinite wait vs timeout with error)
- Whether the MBM runs its Load phase before or after the save file is read (current design: mods load first, then save chunks are dispatched)
