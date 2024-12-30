# Commands

- [Commands](#commands)
    - [How this page works](#how-this-page-works)
    - [/fastbuilder](#fastbuilder)
    - [/fastbuildermap](#fastbuildermap)
    - [/logs](#logs)
    - [/stats](#stats)
    - [/pb](#pb)
    - [/replays](#replays)
    - [/fbcurrencyadd](#fbcurrencyadd)
    - [/fbreload](#fbreload)

### How this page works
To see the permission please look at [Permissions](./permissions)

- If a argument is surrounded with `<>` it means it is required.
- If a argument is surrounded with `[]` it means it is optional.
- All commands have a alias /fb[name] (eg. `/logs` also has `/fblogs`)

### /fastbuilder
- **Usage:** `/fastbuilder <join/leave> [mode]`
  - `join` - Join the mode in the next argument
  - `leave` - Leave the current game
- **Description:** For joining and leaving the game.
- **Examples:**
  - `/fb join test` - Join the mode test
  - `/fb leave` - Leave the current game
- **Aliases:**
  - `/fb`
  - `/fastbridge`

### /fastbuildermap
- **Usage:** `/fastbuildermap <delete/set> <mode> <map> [spawn/pos1/pos2]`
  - `set` - Set a location of the island
    - `mode` - The mode that the map is in
    - `map` - A number of the map id (first map is 0, then 1, then 2, etc...)
    - `spawn/pos1/pos2` - The position you are setting.
      - `spawn` - The position which the player is spawned in when they join the map
      - `pos1` - A position to the lower left hand side of the map to set its borders. This position also sets the void.
      - `pos1` - A position to the upper right hand side of the map to set its borders.
  - `delete` - Delete a map
    - `mode` - The mode that the map is in
    - `map` - A number of the map id (first map is 0, then 1, then 2, etc...)
- **Description:** For setting up maps.
- **Examples:**
  - `/fbmap set short 0 spawn` - set the spawn position for map 0 in mode short
  - `/fbmap set short 0 pos1` - set the pos1 position for map 0 in mode short
  - `/fbmap set short 0 pos2` - set the pos2 position for map 0 in mode short
  - `/fbmap delete short 0` - delete the map short 0
- **Aliases:**
  - `fbmap`

### /logs
- **Usage:** `/logs`
- **Description:** To toggle messages in chat when a player scores. (on / off)
- **Aliases:**
  - `fblogs`

### /stats
- **Usage:** `/stats <player> <mode>`
  - `player` - A player connected to get the stats of.
  - `mode` - The mode to get stats from.
- **Description:** View other players stats
- **Examples:**
  - `/stats hi12167pies short` - get hi12167pies stats in mode short
  - `/stats hi12167pies long` - get hi12167pies stats in mode long
- **Aliases:**
  - `fbstats`

### /pb
- **Usage:** `/personalbest <player> <mode> [time set]`
- **Description:** This command is current being re-worked. It is not currently available.
- **Aliases:**
  - `personalbest`
  - `fbpb`
  - `fbpersonalbest`

### /replays
- **Usage:** `/replays <view/save> [player]`
- **Description:** View another players replay or save a failed attempt.
- **Examples:**
  - `/replays view hi12167pies` - view hi12167pies replays
  - `/stats save` - save a unfinished attempt (not recommended to use)
- **Aliases:**
  - `fbreplays`

### /fbcurrencyadd
- **Usage:** `/fbcurrencyadd <player> <amount>`
- **Description:** Add or remove a amount of currency from a player
- **Examples:**
  - `/fbcurrencyadd hi12167pies 500` - give hi12167pies 500 of the set currency
  - `/fbcurrencyadd hi12167pies -500` - remove 500 of the set currency from hi12167pies

### /fbreload
- **Usage:** `/fbreload`
- **Description:** Reload the config
- **Examples:**
  - `/fbreload`

[Next Section (Permissions)](./permissions)