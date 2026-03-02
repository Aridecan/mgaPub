# MGA — Inventory System

---

## Overview

Meghan's inventory is physically modelled. Items are not stored in an abstract grid — they are stored in specific containers (pockets, bags, backpacks) attached to specific pieces of clothing or equipment. Each container has physical constraints: the maximum dimensions of an object that can fit inside it, and the volume and weight it can hold. If clothing is destroyed, everything stored in it drops to the ground.

This system exists because clothing is destructible. The inventory is not separate from the clothing system — it is part of it.

---

## Item Properties

Every carriable item has three physical properties:

| Property | Description |
|----------|-------------|
| **Dimensions** | X, Y, Z in consistent units — the bounding box of the item |
| **Volume** | Actual space the item occupies (≤ X×Y×Z; accounts for irregular shapes) |
| **Weight** | Mass of the item in consistent units |

---

## Storage Containers

Any piece of clothing or equipment that provides storage is a container. Each container has:

| Property | Description |
|----------|-------------|
| **Max X, Y, Z** | The largest item that can physically enter the space — a tall bottle won't fit a shallow pocket even if the volume is available |
| **Available volume** | Total volume minus volume of currently stored items |
| **Weight capacity** | Total weight the container can hold |
| **Current weight** | Sum of weights of currently stored items |

Examples of storage containers: pants pockets, jacket pockets, shirt breast pocket, backpack, courier bag, belt pouch. Each has different dimensional constraints and capacities reflecting its physical form.

---

## Storage Resolution

When an item is placed into a container, three checks run in order:

1. **Dimension check** — all three item dimensions must be ≤ the container's max X, Y, Z respectively
2. **Volume check** — item volume must be ≤ container's available volume
3. **Weight check** — item weight must be ≤ container's remaining weight capacity

All three must pass. If any fails, the container cannot accept the item.

---

## Hand Slots

Meghan has two hand slots — left and right. These are not storage; they are active hold positions.

When Meghan picks up an item from the ground, it goes to a hand slot. From there the player moves it to a storage container as a separate action. This is intentional: picking something up and stowing it are two distinct physical acts.

Items held in hand slots are visible on Meghan's model in third-person view wherever technically feasible. A phone in her right hand appears in her right hand. This is a best-effort commitment — highly irregular items may not have a modelled hold position, but standard items (phone, weapon, tool, consumable) should reflect their hand slot state visually.

---

## Quick Actions and Reservations

### Quick-Store and Quick-Retrieve

Quick actions are mappable to the action bar and collapse two-step inventory interactions into one input. Two types:

- **Quick-store:** *"Store right hand item → jacket pocket"* — takes the item currently held in the specified hand and places it in the target container
- **Quick-retrieve:** *"Retrieve phone"* — pulls a specified item from storage and places it in a hand slot

These are power-user features. Most players will pick something up, open inventory, and drag it into place. Quick actions are for players who want to optimise common workflows — a courier who picks up the same tool repeatedly, or a player who always wants a specific item in hand at the start of a fight.

### Reservations

A reservation links a specific item to a specific container slot — encoding the equivalent of muscle memory. For example: *phone → Jeans A, right front pocket.*

Once a reservation is set:

- **Quick-store** for that item always targets the reserved container first. If the reserved container can accept the item (dimensions, volume, weight all pass), it goes there. If not — because the container is destroyed, full, or Meghan is not wearing that piece of clothing — the action falls back to auto-storage or requires manual placement.
- **Quick-retrieve** for that item pulls from the reserved container first, then searches all other containers if not found there.

Reservations are advisory, not enforced. The system uses them as a first preference, not a guarantee. If the reserved container no longer exists — the jeans were destroyed, the backpack was dropped — the reservation is orphaned and the fallback applies.

**Setting a reservation:** In the per-storage view, the player assigns a reservation by right-clicking (or equivalent) an item and selecting a target container, or by dragging an item to a container slot and marking it as reserved. Reserved slots display a visual indicator (a pin or lock icon on the slot) so the player knows which locations are committed.

**Clearing a reservation:** Any reservation can be cleared or reassigned at any time through the same interface.

---

## Weight Limit and Stamina

Total carried weight is the sum of all items in all active storage containers. This is checked against a weight limit derived from Meghan's **Strength (STR)** attribute — higher STR allows a higher weight limit before penalty applies.

**Formula:** STR × 2.5 kg = comfortable carry weight threshold. STR 4 (fit woman) = 10 kg; STR 6 (fit man) = 15 kg; STR 10 (fit heavy worlder man) = 25 kg. The formula scales linearly with no further calibration needed.

Carrying weight above the limit does not hard-cap movement or action. Instead it causes **Stamina Pool (SP) drain** at a rate proportional to how far over the limit Meghan is. The further over, the faster SP depletes.

| Weight state | Effect |
|--------------|--------|
| At or below limit | No SP drain from weight |
| Moderately over | Slow additional SP drain during movement and activity |
| Significantly over | Faster SP drain; sustained overloading becomes costly in extended scenarios |

This creates a meaningful economic decision: carry more gear for more options, but pay for it in stamina — which affects combat performance, job effectiveness, and overall endurance.

---

## Clothing Destruction

When a piece of clothing is destroyed — HP reaching zero — all items stored in its containers are ejected and drop to the ground at Meghan's position. They become world-space objects that can be picked up.

The player is not warned before this happens. If the phone is in the pants pocket and the pants are destroyed, the phone drops. Awareness of what is stored where is the player's responsibility.

*Item damage from clothing damage (short of destruction) is not implemented in the current design. The option is left open for a future design pass if it proves valuable.*

---

## Auto-Storage

Auto-storage is only active in the combined inventory view. The algorithm is:

1. Iterate through all active storage containers in a defined order
2. For each container, run the three-check resolution (dimensions, volume, weight)
3. Place the item in the first container that passes all three checks
4. If no container can accept the item, the item remains in hand or on the ground

**Iteration order — first worn, first checked.** Containers are iterated in the order the clothing piece was equipped. If Meghan put her pants on before her jacket, the pants pockets are checked before the jacket pockets. This is predictable and player-legible: the player controls the order by controlling how they dress. The order is "first that fits within the first-worn priority" — not optimal packing, but fast and consistent. Players who want precise control use the per-storage view.

---

## UI — Per-Storage View

The per-storage view shows inventory by container. Each container is a horizontal row:

```
[ Jacket pocket    ] [ Phone ] [ Keys ] [ _______ ]
[ Backpack         ] [ Water bottle ] [ Toolkit ] [ First aid ] [ _______ ]
[ Pants pocket (L) ] [ _______ ]
[ Pants pocket (R) ] [ Coins ] [ _______ ]
```

The left label identifies the storage container. Items are shown to the right. Empty space within a container is visible.

**Interaction:** Click and drag to move items. When dragging an item, all containers that cannot accept it (failing any of the three checks) are greyed out. Only valid destinations remain full colour. Drop on a valid container to store; drop on a greyed container does nothing.

Items can be dragged between containers, from a hand slot into a container, or from a container back to a hand slot.

---

## UI — Combined Grid View

The combined grid view merges all stored items into a single display regardless of which container holds them. Physical location is not shown.

Adding an item in this view triggers auto-storage — the system finds the first valid container and places the item there without player direction. The player sees the item appear in the grid; the container assignment is handled automatically.

This view trades control for speed. Players who do not want to think about container assignment use this view. Players managing around destructible clothing — aware that certain items should be in certain containers — use the per-storage view.

**Tooltip:** Hovering over any item in the combined view shows which container it is currently stored in. This is the minimum information needed for players who care about clothing destruction risk — if a jacket is taking heavy damage, they can check what is in it without switching to the per-storage view.

---

## Phone Independence

The inventory UI is accessible without Meghan's phone. Meghan knows what is in her pockets without a digital interface — this is physical knowledge, not digital. The inventory screen joins save, load, settings, and exit as options available regardless of phone status.

This is relevant during pipeline capture sequences and other scenarios where the phone may be unavailable. Knowing what you are carrying in those moments is often exactly the information you need.

---

## Magical Girl Uniform and the Pocket Dimension

Uniform pieces are **spell constructs**, not physical garments. They exist in a dedicated magical inventory managed through a phone app, separate from the physical inventory system. Pieces have no weight, volume, or physical storage requirements — they manifest on the magical girl's body during transformation and return to spell-component state on untransformation.

The physical inventory system interacts with the uniform only through the pocket dimension and through any physical items (e.g. a phone) stored in pockets on manifested uniform pieces.

Full uniform piece design — piece types, stats, customisation, acquisition, and the skill bonus system — is documented in the magical girl systems (private repo).

### Pocket dimension behaviour

When Meghan transforms into Ava, her civilian clothing and everything stored in its containers enter a pocket dimension for the duration. The uniform pieces manifest from the magical inventory simultaneously. When she untransforms, the uniform pieces return to the magical inventory and her civilian clothes return from the pocket dimension.

**Items stored in the uniform's pockets follow the uniform.** If Meghan places her phone in a uniform pocket and then untransforms, the phone remains in the pocket dimension with the uniform — inaccessible until she transforms again. This is not a bug or an edge case; it is a deliberate consequence of the pocket dimension's scope. Players who store items in the uniform's pockets are accepting that those items are only accessible in transformed state.

### Uniform damage and mana

Uniform health reaching zero does not force untransformation. Only mana reaching zero causes untransformation. However, Meghan can deliberately sacrifice uniform health to recharge mana — trading structural integrity for continued transformation. This creates a last-resort option when mana is critically low: burn the uniform's remaining health to stay transformed long enough to complete the objective or reach a recovery point.

Items in the uniform's pockets are not at risk from uniform damage short of the player deliberately reducing the uniform to zero health and choosing to untransform.

### Notable items

The **burner phone** is a purchasable item — a basic handset with no widget capability, no skill profile, and no data. It is intended as expendable: carried as a decoy during infiltration, confiscated by captors while the real phone remains secure elsewhere. Exact item properties (dimensions, volume, weight) TBD in the item database pass.

---

## Open Items

- Container list for all clothing items — dimensions, volume, and weight capacity per storage slot
- Item database — dimensions, volume, and weight for all carriable items; includes burner phone
- ~~STR-to-weight-limit formula~~ — confirmed: STR × 2.5 kg; STR 4 (fit woman) = 10 kg, STR 6 (fit man) = 15 kg, STR 10 (fit HW man) = 25 kg
- SP drain rate per unit of overweight — exact values for calibration
- ~~Auto-storage iteration order~~ — confirmed: first clothing worn = first checked
- ~~Whether the combined view shows which container an item is in~~ — confirmed: tooltip on hover shows current container
- Quick-store action bar binding UI — how the player defines and edits these mappings
- ~~Whether items in hand slots are visible on Meghan's model in third-person view~~ — confirmed: yes, best-effort for all standard items
- Item damage from clothing damage — left open; revisit in a future design pass
- ~~Magical girl uniform piece locations around Terridyn~~ — resolved: uniform piece system documented in private repo (magical-girl-uniform-pieces.md); pieces are spell constructs in a dedicated magical inventory, not physical items in the physical inventory. Stash locations and acquisition detail are private repo
