# Dungeons (dungeons.yml)

Each `Dungeon` is functionally a gamemode that players can complete. It defines how many lives they get, max players inside, and which dungeon types can appear.

### Dungeons Configuration (dungeons.yml)

These settings control boss spawns, difficulty, team requirements, and optional startup commands.

| Key                             | Default | Description                                                                                                     |
|---------------------------------|---------|-----------------------------------------------------------------------------------------------------------------|
| `display-name`                  | 3       | Pretty name using MiniMessage formatting for player views.                                                      |
| `max-lives-per-player`          | 3       | The number of lives each player receives in this dungeon.                                                       |
| `max-players-per-team`          | 5       | The maximum team size allowed in this dungeon. Teams larger than this are prompted to select members.           |
| `type-list`                     | -       | The list of possible DungeonTypes that may spawn. Each type can have an optional weight for randomness.         |
| `variables`                     | -       | Optional key/value pairs used in dungeon formulas or calculations.                                              |
| `commands.on-start`             | -       | Optionally run commands to run when the dungeon starts. Commands containing `{player}` are run for each player. |
| `introductions-per-room.<room>` | -       | Optionally set a title message introduction for each room type.                                                 |

### Example
```yaml
Dungeons:

  # Example dungeon: EXAMPLE... duh
  # This is the id of this dungeon
  example:
    # The display name for this dungeon config
    display-name: "<red>Example</red>"
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
    # Optional section. The commands to run on startup. Commands containing a {player} will be run for every player, but otherwise they run once.
    commands:
      on-start:
        - "tellraw {player} {\"text\":\"Welcome to the Dungeon!\"}"
    # Optional section. These will be given when entering each room. Only enter rooms you'd like to have an intro. See the config.yml's "starting-room-priority" for a list of room names.
    introductions-per-room:
      kitchen:
        title: ""
        subtitle: "<gold>Hungry?"
        fadeInTicks: 10
        stayTicks: 20
        fadeOutTicks: 10
```