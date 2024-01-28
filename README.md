# Level border
This is a Fabric mod for Minecraft 1.20.4 that restricts your world borders based on your experience level. It's being developed with multiplayer in mind, which means each player will have their own spawn point.

## Planned initial features
### Spawn mechanics
Before a player spawns for the first time, a random coarse spawn point is chosen within a certain range from (0,0). 
The nearest tree within a smaller range is located and chosen as that player's actual spawn point.
If this fails, a new coarse point is looked for. Two players' spawn points cannot be close to each other. All of these parameters can be configured.
### Border expansion
Both the initial border size and how much the border expands with each level can be configured. Default values are a 2x2 border at level 0, increasing by one block per side for each level gained.
The formula to obtain the side length for a specific level would be `initial_size + 2·levelup_size·level`.
### Commands
This mod will support two main commands, both related to moving your spawn point, and both requiring confirmation as they have irreversible effects.  
 - `/lvborder relocate` allows you to repeat the initial spawn procedure. This will _entirely_ revert any blocks within your border and not within any other border to their initial state, as well as completely reset your player.
It is basically akin to starting from scratch entirely.
OPs can relocate other people, to coordinates of their choice, with `/lvborder relocate <nick> <X> <Y> <Z>`.
 - `/lvborder together join <nick>` allows you to request moving your spawn point to the exact same block as the player you're targeting, who then needs to accept or reject your request.
 - - `/lvborder together accept <nick>` accepts such a request.
 - - `/lvborder together reject <nick>` rejects such a request.
 - - `/lvborder together movein <nick>` actually moves your spawn point and can only be used after a request has been accepted. Like `/lvborder relocate`, this will reset your player, as well as any blocks within your borders not inside any others'.
### Multiplayer
When playing with other people, either a cooperative or a free-for-all mode can be set.
In cooperative mode, the level of players sharing the same spawn point contribute towards a virtual, shared "level" expanding a shared border between said players. How exactly this happens can be configured, and will be explained in the configuration section.
In free-for-all mode, this simply does not happen; each player has their own borders, regardless of whether they share spawn point with anyone else, and everything happens individually.
### Dungeons
Dungeons, in vanilla Minecraft, are an endless well of resources and experience. Having access to such a thing may, in some way, trivialize this mod's limitation; thus, there are several built-in behaviours, chosen via configuration.
 - Default: mobs spawned by a spawner within a player's borders do not award XP to said player when killed.
 - Vanilla: spawners, and mobs thereof, are unaffected by this mod.
 - Restricted: mobs spawned by spawners do not award XP when killed.
 - Draining: mobs spawned by spawners within one or mor players' borders drain a bit of experience from said players. When this happens, affected players will hear a sound coming from the direction of the spawner, possibly with visual cues. I don't know yet.
### Exploring
Brewing a [Mundane/Thick/Awkward TBD] potion with an Ender Pearl gives you the True Breathing effect, allowing you to finally be free and breathe air outside the border... If you can find a way out. And it only lasts 10 seconds.
### Death
> Dying is usually inconvenient.
> - [Susan Pompoms, El Goonish Shive](https://www.egscomics.com/comic/2010-07-26)

On death, your entire inventory and a portion of your XP are dropped to the ground, with the rest of your XP being lost.
Since this might be a bit too big an inconvenience, configuration options exist to both allow players to retain some of their experience, and keep part of their borders even if ending up at a lower level.   
### Endgame
While I haven't decided on a specific crafting recipe yet, a Nether Star and/or Netherite ingots can be used to craft a consumable item allowing players to choose a new spawn point, still resetting any block that ends up outside of any borders, while keeping all of their XP and inventory.
A Potion of True Breathing can be crafted (not brewed! Brewing has the standard vanilla effect) together with Dragon's Breath to greatly extend its duration.
### Configuration
Most aspects introduced or modified by this mod are configurable, either in the mod config or via the `/gamerule` command. Here's a list of settings:
#### Spawn
| Option          | Default value | Description                                                                                                                      |
|-----------------|---------------|----------------------------------------------------------------------------------------------------------------------------------|
| spawn_range     | `10000`       | The range around the world's spawn point for the initial, coarse spawn location.                                                 |
| tree_range      | `100`         | How far to look for trees near the initial spawn location. Note that high values may cause initial player spawns to take longer. |
| player_distance | `1000`        | The minimum allowed distance for two different players' natural spawn points. A value of 0 will disable any restrictions.        |
#### Border size
| Option       | Default value | Description                                                                                                                                          |
|--------------|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| initial_size | `2`           | Determines the border size for a player with 0 experience points.                                                                                    |
| levelup_size | `1`           | Determines how much the border size increases on level-up. This is probably best read as twice its actual value, as it is applied on all four sides. |
#### Multiplayer
| Option               | Default value | Description                                                                                                                                                    |
|----------------------|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| multiplayer_mode     | `coop`        | Controls whether players sharing a spawn point will contribute to shared borders (`coop`), or whether each player's level determines their own border (`ffa`). |
| multiplayer_xp_share | `lv_sum`      | In co-op mode, etermines how the shared level for shared borders is determined. In free-for-all mode, does nothing.                                            |

| multiplayer_xp_share value | Description                                                                                                                                           | 
|----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| `lv_sum`                   | The most forgiving option. The level of each player is summed to determine the border size.                                                           |
| `xp_sum`                   | Instead of adding each player's level, each player's XP is added instead. Due to vanilla level scaling, this results in considerably smaller borders. |
| `lv_avg`                   | The border size is determined by the arithmetic mean of the players' level.                                                                           |
| `xp_avg`                   | The border size is determined according to the level corresponding to the arithmetic mean of the players' experience points.                          |
| `lv_geo`                   | See `lv_avg`, except the geometric mean is used. This always results in smaller borders compared to `lv_avg`.                                         |
| `xp_geo`                   | Similarly, see `xp_avg`, except the geometric mean is used.                                                                                           |
| `max`                      | The border size is determined based on the level of the highest level player.                                                                         |
| `min`                      | The border size is determined based on the level of the _lowest_ level player.                                                                        |
#### Dungeons
| Option          | Default value            | Description                                                                                                                        |
|-----------------|--------------------------|------------------------------------------------------------------------------------------------------------------------------------|
| spawner_xp_mode | `default` (yeah, I know) | Determines mob spawner behaviour. See the Dungeons section for details. Values are `default`, `vanilla`, `restricted`, `draining`. |
#### Exploring
| Option                         | Default value | Description                                                                                                      |
|--------------------------------|---------------|------------------------------------------------------------------------------------------------------------------|
| true_breathing_duration        | `10`            | Duration in seconds of the standard True Breathing effect.                                                       |
| true_breathing_ii_duration     | `15`            | Duration in seconds of a True Breathing potion when brewed with redstone. 0 to disable brewing it with redstone. |
| dragon_true_breathing_duration | `60`            | Duration in seconds of a True Breathing potion combined with Dragon's Breath.                                    |
#### Death
| Option            | Default value | Description                                                                                                                                                                                      |
|-------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| death_xp_kept     | `0`           | A value from 0 to 100, determining what percentage of XP points / levels players keep after dying. Dropped XP is based on the lost experience.                                                   |
| death_border_kept | `0`           | A value from 0 to 100, determining what percentage of border is kept after dying. This is applied after calculating the kept experience, meaning it only applies to the portion of border you'd lose. |
| death_xp_lv       | `xp`          | Either `xp` or `lv`, determines whether the kept experience is calculated as a percentage of player level or player experience. `xp` should be more forgiving.                                   |
