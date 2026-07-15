# MGA — Content Architecture & DLC

## Overview

MGA is an adult game dealing with adult subject matter. It is designed to be modular so that it can be distributed across different platforms and regions without compromising the full experience for players who want it. The base game is suitable for Steam distribution; additional content is delivered through a structured DLC stack.

---

## Content Tiers

| Tier | Contents |
|------|----------|
| **Base Game** (aka **Core**) | Full story, full systems, Steam-ready. No content that would prevent distribution on mainstream platforms. **Outfit standard: all outfits meet the Twitch VTuber minimum-cover standard — Base is safe to stream/record by design.** (This is exactly what **Streamer Mode** enforces.) |
| **Spicy DLC** | Relaxes the coverage standard: **spicier outfit designs and full clothing removal**, plus the combat-driven clothing destruction system (tied to the in-game economy) and nudity. |
| **Super Spicy DLC** | Adds explicit content beyond nudity, including suggestive and sexual scenes. Full intended experience. |

**Outfit-coverage line (2026-07-15, art direction):** now fixed — **Base/Core = Twitch VTuber
minimum-cover standard; Spicy = spicier outfits + full clothing removal.** This is *why*
**Streamer Mode = the Base/Core preset**, and why the boot **B6 advisory** steers streamers/recorders
to the **Sensitive Content** settings tab to disable bannable (Spicy/Super Spicy) content — see
[TB — Settings Menu](../technical-briefs/TB-settings-menu.md) and
[TB — Boot Loader](../technical-briefs/TB-boot-loader.md). The explicit **Spicy ↔ Super Spicy**
boundary is still being refined; details in the clothing feature briefs.

---

## Localization Architecture

Localization is delivered as DLC in two separate packages per language:

| Package | Contents |
|---------|----------|
| **Text Translation** | All in-game text, UI strings, and subtitles |
| **Vocals / Voice-Over** | Dubbed audio for the target language |

Separating text and audio allows fan translators to contribute at whatever level they are able — text only, audio only, or both. Official localizations will use the same structure.

Text and voice packages are **fully independent** — they do not depend on each other and can be mixed across languages. A player can select English text with Japanese vocals, or any other installed combination. The two are selected via separate dropdowns in the settings menu. See [UI/UX — Localization Settings](ui-ux.md#localization-settings).

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
- [Boot Manager](boot-manager.md) — ABM/MBM phased lifecycle, pak registration names, dependency resolution, search order
- Feature Brief: Clothing Destruction System *(forthcoming)*
- Feature Brief: Localization Pipeline *(forthcoming)*
