# Creative Brief — Inventory System

---

## Overview

The player manages a physically modelled inventory where every item has real weight, dimensions, and a physical location — in a hand, in a pocket, in a bag, or in the world. There is no abstract inventory grid disconnected from the character's body. What the player carries is what Meghan is physically carrying, and the consequences of that are real.

---

## Use Cases

### UC1 — Picking Up Objects

**Actor:** The player
**Goal:** Acquire an object from the world into Meghan's hands
**Flow:** Approach a non-fixed object → interact → item goes to a hand

**Rules:**

- Items always go to hands first — never straight to storage
- Every object has a **hand cost** — a fractional value representing how much hand capacity it consumes. Total hand budget = 2.0 (two hands)
- Objects with hand cost ≤ 1.0 can be held in one hand. Multiple small objects can share a hand (e.g. five pens at 0.2 each = 1.0)
- Objects with hand cost > 1.0 and ≤ 2.0 require both hands
- Objects with hand cost > 2.0 require multiple carriers — NPC cooperation is needed, used as a puzzle mechanic (e.g. moving a mana purifier)
- **Tethered objects** are attached to something via string or chain. They can be picked up but restrict movement to the tether's range. Can overlap with any hand cost class
- Picking up an object is a physical action that costs SP. If SP = 0, the player cannot pick up objects
- Items can be **forcibly removed** from hands by enemies during combat or capture

**Success:** Item is in hand, player can use it, stow it, or throw it
**Failure:** Hands full (not enough hand budget), SP exhausted, or object is fixed/static

---

### UC2 — Stowing Items

**Actor:** The player
**Goal:** Move an item from hand into a body container (pocket, pouch, bag)
**Flow:** With item in hand → select container or use quick-store action → item transfers to container

**Rules:**

- Only items with hand cost ≤ 1.0 can be stored in body containers. Larger items must stay in hands or be put down
- Accessing a container requires **free hands** — the number varies by container type (a pants pocket needs 1 free hand; a fiddly backpack side pocket needs 2)
- **Container accessibility** depends on how the container is worn:
  - Pockets, belt pouches, courier bags: accessible while worn
  - Backpacks: NOT accessible while on back — must be taken off to hand (1 action) or put to ground (1 action)
- **Backpack weight lifecycle:** On back (0.75× weight modifier, not accessible) → in hand (1.0× weight, hand cost 1.0, accessible) → on ground (world container, no weight on character, accessible by anyone nearby)
- Container weight = own weight + contents weight, multiplied by carry modifier
- **No combat state.** All stowing happens in real time. The world does not pause. Stowing takes time and combat continues around the player
- **Pickpocketing** works on NPCs and on the player character. Accessing another character's container without discovery is a skill-based timed interaction — longer access increases discovery chance

**Stow actions (action bar capable):**

- Store left hand, first fit (system finds first valid container)
- Store right hand, first fit
- Store left hand into container X (player-specified target)
- Store right hand into container X
- Stow [item name] (item-named action — uses the item's reserved container)

**Success:** Item is in a body container, hands are free
**Failure:** No accessible container with space, not enough free hands, container destroyed

---

### UC3 — Retrieving Items

**Actor:** The player
**Goal:** Get a specific item from storage into hand, ready to use
**Flow:** Use hotkey or inventory screen → item moves from container to hand

**Rules:**

- Retrieval is the reverse of stowing but the player experience is different: the player thinks in terms of **named items** ("get phone"), not containers
- The reservation system links specific items to specific containers — the system knows where to look first
- If the reserved container is unavailable (destroyed, not worn), the system searches all containers

**Retrieve actions (action bar capable):**

- Retrieve [item name] — pulls from reserved container, falls back to search
- Retrieve from left hand / right hand (for moving between hands)

**Combo chains:**

Multiple object interaction steps can be chained into a single action bar entry. Chainable verbs are **object interactions only**:

- **Retrieve** — pull item from container to hand
- **Stow** — move item from hand to container
- **Activate** — turn on / arm / flip safety off
- **Deactivate** — turn off / safe
- **Throw** — release item from hand

Combat actions (attacks, abilities, aiming, firing) are NOT chainable. This prevents macro-style "I win" buttons while keeping object handling fluid.

Example chains:
- "Retrieve grenade → activate → throw (aimed)"
- "Retrieve pistol → deactivate safety"
- "Retrieve potion → drink → throw bottle at target"

Each step in a chain costs real time. Combat continues during the sequence.

**Release actions (sub-types of throw):**

| Action | Behaviour | Targeting |
|--------|-----------|-----------|
| **Drop** | Release at feet, velocity = 0 | None |
| **Throw (quick)** | Fixed velocity in facing direction | No aiming phase |
| **Throw (aimed)** | Enter targeting mode, select landing point | Range = f(STR, Throwing skill, object mass) — see [Throwing Feature Brief](FB-throwing.md) |

**Success:** Item is in hand, ready to use or chain into next action
**Failure:** Item not found in any container, not enough free hands, SP exhausted

---

### UC4 — Interacting with World Containers

**Actor:** The player
**Goal:** Access a container in the world (chest, dresser, locker, backpack on ground) and move items in or out
**Flow:** Approach container → unlock if needed → open → interact with contents

**Rules:**

- **Locked containers** require either the correct key in hand (auto-unlock) or lockpicks in hand (triggers lockpicking minigame — see [Lockpicking Creative Brief](CB-lockpicking.md))
- Opening a world container requires hands (varies by container type)
- Once open, the container's contents are visible and individually selectable
- The player can grab items directly to left hand or right hand without opening the full inventory screen
- Opening the **full inventory screen** shows a unified view: all worn body containers + both hand slots + all open/accessible world containers within reach
- Items can be dragged between **any** containers on the unified screen — world container to body container, body container to world container, world container to world container
- Moving an item between any containers requires at least one free hand (the item conceptually passes through a hand)
- **Proximity check:** World containers appear on the inventory screen only while in reach. Walking away removes them from the view
- Other characters (NPCs, enemies) can access world containers if they are close enough — a backpack on the ground is not secure

**Design principle:** Minimize screen transitions. The player should never need to close one UI and open another to move an item from point A to point B. One screen, all accessible containers, drag between them.

**Success:** Items moved where the player wants them, efficiently, on one screen
**Failure:** Container locked and no key/picks, not enough free hands, out of reach

---

### UC5 — Managing Carried Inventory

Covered by the tools established in UC2, UC3, and UC4. The player manages their carried inventory through:

- The **unified inventory screen** (drag and drop between all accessible containers)
- **Action bar shortcuts** (quick-store, quick-retrieve, item-named actions)
- **Reservations** (persistent item-to-container assignments for muscle-memory shortcuts)

No additional mechanics are needed. The management experience is the sum of the interaction tools.

---

### UC6 — Dealing with Overload

**Actor:** The player
**Goal:** Manage the consequences of carrying too much weight
**Flow:** Total carry weight exceeds STR × 2.5 kg threshold → SP drain begins → player makes trade-off decisions

**Rules:**

- Carry weight over the threshold causes **SP drain** proportional to how far over the limit the character is
- **SP = 0 consequences:**
  - Heavy items held in hands are **force-dropped** — the character physically cannot maintain grip
  - Light items (pen, phone, keys) remain in hand — trivial weight, no strength needed
  - The player **cannot pick up objects** — picking up is a physical action requiring SP
  - The player **cannot perform physical actions** — exhaustion is a real crisis state
- Items in worn containers are NOT force-dropped on SP = 0 — only hand-held items are at risk
- The forced drop threshold (what counts as "heavy" relative to STR) is a calibration question for the [Inventory Feature Brief](FB-inventory.md)

**Tactical implications:**
- Stowing valuable items in containers protects them from force-drop
- The backpack weight modifier (0.75× on back vs 1.0× in hand) matters — taking off a heavy pack to access it might push over the threshold
- SP management becomes critical when carrying heavy objects into dangerous areas

**Success:** Player manages weight within SP budget, keeps important items secure
**Failure:** SP hits 0, heavy items drop, player is temporarily unable to act physically

---

## Item Activation

Items have an **activation** property orthogonal to their hand cost:

- **Activatable in hand** — item must be held to activate (pull trigger, press button, drink potion)
- **Activatable in world** — item can be activated without picking it up (press a switch, tap a remote on a table)
- **Passive** — no activation, the item is just an object
- Items **cannot** be activated from inside a container — must be in hand or in the world

---

## Related Documents

- [Inventory Feature Brief](FB-inventory.md) *(forthcoming)* — storage resolution, UI layouts, weight formulas, forced drop calibration
- [Clothing Creative Brief](CB-clothing.md) — wardrobe, dressing/undressing, layering, clothing damage cascade, degradation, repair, pocket viability
- [Throwing Feature Brief](FB-throwing.md) *(forthcoming)* — aimed throw range calculation, targeting UI
- [Lockpicking Creative Brief](CB-lockpicking.md) *(forthcoming)* — lock minigame design
- [Inventory System (GDD)](../gdd/inventory.md) — current feature-level documentation (predates this creative brief)

---

## Open Items

- Hand cost values for common object categories — calibration pass needed
- Forced drop weight threshold relative to STR — feature brief detail
- Combo chain UI — how does the player author/edit chains on the action bar?
- Lockpicking minigame — separate creative brief
- Whether containers have a weight limit independent of volume (e.g. pocket can hold the volume but not the weight of a gold brick)
- Interaction prompts — how does the player know an object is pickupable vs fixed? Visual indicator? Contextual prompt?
- NPC cooperation for multi-carrier objects — how is this initiated? Command? Contextual?
- Tether break — can tethered objects be ripped free with enough force/STR?
