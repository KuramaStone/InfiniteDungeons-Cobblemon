~~# Dungeon Types (dungeontypes.yml)

## Boss Properties

Each `DungeonType` defines a group of possible bosses that can spawn in a dungeon. You can use static values or formulas
to dynamically scale difficulty.~~

| Key                    | Default | Description                                                                                                                   |
|------------------------|---------|-------------------------------------------------------------------------------------------------------------------------------|
| `boss.base-properties` | —       | Properties applied to **every entry** in this type. For example, `lvl=25` will apply to all spawned bosses unless overridden. |
| `boss.spawn-list`      | —       | A list of spawnable boss Pokemon with optional weights and additional spawn rules.                                            |

## Random Spawn Properties

Each `DungeonType` defined a group of Random Pokemon that can spawn. You can use static values or formulas to
dynamically scale difficulty.

| Key                             | Default | Description                                                                                                                   |
|---------------------------------|---------|-------------------------------------------------------------------------------------------------------------------------------|
| `random.base-properties`        | —       | Properties applied to **every entry** in this type. For example, `lvl=25` will apply to all spawned bosses unless overridden. |
| `random.spawn-list`             | —       | A list of spawnable boss Pokemon with optional weights and additional spawn rules.                                            |
| `preferred-rooms`               | list    | Optional list of dungeon rooms where this Pokemon will spawn. Unlike the boss, it will only spawn in a valid room.            |                                           |
| `random.pokemon-per-room`       | —       | How many Pokemon to spawn per room. These Pokemon temporarily despawn after a Player leaves that room.                        |                                           |
| `random.spawn-count-calculator` | —       | Determine how many Pokemon will be able to spawn per room.                                                                    |                           |

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

## Example: Basic Type

```yaml
Types:
  example:
    base-properties: "lvl=25"
    spawn-list:
      - properties: "pikachu"
        weight: 100.0
```

---

## Example: Dynamic Advanced Type

```yaml
Types:
  advanced:
    base-properties: "lvl=${min(100, {baseLevel}+{avg_times_completed}*5)} aspect=dungeon-pokemon aggression-bias=-${{max_times_completed}/5.0}"
    spawn-list:
      - properties: "mewtwo"
        weight: 99.9
        preferred-rooms: [ "ender", "" ]
      - properties: "mewtwo shiny=true"
        weight: 0.1
```