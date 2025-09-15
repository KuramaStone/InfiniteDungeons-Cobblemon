# Loot Pools (loot.yml)

Loot Pools define sets of items that can drop from dungeons. Each pool has a weight, a number of pulls, and a list of items with their own weights and item counts.

| Key               | Default | Description                                                                  |
|-------------------|---------|------------------------------------------------------------------------------|
| `pool weight`     | —       | Determines how likely this pool is selected relative to other pools.         |
| `pulls`           | —       | Range `[min, max]` indicating how many items to randomly pull from the pool. |
| `pool`            | —       | List of individual loot entries (shown below).                               |

---

## Loot Entry Properties

| Property       | Type        | Description                                                |
|----------------|-------------|------------------------------------------------------------|
| `item`         | string      | The item to give (Minecraft item ID). Uses `/give` format  |
| `amount`       | list        | Range `[min, max]` for the quantity of this item.          |
| `weight`       | float       | Relative chance of this item being selected during a pull. |

---

## Example: Common Loot Pool

```yaml
Loot Pools:
  common:
    pool weight: 9
    pulls: [8, 16]
    pool:
      - item: "nether_wart"
        amount: [1, 6]
        weight: 6
      - item: "gold_ingot"
        amount: [3, 8]
        weight: 3
      - item: "iron_sword[custom_name='[\"\",{\"text\":\"Brook's Favorite Sword\",\"italic\":false}]']"
        amount: [1, 1]
        weight: 1
```