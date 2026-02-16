# Themes (config/infinitedungeons/themes/)

Themes are the block palette used by Roguelike Dungeons during its generation. Each `dungeon-type` can have a selection 
of themes. 

Roguelike-dungeons provides these by default:
- DEFAULT
- TOWER
- OAK
- SPRUCE
- DARKOAK
- STONE
- CRUMBLEDSTONE
- MOSSY
- CRUMBLEDMOSSY
- SLATE
- TILEDSLATE
- NETHER
- REDNETHER
- WARPED
- BLACK

If you're feeling adventurous, you can create your own!

## Custom Themes
An example custom theme is provided in `config/infinitedungeons/themes/custom_ice.json`. 

There are two categories: `main` and `decorative`. 

`main` is used as the primary block set for "Main structural walls".

`decorative` is used as the secondary blockset for "Decorative feature walls".

More details can be found here: https://github.com/Greymerk/minecraft-roguelike/wiki/Themes

`floor`, `walls`, `pillar`, `lightblock`, `liquid`, `stair`, `slab`, and `door` can have a configurable number of blocks.
The weight is used to determine how likely something is to be selected. The total chance a block will be selected is `weight`/`sum of weights`.
```json
  "floor": [
    { "weight": 75, "blockstate": "minecraft:packed_ice" },
    { "weight": 25, "blockstate": "minecraft:blue_ice" },
  ]
```