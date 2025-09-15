# Dungeon Minigame Mod

This mod adds a configurable dungeon-based minigame to Minecraft. Players form teams, enter temporary dungeon instances, fight through challenges, and face a customizable boss Pokémon. The system is built around flexible configuration and strong safety controls for multiplayer servers.

---

# Features

## Entering Dungeons
- Players can enter a dungeon via command or via gui.
- Teams enter dungeons together. Teams may be a single player or multiple players.
- Before entry, each team member receives a confirmation menu/message with a 15s timer. Only confirmed players are sent into the dungeon.
- Players can only access dungeons the teamleader has permission to. Team owners can bring teammates without that permission. Permissions can be granted via `infinitedungeons.dungeons.<dungeon config id>`

## Minigame Details
- Each dungeon instance is generated in a temporary dimension using Roguelike Dungeons.
- A configurable Boss Pokémon spawns in the lowest valid preferred room.
- Chunks outside of the dungeon do not generate.
- Players spawn in the **Jukebox Room**, or in a random room if unavailable.
- Loot is **per-player**: each chest shows unique contents for each participant.
- Players have limited lives:
    - On death, they keep their inventory and respawn at the starting room.
    - When out of lives, players switch to spectator mode within the dungeon.
    - Spectators follow their closest living teammate if possible.
- Once all players run out of lives, the dungeon ends and the temporary world is deleted.
- End conditions:
    - Defeat the Boss Pokémon
    - All players lose their remaining lives

## Teams
- Teams are persistent across saves and have a configurable maximum size.
- Players can only be a member of one team at a time.
- Team ownership:
    - Owners can invite or kick members.
    - Owners must transfer ownership or disband the team before leaving.
- Invitations are required to join a team.
- Not every member must enter a dungeon. Those who decline are excluded for that run.

## Safety
- Dungeon dimensions are **temporary** and never saved to disk.
- Only confirmed team members can teleport into their dungeon instance.
- Non-members cannot teleport into a dungeon unless they are in creative or have operator permissions. A clear message explains the exception.
- Players automatically return to their original gamemode when leaving the dungeon world.

## Commands

### Dungeon Commands
| Command | Permission | Description |
|---------|------------|-------------|
| **/infdungeon startsolo \<dungeon\>** | `infinitedungeons.commands.startsolo` | Starts a solo dungeon using the specified dungeon config. |
| **/infdungeon startteam \<dungeon\>** | `infinitedungeons.commands.startteam` | Starts a team dungeon in the specified dungeon config. All team members receive a chat + GUI prompt to join or decline. |

### Admin Commands
| Command | Permission | Description |
|---------|------------|-------------|
| **/infdungeon admin startsolo \<dungeon\> \<player\>** | `infinitedungeons.commands.admin.startteam` | Instantly starts a solo dungeon for the given player (no prompt). |
| **/infdungeon admin startteam \<dungeon\> \<2+ players\>** | `infinitedungeons.commands.admin.startteam` | Instantly starts a team dungeon for the given players (no prompt). Does **not** affect existing teams. |

### Team Commands
| Command                                               | Permission                               | Description                                                                                                            |
|-------------------------------------------------------|------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| **/infdungeon team create \<teamName\> [players...]** | `infinitedungeons.commands.teams.create` | Creates a new team with you as the owner. You may optionally invite players during creation (if they have permission). |
| **/infdungeon team invite [player]**                  | `infinitedungeons.commands.teams.invite` | Invites a player to your team.                                                                                         |
| **/infdungeon team delete**                           | `infinitedungeons.commands.teams.delete` | Deletes your team if you are the owner.                                                                                |
| **/infdungeon team leave**                            | `infinitedungeons.commands.teams.leave`  | Leave your team if you are not the owner.                                                                              |

---

## Configuration

The dungeon system is fully configurable via the config files.

### General Config Options (config.yml)
| Key                           | Default | Description                                                                    |
|-------------------------------|---------|--------------------------------------------------------------------------------|
| `max-players-per-team`        | 10      | The max team size permitted. Separate from dungeon team sizes.                 |
| `spectator-mode.enabled`      | True    | If enabled, players can spectate after death. If disabled, they are sent home. |
| `spectator-mode.only-on-team` | True    | If enabled, players cannot freecam while spectating.                           |

### Dungeons Configuration (dungeons.yml)

These settings control boss spawns, difficulty, team requirements, and optional startup commands.

| Key                                  | Default | Description                                                                                              |
|--------------------------------------|---------|----------------------------------------------------------------------------------------------------------|
| `max-lives-per-player`               | 3       | The number of lives each player receives in this dungeon.                                                |
| `max-players-per-team`               | 5       | The maximum team size allowed in this dungeon. Teams larger than this are prompted to select members.    |
| `type-list`                          | —       | The list of possible DungeonTypes that may spawn. Each type can have an optional weight for randomness.  |
| `variables`                          | —       | Optional key/value pairs used in dungeon formulas or calculations.                                        |
| `commands.on-start`                  | —       | Commands to run when the dungeon starts. Use `{player}` to target individual players.                    |

#### Example

```yaml
Dungeons:
  example:
    max-lives-per-player: 3
    max-players-per-team: 5
    type-list:
      - type: "grassbosses"
        weight: 1
      - type: "waterbosses"
        weight: 1
      - type: "firebosses"
        weight: 1
    variables:
      baseLevel: 25
    commands:
      on-start:
        - "tellraw {player} {\"text\":\"Welcome to the Dungeon!\"}"
```

### Dungeon Types (dungeontypes.yml)

Each `DungeonType` defines a group of possible bosses that can spawn in a dungeon. You can use static values or formulas to dynamically scale difficulty.

| Key                  | Default | Description                                                                                                                   |
|----------------------|---------|-------------------------------------------------------------------------------------------------------------------------------|
| `base-properties`    | —       | Properties applied to **every entry** in this type. For example, `lvl=25` will apply to all spawned bosses unless overridden. |
| `spawn-list`         | —       | A list of spawnable bosses/Pokémon with optional weights and additional spawn rules.                                          |

#### Spawn List Properties

| Property           | Type        | Description                                                      |
|--------------------|--------------|------------------------------------------------------------------|
| `properties`       | string      | The Pokémon/build properties, similar to `/pokegive`.            |
| `weight`           | float       | Higher weight = more likely to spawn.                            |
| `preferred-rooms`  | list        | Optional list of dungeon rooms where this boss prefers to spawn. |

---

#### Example: Basic Type

```yaml
Types:
  example:
    base-properties: "lvl=25"
    spawn-list:
      - properties: "pikachu"
        weight: 100.0
```

### Loot Pools (loot.yml)

Loot Pools define sets of items that can drop from dungeons. Each pool has a weight, a number of pulls, and a list of items with their own weights and item counts.

| Key               | Default | Description                                                                  |
|-------------------|---------|------------------------------------------------------------------------------|
| `pool weight`     | —       | Determines how likely this pool is selected relative to other pools.         |
| `pulls`           | —       | Range `[min, max]` indicating how many items to randomly pull from the pool. |
| `pool`            | —       | List of individual loot entries (shown below).                               |

---

#### Loot Entry Properties

| Property       | Type        | Description                                                |
|----------------|-------------|------------------------------------------------------------|
| `item`         | string      | The item to give (Minecraft item ID). Uses `/give` format  |
| `amount`       | list        | Range `[min, max]` for the quantity of this item.          |
| `weight`       | float       | Relative chance of this item being selected during a pull. |

---

#### Example: Common Loot Pool

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