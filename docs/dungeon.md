# Dungeons (dungeons.yml)

Each `Dungeon` is functionally a gamemode that players can complete. It defines how many lives they get, max players inside, and which dungeon types can appear.

### Dungeons Configuration (dungeons.yml)

These settings control boss spawns, difficulty, team requirements, and optional startup commands.

| Key                    | Default | Description                                                                                             |
|------------------------|---------|---------------------------------------------------------------------------------------------------------|
| `max-lives-per-player` | 3       | The number of lives each player receives in this dungeon.                                               |
| `max-players-per-team` | 5       | The maximum team size allowed in this dungeon. Teams larger than this are prompted to select members.   |
| `type-list`            | —       | The list of possible DungeonTypes that may spawn. Each type can have an optional weight for randomness. |
| `variables`            | —       | Optional key/value pairs used in dungeon formulas or calculations.                                      |
| `commands.on-start`    | —       | Commands to run when the dungeon starts. Commands containing `{player}` are run for each player.        |

### Example
```yaml
Dungeons:
  # This is the id of this dungeon
  example:
    # The number of lives given in this dungeon
    max-lives-per-player: 3
    # The max team size for the dungeon. If the user team is larger, they are asked to select which members to bring.
    max-players-per-team: 5

    # These are the possible DungeonTypes that could be selected. Completing any of them still counts as a completion for this specific dungeon.
    type-list:
      - type: "example"
        weight: 1
    # Variables here will be used when calculating the formulas. They are optional but may offer additional customization.
    variables:
      baseLevel: 25
    # The commands to run on startup. Commands containing a {player} will be run for every player.
    commands:
      on-start:
        - "tellraw {player} {\"text\":\"Welcome to the Dungeon!\"}"
```