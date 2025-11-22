~~# Dungeon Types (dungeontypes.yml)

# Win Conditions
Win conditions are ways to establish a win or loss for the dungeon.

## Boss Properties

Each Dungeon will have a boss even if there is no `defeat_boss` win condition.

Each `DungeonType` defines a group of possible bosses that can spawn in a dungeon. You can use static values or formulas
to dynamically scale difficulty.~~

| Key                    | Default | Description                                                                                                                   |
|------------------------|---------|-------------------------------------------------------------------------------------------------------------------------------|
| `boss.base-properties` | -       | Properties applied to **every entry** in this type. For example, `lvl=25` will apply to all spawned bosses unless overridden. |
| `boss.spawn-list`      | -       | A list of spawnable boss Pokemon with optional weights and additional spawn rules.                                            |

## Random Spawn Properties

Each `DungeonType` defined a group of Random Pokemon that can spawn. You can use static values or formulas to
dynamically scale difficulty.

| Key                             | Default | Description                                                                                                                   |
|---------------------------------|---------|-------------------------------------------------------------------------------------------------------------------------------|
| `random.base-properties`        | -       | Properties applied to **every entry** in this type. For example, `lvl=25` will apply to all spawned bosses unless overridden. |
| `random.spawn-list`             | -       | A list of spawnable boss Pokemon with optional weights and additional spawn rules.                                            |
| `preferred-rooms`               | list    | Optional list of dungeon rooms where this Pokemon will spawn. Unlike the boss, it will only spawn in a valid room.            |                                           |
| `random.pokemon-per-room`       | -       | How many Pokemon to spawn per room. These Pokemon temporarily despawn after a Player leaves that room.                        |                                           |
| `random.spawn-count-calculator` | -       | Determine how many Pokemon will be able to spawn per room.                                                                    |                           |

### Advanced `preferred-rooms`

`preferred-rooms` is actually a type of search engine for rooms. You can use room names, `*`, and `!` to modify the
search.
If you use `preferred-rooms: ["*"]`, then all rooms are possible - unless specifically excluded such as
`["*", "!cistern"]`

## Spawn List Properties

| Property          | Type   | Description                                                                                                            |
|-------------------|--------|------------------------------------------------------------------------------------------------------------------------|
| `properties`      | string | The Pokémon/build properties, similar to `/pokegive`.                                                                  |
| `weight`          | float  | Higher weight = more likely to spawn.                                                                                  |
| `preferred-rooms` | list   | Optional list of dungeon rooms where this boss prefers to spawn. If the room isnt found, it goes with the lowest room. |

# Advanced DungeonTypes with Formulas

Advanced DungeonTypes allow dynamic scaling using **formulas**, making dungeons increasingly difficult and customizable.

Formulas use `${...}` syntax and are calculated **after the dungeon has been defeated**.

---

## Built-in Variables

You can use these variables inside formulas:

| Variable                | Description                                                                 |
|-------------------------|-----------------------------------------------------------------------------|
| `{config_id}`           | The ID of the current dungeon.                                              |
| `{avg_times_completed}` | Average number of times this dungeon has been completed by the DungeonTeam. |
| `{max_times_completed}` | Highest number of completions by a single team member.                      |
| `{team_size}`           | Number of players in the team.                                              |

---

## Supported Functions

It uses JavaScript's `Math` class for
these. [You can find a full list here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math#static_methods).

Here are some common functions:

| Function          | Description                                        |
|-------------------|----------------------------------------------------|
| `abs(x)`          | Absolute value of `x`.                             |
| `floor(x)`        | Rounds `x` down to the nearest whole number.       |
| `round(x)`        | Rounds `x` to the nearest whole number.            |
| `ceil(x)`         | Rounds `x` up to the nearest whole number.         |
| `min(x, y)`       | Returns the smaller of `x` and `y`.                |
| `max(x, y)`       | Returns the larger of `x` and `y`.                 |
| `random()`        | Returns a random decimal between 0.0 and 1.0.      |
| `randomInt(x, y)` | Returns a random integer inclusive of the numbers. |

---

## Example: Dynamic Advanced Type

```yaml
advanced_example:
boss:
  # This example showcases a level that raises by 5 for each avg_times_completed AND causes higher aggression based on the max_times_completed divided by 5. baseLevel is a variable provided by the parent Dungeon config located in /dungeons.yml
  base-properties: lvl=${min(100, {baseLevel}+{avg_times_completed}*5)} fof-players-only aggression-bias=-${{max_times_completed}/5.0} aggression-rate=100.0 fof-damage=4.0
  spawn-list:
    # You can select certain rooms from RogueLike Dungeons that it will attempt to spawn in
    - properties: mewtwo
      weight: 99.9
      preferred-rooms:
        - ender
    - properties: mewtwo shiny=true
      weight: 0.1
      preferred-rooms:
        - ender
random:
  spawn-count-calculator:
    type: formula
    formula: '{room_area} / 9 + floor({team_size} / 2)'
  base-properties: lvl=${randomInt(10, 20)} fof-players-only aggression-bias=-5 aggression-rate=100.0 fof-damage=4.0
  spawn-list:
    # You can select certain rooms from RogueLike Dungeons that it will attempt to spawn in
    # You can select certain rooms from RogueLike Dungeons that it will attempt to spawn in
    - properties: clefairy
      weight: 99.9
      preferred-rooms:
        - cistern
    - properties: clefairy shiny=true
      weight: 0.1
      preferred-rooms:
        - cistern
# Some generic config values
options:
  allow-spawner-mob-spawners: true
  # Any and all gamerules should function here. The gamerules only apply to the dungeon level.
  gamerules:
    doMobSpawning: true
    doMobLoot: false
    mobGriefing: false
    keepInventory: true
# These are the loot pools that can generate in barrels and chests.
# You can define a default loot pool, but the default will be overriden if you specify.
loot:
  default:
    - pool: common
      weight: 99
    - pool: rare
      weight: 1
  kitchen:
    - pool: rare
      weight: 1
# These are the win conditions for ending a dungeon.
# Currently there are two types: `defeat_boss` and `defeat_n_pokemon`
win-conditions:
  - type: defeat_boss
    weight: 50
    max-seconds: 60
    action-bar-text: <gray>[<dark_red>Defeat <yellow><b>{dungeon_boss_name}</b><gray>]
    win-message:
      chat-message: <green>You have defeated {dungeon_boss_name}! Prepare to exit the dungeon in 30 seconds!
      action-bar: null
      title:
        subtitle: <gold>You defeated {dungeon_boss_name}!
        fadeInTicks: 10
        stayTicks: 20
        fadeOutTicks: 10
    timeout-message:
      chat-message: <red>You ran out of time! Prepare to exit the dungeon in 30 seconds!
      action-bar: null
      title:
        title: <red>You ran out of time!
        fadeInTicks: 10
        stayTicks: 20
        fadeOutTicks: 10
```