# CB — Settings Menu

## Overview

The settings menu is the primary out-of-character configuration layer. It is accessed via the **Game Settings** option in the out-of-character menu (see [UI/UX](../gdd/ui-ux.md)), available at any time regardless of phone status.

Visual language: **clean and minimal**. The settings menu is functional infrastructure, not a showcase. Flat, readable layout. No decorative chrome that would need to be redesigned later. Designed to be built on, not finished.

Supports **full controller and keyboard/mouse input from launch**. The UI auto-detects the last-used input device and swaps prompts accordingly ("Press A" / "Press Enter"). No manual selection required.

---

## First-Launch Wizard

On first launch only, a skippable wizard runs before the main menu. It covers the settings most likely to affect comfort and compliance from the first minute of play.

**Steps:**
1. **Language** — game language and subtitle language
2. **Display** — resolution and window mode only; full display settings available in the menu
3. **Content** — adult content categories; age acknowledgement required to unlock anything above Base

A **Skip** option is available at any point. Skipping sends the player directly to the main menu with defaults applied. The wizard does not repeat on subsequent launches. All wizard settings are adjustable in the settings menu at any time.

The streamer/content-creator question from the save system first-boot flow is incorporated into the **Content** step of this wizard rather than appearing as a separate dialog.

---

## Menu Structure

### 1. Display

**Main settings** (always visible):

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Window Mode | Dropdown | Borderless Windowed | Fullscreen / Borderless Windowed / Windowed |
| Aspect Ratio | Dropdown | 16:9 | See aspect ratio table below |
| Resolution | Dropdown | Native | See resolution table below; hidden in Windowed mode |
| Refresh Rate | Dropdown | Max available | Fullscreen only |
| VSync | Toggle | On | |
| Quality Preset | Dropdown | High | Low / Medium / High / Ultra / Custom |
| Field of View | Slider | 90° | Range: 70°–110° |
| Brightness | Slider | 50% | |
| HDR | Toggle | Auto | Greyed out if display does not support HDR |

#### Aspect Ratios

| Aspect Ratio | Common use | Example resolutions |
|--------------|-----------|---------------------|
| **16:9** | Standard widescreen — the most common monitor format | 1920×1080, 2560×1440, 3840×2160 |
| **16:10** | Slightly taller; common on older monitors, some laptops | 1920×1200, 2560×1600, 3840×2400 |
| **21:9** | Ultrawide | 2560×1080, 3440×1440, 5120×2160 |
| **32:9** | Super ultrawide (Samsung Odyssey and similar) | 3840×1080, 5120×1440 |

#### Resolutions — Fullscreen and Borderless Windowed

The resolution dropdown is **populated from the display's supported modes** at runtime — only resolutions the connected monitor actually supports are shown. The list is filtered by the selected aspect ratio.

**Minimum supported resolution: 1920×1080.** Resolutions below 1080p are not offered. The game is not designed or tested below this threshold.

**Every resolution entry includes its aspect ratio label.** Players should never have to calculate whether a resolution matches their streaming setup. Format:

```
1920×1080  (16:9)
2560×1440  (16:9)
3840×2160  (4K · 16:9)
1920×1200  (16:10)
2560×1600  (16:10)
2560×1080  (21:9 Ultrawide)
3440×1440  (21:9 Ultrawide)
5120×2160  (21:9 Ultrawide)
3840×1080  (32:9 Super Ultrawide)
5120×1440  (32:9 Super Ultrawide)
```

The aspect ratio label appears in a muted secondary colour alongside the resolution — present and readable without competing with the numbers.

Reference resolution list by aspect ratio (all subject to monitor support):

| 16:9 | 16:10 | 21:9 | 32:9 |
|------|-------|------|------|
| 1920×1080 | 1920×1200 | 2560×1080 | 3840×1080 |
| 2560×1440 | 2560×1600 | 3440×1440 | 5120×1440 |
| 3840×2160 | 3840×2400 | 5120×2160 | — |

#### Windowed Mode — Free Resize

> **Platform note:** Aspect-ratio-locked window resizing requires OS-level window message handling (e.g. WM_SIZING on Windows) that Unreal does not provide out of the box. Feasibility must be confirmed on Windows, Linux, and Mac before committing to this feature. **If cross-platform implementation is not straightforward, drop the free-resize behaviour entirely and use the standard resolution dropdown in Windowed mode instead.** Do not carry this as a partial implementation.

If implemented on all three platforms:

In **Windowed** mode the resolution dropdown is hidden. The player resizes the window freely by dragging the window border. The selected **Aspect Ratio** is enforced during resize — dragging any edge or corner snaps the window to maintain the chosen ratio. A **minimum window size of 1920×1080** is enforced. The game remembers window position and size between sessions.

If not implemented: Windowed mode uses the same resolution dropdown as Fullscreen, with the same aspect ratio labels. Window is not user-resizable beyond the selected resolution.

**Advanced settings** (collapsed by default; visible at all times but expanded only when needed — no requirement to set Custom preset first):

*Upscaling* — mutually exclusive; only one can be active:

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Upscaling Mode | Dropdown | Off | Off / DLSS / FSR / XeSS |
| Upscaling Quality | Dropdown | Quality | Performance / Balanced / Quality / Ultra Quality — only shown when Upscaling Mode ≠ Off |
| DLSS Frame Generation | Toggle | Off | NVIDIA RTX 40-series only; only shown when DLSS selected |

DLSS requires an NVIDIA RTX GPU. FSR (AMD FidelityFX Super Resolution) works on a wider range of hardware. XeSS is Intel's equivalent. All three are integrated via Unreal plugins; availability is detected at runtime and unavailable options are greyed out.

*Rendering quality* — individual overrides; selecting any override sets Quality Preset to Custom:

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Anti-Aliasing | Dropdown | TAA | Off / FXAA / TAA / MSAA 2× / MSAA 4× / MSAA 8× — TAA required for DLSS |
| Shadow Quality | Dropdown | High | Off / Low / Medium / High / Ultra |
| Shadow Distance | Dropdown | High | Low / Medium / High / Ultra |
| Ambient Occlusion | Toggle | On | Screen-space AO; performance cost on lower-end hardware |
| Global Illumination | Dropdown | Lumen | Off / Baked / Lumen — Lumen is Unreal 5 dynamic GI; high hardware cost |
| Reflections | Dropdown | Lumen | Off / Screen Space / Lumen |
| Texture Quality | Dropdown | High | Low / Medium / High / Ultra |
| Effects Quality | Dropdown | High | Particle and magical effect fidelity — Low / Medium / High / Ultra |
| View Distance | Slider | 75% | |

*Visual effects* — toggles for effects that are stylistic rather than technical; off by default for some:

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Motion Blur | Toggle | Off | Also synced with Accessibility setting |
| Depth of Field | Toggle | On | Bokeh blur on out-of-focus elements; personal preference varies |
| Bloom | Toggle | On | Light bleed on bright sources |
| Lens Flare | Toggle | On | |
| Film Grain | Toggle | Off | Subtle grain overlay; personal preference |
| Chromatic Aberration | Toggle | Off | Colour fringing effect; many players dislike this |
| Vignette | Toggle | Off | Screen-edge darkening |

---

### 2. Audio

MGA routes audio through **two separate output channels**. This allows streamers to capture game audio and music independently in OBS or similar software, dropping the music channel to avoid licensing issues without losing game sound.

| Channel | Contains |
|---------|----------|
| **Game Audio** | SFX, voice, ambient, UI sounds |
| **Music** | All music only |

Both channels default to the system's primary output device. Players with virtual audio routing (e.g. VB-Cable) can route the Music channel to a separate device for independent OBS capture.

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Game Audio Output | Dropdown | Primary Device | Lists all available output devices |
| Music Output | Dropdown | Same as Game Audio | Can be set to a separate device |
| Master Volume | Slider | 80% | Scales both channels |
| Music Volume | Slider | 70% | Scales Music channel only |
| SFX Volume | Slider | 80% | |
| Voice Volume | Slider | 85% | |
| Ambient Volume | Slider | 65% | |
| UI Volume | Slider | 60% | |
| Subtitles | Toggle | On | |
| Subtitle Size | Dropdown | Medium | Small / Medium / Large |
| Mono Audio | Toggle | Off | Mixes all audio to mono |

---

### 3. Controls

Two sub-tabs: **Keyboard & Mouse** and **Controller**. Both are fully rebindable.

#### Keyboard & Mouse

| Setting | Type | Default |
|---------|------|---------|
| Full key binding table | Keybind | [Defaults TBD] |
| Mouse Sensitivity (Horizontal) | Slider | 50% |
| Mouse Sensitivity (Vertical) | Slider | 50% |
| Invert Mouse X | Toggle | Off |
| Invert Mouse Y | Toggle | Off |
| Mouse Smoothing | Toggle | Off |

#### Controller

| Setting | Type | Default |
|---------|------|---------|
| Full button binding table | Keybind | [Defaults TBD] |
| Stick Sensitivity (Horizontal) | Slider | 50% |
| Stick Sensitivity (Vertical) | Slider | 50% |
| Invert Stick X | Toggle | Off |
| Invert Stick Y | Toggle | Off |
| Inner Dead Zone | Slider | 15% | Below this threshold, input ignored |
| Outer Dead Zone | Slider | 95% | Above this threshold, treated as max input |
| Trigger Sensitivity | Slider | 50% |
| Rumble / Vibration | Toggle | On |
| Aim Assist | Toggle | On |

---

### 4. Camera

Controls the CAMERA drone orbit behavior around Meghan. The drone is the physical third-person camera object in the game world; these settings govern how it responds to player input (see [UI/UX](../gdd/ui-ux.md) for the drone system design).

Separate values are stored for KB+M and controller input.

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Horizontal Sensitivity (KB+M) | Slider | 50% | |
| Vertical Sensitivity (KB+M) | Slider | 50% | |
| Invert Horizontal (KB+M) | Toggle | Off | |
| Invert Vertical (KB+M) | Toggle | Off | |
| Horizontal Sensitivity (Controller) | Slider | 50% | |
| Vertical Sensitivity (Controller) | Slider | 50% | |
| Invert Horizontal (Controller) | Toggle | Off | |
| Invert Vertical (Controller) | Toggle | Off | |
| Camera Distance | Slider | 50% | Controls drone spring arm length |
| Camera Height | Slider | 50% | Drone hover height relative to Meghan |
| Auto-Center | Toggle | On | Camera swings behind Meghan when moving |
| Auto-Center Delay | Slider | Medium | How quickly auto-center triggers; Short / Medium / Long |
| Camera Lag | Slider | 25% | 0% = snappy; 100% = floaty; preference varies; some players experience motion sickness at extremes |
| Camera Collision | Toggle | On | Drone pulls toward Meghan when geometry is in the way |

---

### 5. Gameplay

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Difficulty | Dropdown | Normal | Easy / Normal / Hard / Custom (TBD) |
| Autosave | Dropdown | 10 min | Off / 5 / 10 / 15 / 20 / 30 min |
| Pause in In-Game Menu | Toggle | Off | When on, world time pauses while any in-game menu is open (both phone and out-of-character); default off — the world continues normally and the game's pacing is designed around this |
| Tutorial Hints | Toggle | On | |
| HUD Opacity | Slider | 100% | Scales all widget opacity |
| Notifications | Toggle | On | Skill level-up log, mission updates, etc. |
| Notification Duration | Slider | Medium | How long non-critical notifications stay on screen |

---

### 6. Content

An age acknowledgement is required on first access. Once acknowledged, individual categories can be toggled freely.

Content settings are **per-playthrough**, not global. Changing content settings here updates the active playthrough's configuration. See [Save System](../gdd/save-system.md) for playthrough folder design.

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Age Acknowledgement | One-time confirmation | — | Required before any non-Base content can be enabled |
| Cursed Uniform System | Toggle | Off | Enables the cursed route mechanics and associated content |
| Inhibition / Embarrassment Mechanics | Toggle | Off | Enables inhibition buildup and embarrassment states |
| Explicit DLC Content | Toggle | Off | Greyed out until relevant DLC is installed |
| Combat Intensity | Dropdown | Standard | Standard / Reduced (reduces visual gore, if any) |
| Streamer Mode | Toggle | Off | Applies Base-tier preset to active playthrough; overrides individual toggles above |
| Streaming Platform | Dropdown | — | Visible when Streamer Mode is On; selects platform content preset (Twitch / YouTube / Other) |

---

### 7. Accessibility

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Colorblind Mode | Dropdown | Off | Off / Deuteranopia / Protanopia / Tritanopia |
| Text Size | Dropdown | Medium | Small / Medium / Large |
| High Contrast UI | Toggle | Off | Increases UI element contrast |
| Motion Blur | Toggle | Off | Also in Display; synced |
| Screen Shake | Toggle | On | Can be disabled for motion sensitivity |
| Camera Shake | Toggle | On | Separate from screen shake |
| Photosensitivity Mode | Toggle | Off | Reduces flashing and rapid light changes |

---

### 8. Language

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Game Language | Dropdown | System default | Populated from installed language packs |
| Subtitle Language | Dropdown | Same as Game | Can differ from game language |

---

## Open Items

- Full default key and button binding tables
- Difficulty system design (what does Custom difficulty expose?)
- Whether camera lag and auto-center settings should also be exposed per Meghan mode (civilian vs. Ava)
- Whether any settings require a restart to apply (resolution changes, language changes)
- Settings file location on disk — recommend alongside save data in Documents/MGA/
- Whether settings are global or per-playthrough (most should be global; content and streamer settings are per-playthrough)
- Music output device routing implementation — Unreal audio submix architecture; may require platform-specific audio API work on Windows
