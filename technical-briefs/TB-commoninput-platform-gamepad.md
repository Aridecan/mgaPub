# TB — CommonInput Per-Platform Gamepad Glyph Resolution

> **Status: reference note (2026-07-22).** Captured while wiring the WS4 controller glyphs on Windows.
> **Read this before bringing up Linux or Mac** — the controller-glyph setup that works on Windows will
> **silently show no gamepad glyphs** on the other platforms unless addressed. Engine facts are from the
> local UE 5.8 source (`d:/uegit`); treat API/line numbers as navigational.

## TL;DR

CommonUI's `UCommonInputPlatformSettings` is **per-platform**, and its `DefaultGamepadName` defaults to
the **platform's own name** (`"Windows"`, `"Linux"`, `"Mac"`). The glyph lookup
`FCommonInputPlatformBaseData::TryGetInputBrush` matches a controller-data asset's `GamepadName`
**EXACTLY, with no fallback**. So a `UCommonInputBaseControllerData` asset whose `GamepadName` is
`"Windows"` resolves **only** on Windows — on Linux/Mac the runtime reports `"Linux"`/`"Mac"`, finds no
match, and draws no glyph (CommonUI falls back to key-name text).

**MGA targets Windows (primary), Linux (secondary), Mac (aspirational)** — so this WILL bite.

## The mechanism (UE 5.8)

- **Per-platform settings.** Controller data is registered in `UCommonInputPlatformSettings`
  (`config = Game`), which is a `UPlatformSettings` — one instance **per platform**. In `DefaultGame.ini`
  each platform gets its own section, e.g. Windows is:
  ```ini
  [CommonInputPlatformSettings_Windows CommonInputPlatformSettings]
  DefaultInputType=MouseAndKeyboard
  DefaultGamepadName=Windows
  +ControllerData=/Game/MGA/UI/Glyphs/BP_CommonInputData_Generic.BP_CommonInputData_Generic_C
  ```
  Linux/Mac would be `[CommonInputPlatformSettings_Linux …]` / `[CommonInputPlatformSettings_Mac …]` —
  **separate sections with their own `+ControllerData` lists and their own `DefaultGamepadName`.**
- **Default gamepad name = platform name.** `UCommonInputPlatformSettings` sets
  `DefaultGamepadName = PlatformName` (`CommonInputBaseTypes.cpp` ~line 288), i.e. `"Windows"` on Windows.
  (The base class default is `FCommonInputDefaults::GamepadGeneric` = `"Generic"`, but the platform init
  overrides it to the platform name.) `CommonInputSubsystem::GetCurrentGamepadName()` returns this default
  unless a specific pad is identified via **`gamepadHardwareIdMapping`** on a controller-data asset.
- **Exact-match lookup, no fallback.** `FCommonInputPlatformBaseData::TryGetInputBrush`
  (`CommonInputBaseTypes.cpp` ~line 462) iterates the registered controller-data classes and returns a
  brush only when `DefaultControllerData->GamepadName == GamepadName`. There is **no** "fall back to Generic"
  path. Name mismatch → no brush.

## Current MGA state (Windows only)

- `BP_CommonInputData_Generic` (`/Game/MGA/UI/Glyphs/`): `InputType=Gamepad`, **`GamepadName=Windows`**,
  18 gamepad-FKey → Xbox brushes. Registered under `[CommonInputPlatformSettings_Windows …]`.
- `GamepadName` was set to `Windows` (not the nicer `Generic`) specifically to match the Windows platform
  default that CommonInput writes — the pragmatic single-platform fix. **This is the line that does not
  travel to Linux/Mac.**

## What to do when adding Linux / Mac

Pick one (recommendation first):

1. **Normalize `DefaultGamepadName` to `Generic` on every platform (recommended, Lyra convention).**
   Set `DefaultGamepadName=Generic` in each platform's `CommonInputPlatformSettings` section and set the
   catch-all asset's `GamepadName=Generic`. One `Generic` data asset then resolves on all three platforms.
   Cost: revisit the current Windows `Windows`→`Generic` change (asset + the Windows ini section) so the
   name is consistent everywhere. Cleanest long-term; do this at Linux bring-up (or sooner as tidy-up).

2. **Register per-platform with matching names.** Keep `DefaultGamepadName` = platform name and add a
   `+ControllerData` entry (and, if names differ, a matching-`GamepadName` asset) under each of the
   `_Windows` / `_Linux` / `_Mac` sections. More sections to maintain; no asset rename.

3. **Hardware-ID mapping (needed anyway for true per-pad glyphs).** Populate `gamepadHardwareIdMapping`
   on per-pad assets (Xbox / DualSense / Switch Pro) so CommonInput reports the *specific* pad name
   regardless of platform — then the glyph follows the physical controller, not the OS. This is the real
   answer for the PS5/SteamDeck/KBM sets already imported; it supersedes the platform-name matching for
   pads it recognizes, with option 1/2 as the fallback for unrecognized pads.

**Minimum for Linux/Mac parity with today's Windows behaviour:** option 1 (normalize to `Generic`) — one
asset, three platform sections each pointing at it with `DefaultGamepadName=Generic`.

## Verify (per platform)

Glyphs only render live; there is no static check. On each platform, connect a pad and confirm a
`CommonActionWidget` (e.g. a WS4 rebind row) draws the Xbox glyph rather than falling back to key text.
If it shows text, the reported `GetCurrentGamepadName()` didn't match any registered asset's `GamepadName`.

## Related
- [TB — Controls Rebind](TB-controls-rebind.md) — the WS4 rebind screen these glyphs serve.
- Build farm is one machine per platform (Win primary / Linux secondary / Mac last) — platform bring-up
  context in the `build-infrastructure` session note.
</content>
