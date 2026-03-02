# Traversal System

## Overview

Traversal in MGA is gameplay, not a loading screen connector. Moving through Terridyn is active, skill-driven, and analog — the same skills used to navigate a rooftop shortcut during a courier run are the skills available during a chase sequence or a combat retreat. The Razor holds: there is no separate traversal progression. Parkour, Athletics, Running, Driving, Stealth, and Evasion each contribute to how Meghan moves through the world, and each improves through use regardless of the context that demanded it.

Movement is continuous and analog. Stick deflection maps directly to speed — a slight tilt produces a slow walk, moderate tilt a walk, full tilt a run. Sprint is a held modifier on top of full input. There is no binary walk/run toggle. Keyboard and mouse players have equivalent control through a configurable input model.

---

## Technology

Three systems underpin traversal:

- **Motion Matching (UE)** — procedural animation blending for smooth transitions between gaits, postures, and traversal modes. Covers locomotion, climbing, parkour, and context-sensitive hand/foot placement
- **General Movement Component (GMC) plugin** — enhanced movement controller replacing the default UE character movement component. Provides the runtime logic for all traversal states
- **Analog input model** — stick deflection determines speed on a continuous curve. Dead zones and response curves are configurable per the controller settings in the [settings menu](../creative-briefs/CB-settings-menu.md)

---

## Locomotion Modes

### Walk / Run / Sprint

The default movement modes. Terrain type and gradient affect speed; stamina cost scales with intensity. Motion Matching blends between gaits seamlessly as the player modulates input. Running and Athletics govern base speed and endurance.

### Climb

Surface-dependent traversal. Athletics governs raw strength climbing — sheer walls, ship hulls, vertical surfaces that demand grip and endurance. Parkour governs technical urban climbing — ledges, pipes, vaults, mantles, and chained sequences across irregular geometry. Stamina drains while climbing; fall risk increases at low skill.

### Jump and Swing

Gap crossing and elevation changes. Single jumps are available to all; chained jump sequences, wall kicks, and swing-point traversal require Parkour investment. Swing points are placed in the environment where the geometry supports them. Stamina cost per action.

### Crawl

Two modes: hands-and-knees (faster, higher profile) and belly crawl (slower, lower profile, soldier crawl). Stealth governs noise output during crawling; Athletics governs sustained crawl speed. Used for vent navigation, under-obstacle traversal, and tight spaces.

### Swim

Athletics-driven water traversal. Calm water is accessible at low skill; rough water and underwater traversal require investment. Stamina drain while swimming; drowning risk at zero stamina.

### Vehicle

Bicycle, motorbike, and hoverbike progression. Driving skill governs handling, speed, and available vehicle tiers. Vehicle traversal integrates directly with the courier job — extensive delivery work builds the Driving skill that later enables combat-speed vehicle use.

---

## Content Tier Variants

At **Spicy** tier, when Meghan's inhibition is high and she is in a state of indecency, locomotion variants change. She attempts to cover herself while moving — this affects speed and animation but does not lock out any traversal mode. As inhibition decreases, modesty-covering animations relax and eventually disappear.

At **Super Spicy** tier, restraints limit which traversal modes are available. The governing principle: restraints restrict movement proportional to what they bind. Hands bound locks climbing and swinging. Feet bound locks running and jumping. Both restricts traversal to a minimal shuffle. Specific restraint-to-traversal mappings are deferred to the restraint feature brief. Cross-reference: [Escape Artist](../../mgaPriv/mechanics/skills/escape-artist.md), [Binding](../../mgaPriv/mechanics/skills/binding.md).

---

## Skill Integration

Traversal is governed entirely by the existing skill system. There is no separate traversal progression, traversal XP, or traversal unlock tree. The skills that matter:

| Skill | Traversal role |
|-------|---------------|
| **Parkour** | Urban acrobatics, technical climbing, chained movement, wall traversal |
| **Running** | Base run/sprint speed, endurance at speed |
| **Athletics** | Strength climbing, swimming, sustained physical traversal, crawl speed |
| **Driving** | Vehicle operation, handling, speed |
| **Stealth** | Noise reduction during movement, silent crawling, crouched traversal |
| **Evasion** | Reactive movement during combat, dodge integration with traversal state |

A courier who has spent weeks running deliveries across the city arrives at a chase sequence with the Running and Parkour investment to handle it. A hacker who has spent those weeks indoors does not — but she has other tools.

---

## Cross-References

- [Creative Brief — Traversal System](../creative-briefs/CB-traversal.md) — aspirational design exploration
- [Core Loop](core-loop.md) — how traversal connects to jobs, time, and daily rhythm
- [Combat System](../creative-briefs/CB-combat.md) — traversal during combat encounters
- [Difficulty Framework](difficulty.md) — difficulty does not affect traversal; it is combat-only
- [Skills Overview](../../mgaPriv/mechanics/skills.md) — full skill list and progression model
