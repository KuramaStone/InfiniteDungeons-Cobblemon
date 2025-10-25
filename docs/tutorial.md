# Infinite Dungeons Mod - Tutorial

## Overview

Infinite Dungeons is a highly configurable Minecraft mod that uses procedurally generated dungeons with customizable loot, bosses, and team mechanics. Players can enter dungeons solo or in groups, with each dungeon instance generated in a temporary dimension.

Dungeons are entirely generated through the use of [Roguelike Dungeons](https://github.com/Greymerk/minecraft-roguelike). This mod pastes the dungeon is a highly controllable way and manages the results.

---

## 1. Setting Up Dungeons

### dungeons.yml

Define dungeons as self-contained game modes.

Example configuration for a dungeon with nonsense for content:

```yaml
Dungeons:
  example:
    display-name: "<red>Example Dungeon</red>"
    max-lives-per-player: 3
    max-players-per-team: 5
    type-list:
      - type: "example"
        weight: 1
    variables:
      baseLevel: 25
    commands:
      on-start:
        - "tellraw {player} {\"text\":\"Welcome to the Dungeon!\"}"
    introductions-per-room:
      kitchen:
        title: ""
        subtitle: "<gold>Hungry?"
        fadeInTicks: 10
        stayTicks: 20
        fadeOutTicks: 10
```

**Key fields:**

* `display-name`: Name shown to players.
* `max-lives-per-player`: Lives per player.
* `max-players-per-team`: Maximum allowed team size.
* `type-list`: List of `DungeonTypes` with optional weights.
* `variables`: Optional formula variables.
* `commands.on-start`: Commands run at dungeon start.
* `introductions-per-room`: Optional per-room messages.

---

## 2. Defining Dungeon Types

DungeonTypes configure bosses and random spawns.

Most simple example `dungeontypes.yml`:

```yaml
  example:
    # These properties are applied at the end of every specific entry. For example, the first entry here would become "bulbasaur lvl=25" when spawning the boss.
    boss:
      # This example showcases a level that raises by 5 for each avg_times_completed AND causes higher aggression based on the max_times_completed divided by 5. baseLevel is a variable provided by the parent Dungeon config located in /dungeons.yml
      base-properties: "lvl=${min(100, {baseLevel}+{avg_times_completed}*5)} aspect=dungeon-pokemon aspect=fof-players-only aggression-bias=-${{max_times_completed}/5.0}"
      # This is the list of possible bosses that can spawn here. Use weights to control how likely they are to spawn
      spawn-list:
        - properties: "mewtwo"
          weight: 99.9
          # You can select certain rooms from RogueLike Dungeons that it will attempt to spawn in. names appearing first in the list are preferred over lower rooms.
          preferred-rooms: [ "ender" ]
        - properties: "mewtwo shiny=true"
          weight: 0.1
          preferred-rooms: [ "ender" ] # A boss's preferred room is optional, it will find a new location if the room isnt found.
    random:
      # This determines how many Pokemon can spawn in a room. There are a few types:
      #  This is an Area Based usage. It will spawn 1 Pokemon per 49 blocks in that room's area. So a room with an area of 100 will have 4 Spawns.
      #  Surface Area is used by default
      #   spawn-count-calculator:
      #     type: area
      #     blocks-per-pokemon: 49
      #     surface: true # Optional, this is set to true by default. To enforce using the whole area, set this to false.
      #     max: 8 # Optional, this is set to infinity by default. This is the max spawns permitted in the room.
      #  This is a fixed amount
      #   spawn-count-calculator:
      #     type: fixed
      #     amount: 1
      #  This calculates it as a formula. It uses variables from the dungeon config similar to Pokemon spawns.
      #  Additional variables for this formula:
      #  - {room_area}: The area of the room
      #   spawn-count-calculator:
      #     type: formula
      #     formula: "1+${}"
      spawn-count-calculator:
        type: area
        surface: true # this is set to true by default. to disable surface area, set this to false.
        max: 8
        blocks-per-pokemon: 49
      base-properties: "lvl=25 aspect=fof-players-only"
      # This is the list of possible random pokemon that can spawn. Use weights to control how likely they are to spawn based on the valid spawn conditions
      spawn-list:
        - properties: "clefairy"
          weight: 99.9
          preferred-rooms: [ "cistern" ] # A random's preferred room is mandatory, it won't spawn outside of it. You may use ["*", "!cistern"] to allow it to spawn everywhere except the cistern.
        - properties: "clefairy shiny=true"
          weight: 0.1
          preferred-rooms: [ "cistern" ]
```

**Slightly more advanced example with formulas:**

```yaml
  # This example uses formulas to make it grow increasingly difficult
  # You can write a formula such as ${1+1} to get 2. Text in this format will be calculated before building the pokemon
  # "lvl=${1+1}" turns into "lvl=2"
  # There are some variables built-in:
  # - {config_id}: The config_id of the current dungeon.
  # - {avg_times_completed}: The number of times this dungeon has been beaten on average for this DungeonTeam
  # - {max_times_completed}: The number of times this dungeon has been beaten by the highest value team member
  # - {team_size}: The size of the player team in the dungeon
  # These are provided by JavaScript. For a full list, look at the "Static Methods" section here: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math#static_methods
  # - abs(x): Returns the absolute value
  # - floor(x): Returns after dropping value to the lower whole number (if not already a whole number)
  # - round(x): Returns the closest whole number
  # - ceil(x): Returns after raising the value to the higher whole number (if not already a whole number)
  # - min(x, y): Returns the lower value between the two
  # - max(x, y): Returns the higher value between the two
  # - random(x, y): Returns a random decimal between 0.0 and 1.0
  # And these are ones I add to fill any gaps needed:
  # - randomInt(min, max): Return a random number from an inclusive-inclusive range. So randomInt(0, 5) can get 0, 1, 2, 3, 4, and 5
  advanced:
    pokemon-per-room: 4
    boss:
      # This example showcases a level that raises by 5 for each avg_times_completed AND causes higher aggression based on the max_times_completed divided by 5. baseLevel is a variable provided by the parent Dungeon config located in /dungeons.yml
      base-properties: "lvl=${min(100, {baseLevel}+{avg_times_completed}*5)} aspect=dungeon-pokemon aspect=fof-players-only aggression-bias=-${{max_times_completed}/5.0}"
      spawn-list:
        - properties: "mewtwo"
          weight: 99.9
          # You can select certain rooms from RogueLike Dungeons that it will attempt to spawn in
          preferred-rooms: [ "ender" ]
        - properties: "mewtwo shiny=true"
          weight: 0.1
          preferred-rooms: [ "ender" ]
    random:
      spawn-count-calculator:
        type: formula
        formula: "{room_area} / 9 + floor({team_size} / 2)"
      base-properties: "lvl=${randomInt(10, 20)} aspect=fof-players-only"
      spawn-list:
        - properties: "clefairy"
          weight: 99.9
          preferred-rooms: [ "cistern" ] # You can select certain rooms from RogueLike Dungeons that it will attempt to spawn in
        - properties: "clefairy shiny=true"
          weight: 0.1
          preferred-rooms: [ "cistern" ] # You can select certain rooms from RogueLike Dungeons that it will attempt to spawn in
```

**Notes:**

* `preferred-rooms` supports `*` for including all, and a prefix of `!` for excluding a room.
* Formulas can use variables: `{team_size}`, `{avg_times_completed}`, `{max_times_completed}`, `{config_id}`.
* Functions: `abs()`, `floor()`, `ceil()`, `round()`, `min()`, `max()`, `random()`, `randomInt(x,y)`.

---

## 3. Configuring Loot Pools

Loot is defined per player with pools in `loot.yml`.

Example:

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

**Key fields:**

* `pool weight`: Likelihood of pool selection.
* `pulls`: Number of items drawn per pool.
* `pool`: List of items with `item`, `amount`, and `weight`.

---

## 4. Commands

### Player Commands

* `/infdungeon startsolo <dungeon>`: Start solo.
* `/infdungeon startteam <dungeon>`: Start team dungeon.

### Admin Commands

* `/infdungeon admin startsolo <dungeon> <player>`
* `/infdungeon admin startteam <dungeon> <2+ players>`

### Team Commands

* `/infdungeon team create <teamName> [players...]`
* `/infdungeon team invite [player]`
* `/infdungeon team leave`
* `/infdungeon team delete`

---

## 5. Dungeon Rules & Safety

* Temporary dimensions: not saved to disk.
* Only confirmed team members can enter.
* Players return to original gamemode on exit.
* Spectator mode is available after death if enabled.
* Dungeons end when all players lose lives or the boss is defeated.

---

## 6. Configuration Options

The regex option for commands may be too advanced for some users. In that case, I recommend banning all commands and specifically allowing the ones you want as shown here.

Example from `config.yml`:

```yaml
config-version: 1

# The max team size for teams. If a team is larger than a dungeon allows, then the player will select who to bring.
max-players-per-team: 10

# The priority of each room. Every room is provided here as an additional aid for every room name.
# The starting room will always try to be connected to the main structure with a path towards the boss.
starting-room-priority:
  - MUSIC
  - BANQUET
  - BEDROOM
  - LIBRARY
  - CORRIDOR
  - STAIRWAY
  - CROSS
  - CRYPT
  - RESERVOIR
  - CISTERN
  - OSSUARY
  - KITCHEN
  - CREEPER
  - ENDER
  - ABYSS
  - PRISON
  - MUSIC
  - BREWING
  - PANOPTICON
  - SCULK
  - BTEAM
  - SMITH
  - PIT
  - BALCONY
  - IMPERIAL_STAIRWAY
  - ENTRANCE

# If set to true, players will spectate their dungeon after dying. Otherw
spectator-mode:
  enabled: true
  # If true, then the spectator will be locked to teammembers only.
  only-on-team: true

# These commands cannot be used from SURVIVAL PLAYERS inside a dungeon world.
# Note that these are the raw text a player uses, so use all versions of the command.
# WARNING: It is recommended that you change this commands as needed. These are examples to help you understand usage.
# By default, all commands are banned except a short list.
dungeon-commands:
  # If true, then the whitelist has priority over the blacklist. So if the blacklist bans it, but whitelist allows, then it is permitted still.
  does-whitelist-have-priority: true

  # These command are set to be permitted
  whitelist:
    # Any command starting with this prefix will ALWAYS be excluded. Dont forget to end on a space if desired!
    prefix:
      - "/infinitedungeons "
      - "/infinitedungeon "
      - "/infdungeons "
      - "/infdungeon "
      - "/dungeons "
      - "/dungeons "
      - "/spawn " # This catches any command that starts with this exact match
      - "/feed "
      - "/msg "
      - "/m "
      - "/r "
    # This allows for regex matching. This can do almost anything if you research: https://regex101.com/
    regex:
      - "/spawn" # This is an EXACT match for a player using "/spawn"

  # These commands are not permitted.
  blacklist:
    # Any command starting with this prefix will ALWAYS be excluded.
    # Use ".*" to ban all commands!
    prefix:
      - "/ban BrookEternal for being too cool for school" # Prevents banning BrookEternal for being too cool while inside a dungeon
    # This allows for regex matching. This can do almost anything if you research: https://regex101.com/
    regex:
      - ".*" # Disable pokeheal entirely
      - "/gamemode (?!survival).*" # Disable all gamemode commands unless it is also starts with survival.

```

---

## 7. Tips

* Use variables inside of `dungeons.yml` often. 
* Formulas can make dungeons progressively harder for repeated runs.
* Be careful when modifying the spawn count of random Pokemon as values under 1 pokemon per 40 blocks can become excessive.