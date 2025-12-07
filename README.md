# Dungeon Minigame Mod

This mod adds a highly configurable experience that allows players to enter procedural dungeons from Roguelike Dungeons. These dungeons have customizable loot, boss pokemon, and teams. Players can enter dungeons in groups or solo. Dungeon difficulty is extremely flexible and offers formula support with variables.

[A more thorough guide to setting up the config files can be found here.](docs/tutorial.md)

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

| Command                           | Permission                            | Description                                                                                                             |
|-----------------------------------|---------------------------------------|-------------------------------------------------------------------------------------------------------------------------|
| `/infdungeon startsolo <dungeon>` | `infinitedungeons.commands.startsolo` | Starts a solo dungeon using the specified dungeon config.                                                               |
| `/infdungeon startteam <dungeon>` | `infinitedungeons.commands.startteam` | Starts a team dungeon in the specified dungeon config. All team members receive a chat + GUI prompt to join or decline. |
| `/infdungeon exit`                | `infinitedungeons.commands.exit`      | Exit the current dungeon. Does not end the dungeon if other teammates remain - the player must wait for them to finish. |

### Admin Commands

| Command                                              | Permission                                  | Description                                                                                            |
|------------------------------------------------------|---------------------------------------------|--------------------------------------------------------------------------------------------------------|
| `/infdungeon admin startsolo <dungeon> <player>`     | `infinitedungeons.commands.admin.startteam` | Instantly starts a solo dungeon for the given player (no prompt).                                      |
| `/infdungeon admin startteam <dungeon> <2+ players>` | `infinitedungeons.commands.admin.startteam` | Instantly starts a team dungeon for the given players (no prompt). Does **not** affect existing teams. |

### Team Commands

| Command                                           | Permission                               | Description                                                                                                    |
|---------------------------------------------------|------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `/infdungeon team create <teamName> [players...]` | `infinitedungeons.commands.teams.create` | Creates a new team with you as the owner. Optionally invite players during creation (if they have permission). |
| `/infdungeon team invite [player]`                | `infinitedungeons.commands.teams.invite` | Invite a player to your team.                                                                                  |
| `/infdungeon team delete`                         | `infinitedungeons.commands.teams.delete` | Deletes your team if you are the owner.                                                                        |
| `/infdungeon team leave`                          | `infinitedungeons.commands.teams.leave`  | Leave your team if you are not the owner.                                                                      |


---

## Configuration (config.yml)

### General Config Options (config.yml)
| Key                                             | Default | Description                                                                               |
|-------------------------------------------------|---------|-------------------------------------------------------------------------------------------|
| `max-players-per-team`                          | 10      | The max team size permitted. Separate from dungeon team sizes.                            |
| `spectator-mode.enabled`                        | True    | If enabled, players can spectate after death. If disabled, they are sent home.            |
| `spectator-mode.only-on-team`                   | True    | If enabled, players cannot freecam while spectating.                                      |
| `dungeon-commands`                              | -       | Control what commands survival players are permitted to use while inside a Dungeon world. |
| `dungeon-commands.does-whitelist-have-priority` | True    | If true, then whitelisted commands are ALWAYS permitted even if blacklisted.              |
| `dungeon-commands.whitelist.prefix`             | True    | Any command that starts with a phrase here will be whitelisted.                           |
| `dungeon-commands.whitelist.regex`              | True    | Any command matches a regex here will be whitelisted.                                     |
| `dungeon-commands.blacklist.prefix`             | True    | Any command that starts with a phrase here will be blacklisted.                           |
| `dungeon-commands.blacklist.regex`              | True    | Any command matches a regex here will be blacklisted.                                     |

### [Dungeon Types (dungeontypes.yml)](docs/dungeontypes.md)

### [Chest Loot Pools (loot.yml)](docs/loot.md)

# Made With
Made using API provided by RoguelikeDungeons-2.1.0 for 1.21.1-fabric. It is required to use Infinite Dungeons.
https://modrinth.com/mod/roguelikedungeons/versions?g=1.21.1
