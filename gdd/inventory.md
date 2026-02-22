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

**Quick-store actions** can be mapped to the action bar to collapse this into a single input. An action might read: *"Store right hand item → jacket pocket"* or *"Store left hand item → backpack."* These are user-defined mappings and are a power-user feature. Most players will pick something up, open inventory, and drag it into place — the quick-store actions are for players who want to optimise the workflow.

---

## Weight Limit and Stamina

Total carried weight is the sum of all items in all active storage containers. This is checked against a weight limit derived from Meghan's **Strength (STR)** attribute — higher STR allows a higher weight limit before penalty applies.

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

The order is "first that fits" — not optimal packing, but fast and predictable. Players who want precise control use the per-storage view.

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

---

## Phone Independence

The inventory UI is accessible without Meghan's phone. Meghan knows what is in her pockets without a digital interface — this is physical knowledge, not digital. The inventory screen joins save, load, settings, and exit as options available regardless of phone status.

This is relevant during pipeline capture sequences and other scenarios where the phone may be unavailable. Knowing what you are carrying in those moments is often exactly the information you need.

---

## Open Items

- Container list for all clothing items — dimensions, volume, and weight capacity per storage slot
- Item database — dimensions, volume, and weight for all carriable items
- STR-to-weight-limit formula — exact scaling
- SP drain rate per unit of overweight — exact values for calibration
- Auto-storage iteration order — priority of containers when multiple could accept the same item
- Whether the combined view shows which container an item is in (tooltip or label) or hides it entirely
- Quick-store action bar binding UI — how the player defines and edits these mappings
- Whether items in hand slots are visible on Meghan's model in third-person view
- Item damage from clothing damage — left open; revisit in a future design pass
