# MGA — Content Architecture & DLC

## Overview

MGA is an adult game dealing with adult subject matter. It is designed to be modular so that it can be distributed across different platforms and regions without compromising the full experience for players who want it. The base game is suitable for Steam distribution; additional content is delivered through a structured DLC stack.

---

## Content Tiers

| Tier | Contents |
|------|----------|
| **Base Game** | Full story, full systems, Steam-ready. No content that would prevent distribution on mainstream platforms. |
| **Spicy DLC** | Adds mature content including the clothing destruction system (combat-driven, tied to in-game economy) and nudity. |
| **Super Spicy DLC** | Adds explicit content beyond nudity, including suggestive and sexual scenes. Full intended experience. |

The dividing line between tiers is still being refined. Details will be documented in the relevant feature briefs.

---

## Localization Architecture

Localization is delivered as DLC in two separate packages per language:

| Package | Contents |
|---------|----------|
| **Text Translation** | All in-game text, UI strings, and subtitles |
| **Vocals / Voice-Over** | Dubbed audio for the target language |

Separating text and audio allows fan translators to contribute at whatever level they are able — text only, audio only, or both. Official localizations will use the same structure.

**Planned launch languages:** English (primary), Japanese (secondary, machine-translated initially).

---

## Region Overrides

Different regions have different content sensibilities, platform requirements, and cultural expectations. Region override packages allow tailoring of the experience for a specific market without altering the base packages.

**Planned regions:**
- Americas (North and South)
- Oceania (Australia, New Zealand)
- Japan
- Korea
- Europe

---

## Load Stack

Packages are applied in the following order. Each layer builds on the one beneath it; region overrides have final authority over everything below.

```
Base Game
  └── Base Localization
        └── Spicy DLC
              └── Spicy Localization
                    └── Super Spicy DLC
                          └── Super Spicy Localization
                                └── Region Override
```

Official content packages (Base, Spicy, Super Spicy, localization, region overrides) are loaded during the **boot lifecycle** — all must reach a stable initialized state before the main menu becomes available. Mods are loaded separately during the **session lifecycle** when a new game is started or a save is loaded. See [Manifesto — Boot Lifecycle](manifesto.md#10-the-boot-lifecycle-is-supported) for the two-phase model.

---

## Related

- [Manifesto — Boot Lifecycle](manifesto.md#10-the-boot-lifecycle-is-supported)
- Feature Brief: Clothing Destruction System *(forthcoming)*
- Feature Brief: Localization Pipeline *(forthcoming)*
