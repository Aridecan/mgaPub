# MGA — Platforms & Tech

---

## Platform Targets

| Platform | Priority | Status |
|----------|----------|--------|
| **Windows** | Primary | Confirmed |
| **Linux** | Secondary | Confirmed |
| **Mac** | Tertiary | Aspirational |

PC is the only target platform. Console is not planned for the initial release. The Linux build is a native build, not a compatibility layer.

---

## Engine

**Unreal Engine** — version to be confirmed at production start. The game is built to the engine version current at that time; no version is pinned in this document.

Engine features are selected to match the art direction: the visual style uses a custom 4-tone cel shader with an inverse hull outline solution and no reliance on post-processing. Unreal features that conflict with or are superseded by the custom rendering pipeline (path-traced Lumen, screen-space effects) are not used or are disabled. Features that complement it — geometry processing, animation, physics — are used where appropriate.

*See [Art Direction](art-direction.md) for full rendering detail.*

---

## Minimum Specifications

These are the minimum specifications the game must run on at acceptable performance. The release window is **5–8 years** from the time of writing. At that point, hardware at this tier will be on the older and more common end of the installed base — these are not aspirational specs, they are the floor.

| Component | Minimum |
|-----------|---------|
| **OS** | Windows 10 64-bit (Windows 11 preferred) |
| **CPU** | Intel Core i5 13th generation or equivalent; multi-threaded — the game is designed for multi-threaded CPU execution |
| **RAM** | 16 GB |
| **GPU** | NVIDIA RTX 5060 or equivalent |
| **VRAM** | 8 GB |
| **Storage** | TBD |
| **DirectX** | TBD |

Equivalent CPUs from AMD and Intel Arc GPU equivalents are supported. Multi-threading is not optional on the CPU side — the game's systems are designed around it and will use it whether or not the hardware exposes it cleanly.

---

## Build Pipeline

Official builds are produced by Jenkins on a dedicated build machine, not from a local development environment. This is a commitment made in the manifesto and is non-negotiable: every release build is reproducible and auditable.

*See [Manifesto — Automation Is Part of the Product](manifesto.md#9-automation-is-part-of-the-product)*

The build pipeline handles:
- Compilation and packaging for each target platform
- DLC package generation (Base, Spicy, Super Spicy, localisation packages)
- Region override packaging
- Mod tool builds where applicable

*See [Content Architecture & DLC](content-and-dlc.md) for the full DLC stack.*

---

## Distribution

**Primary:** Steam. The base game is designed to meet Steam's content policies. Spicy and Super Spicy DLC may require distribution through alternative channels depending on platform policy at time of release.

**Other platforms:** TBD. No additional storefronts are confirmed at this stage.

---

## Open Items

- Recommended specifications (above minimum; to be determined during development)
- Storage requirements (depends on final asset counts and compression)
- DirectX version target
- Linux distribution — specific distros and kernel versions to be confirmed
- Mac target hardware (Apple Silicon — M-series; Intel Mac support not planned)
- Controller support — keyboard/mouse confirmed; controller layout TBD
- Whether Nanite is used for geometry, or whether the cel-shader pipeline supersedes it
- Steam Deck compatibility target (optional but worth evaluating given Linux build)
