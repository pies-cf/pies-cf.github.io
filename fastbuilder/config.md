# Config

- [Config](#config)
  - [Info on how it works](#info-on-how-it-works)
  - [Prefix \& Replay Prefix](#prefix--replay-prefix)
  - [Storage](#storage)
  - [BungeeCord](#bungeecord)
  - [Theme](#theme)
  - [Plates](#plates)
  - [Island Jumping](#island-jumping)
  - [Title](#title)
  - [Mode NPC](#mode-npc)
  - [Commands](#commands)
  - [Modes](#modes)
  - [Max Times](#max-times)
  - [Currency](#currency)
  - [Messages](#messages)
  - [Scoreboard](#scoreboard)
  - [Blocks](#blocks)
  - [Holograms](#holograms)

## Info on how it works
```yaml
# If it says "a.b" it means:
a:
  b: 'some value'

# So storage.method is
storage:
  method: 'some value'

# title.score.title is
title:
  score:
    title: 'some value'

# etc, etc...
```

## Prefix & Replay Prefix

```yaml
prefix: "&8[&6Fastbuilder&8] &7"
replayprefix: "&8[&3Replays&8] &7"
```
- `prefix` This is the prefix in all messages sent by the plugin.
- `replayprefix` This is the prefix in for the replays section of the plugin, does same as normal prefix but just for replays 

## Storage

```yml
storage:
  method: 1
```

- `storage.method` The method fastbuilder stores data. Please keep as `1` as anything else will break the plugin.
  - `1` YAML
  - `2` MySQL (Doesn't work)

Placeholders you can use the messages:`
- `%time%` The time the player scored
- `%currency%` The amount of currency the player earned

## BungeeCord

```yaml
bungeecord:
  enabled: false
  a: true
  lobby: lobby
  default_mode: short
  overflow: long
  prevent_login:
    enabled: true
    message: "Fastbuilder is currently full."
```

- `bungeecord.enabled`
- `bungeecord.a` **Do not edit** Changes wether you join fastbuilder or not on server join. Not made for use and only made for addon that never released
- `bungeecord.lobby` This tells fastbuilder the lobby server to send to when you leave the game.
- `bungeecord.default_mode` This is the mode that you will join by default when joining the server.
- `bungeecord.overflow` If `default_mode` is full it will fill this mode up next
> Prevent Login
- `bungeecord.prevent_login.enabled` Optional to enable or disable preventing players joining if fastbuilder is full
- `bungeecord.prevent_login.message` This is the message that will show up in the kick message.

## Theme

```yaml
theme:
  color: b
  glass_pane: 3
  items:
    blocks0: 0
    blocks1: 1
    pickaxe: 2
    replay:
      name: "&5Replay &7(Right-Click to use)"
      item: BOOK:0
      slot: 6
    settings:
      name: "&2Settings &7(Right-Click to use)"
      item: EMERALD:0
      slot: 7
    leave:
      name: "&cLeave &7(Right-Click to use)"
      item: EYE_OF_ENDER:0
      slot: 8
```

- `theme.color` The colour that your server is themed around. Must be a colour like `&a`, `&b`, `&3`. (Look Up Minecraft Colour Codes)
- `theme.glass_pane` The type of glass pane that will show in the guis. Indicates the colour.
> Items
- `theme.blocks0` The slot that the first stack of blocks is in.
- `theme.blocks1` The slot that the second stack of blocks is in.
- `theme.pickaxe` The slot that the pickaxe or tool is in.
- `theme.<item>.name` The display name of the selected item
- `theme.<item>.item` The item that will be placed in the users hotbar
- `theme.<item>.slot` The slot that the item is placed in

## Plates

```yml
plates:
  short:
    # List all plates, "X Y Z"
    - "26 3 1"
    - "26 3 0"
    - "26 3 -1"
  practice:
    - "26 3 1"
    - "26 3 0"
    - "26 3 -1"
```

**THIS SECTION IS OPTIONAL BUT WILL MAKE PLATES MORE ACCURATE**

The offset of each plate base from spawn location, for each mode.

If you do understand what this mean you may want to just leave this out and change it to the following:

```yml
plates: {}
```

## Island Jumping

```yml
# Island Jumping
# For this feature to work you must use numbers to create your islands.
# EG. /fbmap create short 0
jumping:
  # Mode
  short:
    # Enable / Disable the island jumping feature
    enabled: true
    add: NEGATIVE_Z
    subtract: POSITIVE_Z
    maps: 6
```

**You must have your maps next to each other and called 0, 1, 2, 3, etc... in order**

- `jumping.<mode>.enabled` Should island jumping be enabled in this mode
- `jumping.<mode>.add` Which way do you jump to go up by one island (eg. from island 0 to island 1)
  - **Options:**
  - POSITIVE_X
  - NEGATIVE_X
  - POSITIVE_Z
  - NEGATIVE_Z
- `jumping.<mode>.subtract` Which way do you jump to go down by one island (eg. from island 1 to island 0)
  - **Options:**
  - POSITIVE_X
  - NEGATIVE_X
  - POSITIVE_Z
  - NEGATIVE_Z
- `jumping.<mode>.maps` The amount of maps in one mode (if you had maps with name 0, 1 and 2 then this value would be 3 as it is 3 maps)

## Title

```yaml
title:
  score:
    # Use [default] for built in
    title: "[default]"
    subtitle: "[default]"
```

- `title.score.title` This is the main title that shows up when you score a new time
- `title.score.subtitle` This is the smaller (sub) title that shows up when you score a new time

## Mode NPC

```yml
npc:
  enabled: true
  name: "&eMode Changer"
  # [player_skin] for current player skin
  skin: "[player_skin]"
  # Offset the npc from spawn location
  x: -1
  y: 1
  z: 1
  yaw: -90
  pitch: 0
  # If you want modes such as diag to be different
  mode:
    practice:
      x: -1
      y: 1
      z: -1
      yaw: 90
      pitch: 0
```

- `npc.enabled` Should the mode selector npc show at all?
- `npc.name` The name of the mode selector npc
- `npc.skin` The skin of the mode selector npc
- `npc.x` The X Offset from spawn of the npc (Location realitive to spawn)
- `npc.y` The Y Offset from spawn of the npc (Location realitive to spawn)
- `npc.z` The Z Offset from spawn of the npc (Location realitive to spawn)
- `npc.yaw` The yaw that the npc looks at.
- `npc.pitch` The pitch that the npc looks at.
- `npc.mode.<mode>.x` The X Offset from spawn of the npc only in the specified mode (Location realitive to spawn)
- `npc.mode.<mode>.y` The Y Offset from spawn of the npc only in the specified mode (Location realitive to spawn)
- `npc.mode.<mode>.z` The Z Offset from spawn of the npc only in the specified mode (Location realitive to spawn)
- `npc.mode.<mode>.yaw` The yaw that the npc looks at in the specified mode.
- `npc.mode.<mode>.pitch` The pitch that the npc looks at in the specified mode.

## Commands

```yml
# These commands will be executed on join and leave arena.
commands:
  enabled:
    join: false
    leave: false
  join_arena:
    - "/DoSomething"
  leave_arena:
    - "/DoSomething"
```

- `commands.enabled.join` Should command be ran when a player joins?
- `commands.enabled.leave` Should command be ran when a player leaves?
- `commands.join_arena` A list of commands that will be executed when a player joins an arena.
- `commands.leave_arena` A list of commands that will be executed when a player leaves an arena.

## Modes

```yml
modes:
  0:
    # The name of the item in the GUI
    display: "&eShort"
    # The amount of item
    amount: 21
    # The mode it joins
    mode: "short"
    # The item shown
    item: "SANDSTONE:0"
  1:
    display: "&aPractice"
    amount: 5
    mode: "practice"
    item: "SANDSTONE:0"
```

- `modes.<slot>.display` The display name of the item in the GUI
- `modes.<slot>.amount` The amount of a item.
- `modes.<slot>.mode` The mode the player will join when clicking on the item
- `modes.<slot>.item` The item displayed in a GUI

## Max Times

```yml
max_times:
  # if a player scores below this time the run will not count
  short: 1
```

- `max_times.<mode>` This is a number that if a place scores below the time will not count.

## Currency

```yml
currency:
  disabled_modes:
    - practice
  display:
    # in guis and stuff
    a: "&2Emeralds"
    # titles or just cool
    b: "&2&lEm&a&ler&2&lal&a&lds"
    # the + colour
    c: "a"
    # this option is the colour of when you score like + x emerlads colour of x
    d: "f"
    # no formatting
    e: "Emeralds"
```

- `currency.disabled_modes` A list of modes that you cannot earn currency in.
- `currency.display.a` The colour of your currency and it's name
- `currency.display.b` What shows when you score or is a fully formatted version of your currency name
- `currency.display.c` The colour of the + in the score title
- `currency.display.d` The colour of amount of currency your earnt in the score title
- `currency.display.e` No formatting, just the name

## Messages

```yml
messages:
  new_pb: "&a&lGG! You have passed your old best!"
```

- `messages.new_pb` A message sent to the play when they score. You can also use the placeholder `%time%`

## Scoreboard

```yml
scoreboard:
  # use <d> for displayname
  leaderboard: "&7<p> &7&l: &e<t>"
  title: '&6&lExample&7&l.&6&lnet'
  scores:
    15: "&a&8&l|"
    14: "&b&8&l| &b&lPersonal best&7&l:"
    13: "&c&8&l| &e%pb%"
    12: "&d&8&l|"
    11: "&e&8&l| &e&lSession&7&l-&6&lTop 3&7&l:"
    10: "&f&8&l| &e%leader_1%"
    9: "&0&8&l| &e%leader_2%"
    8: "&1&8&l| &e%leader_3%"
    7: "&2&8&l|"
    6: "&3&8&l| &2&lEmeralds&7&l:"
    5: "&4&8&l| &e%currency_amount%"
    4: "&5&8&l|"
    3: "&6&8&l| &3&lTime&7&l:"
    2: "&7&8&l| &e%time%"
    1: "&8&8&l &o- &6&oExample.net &8&l&o-"
```

- `scoreboard.leaderboard` This is the formatting of whaat is displayed in the leader placeholder
- `scoreboard.title` This is the title of the scoreboard
- `scoreboard.scores.<line>` The text displayed on the line

See [Placeholders](./placeholders) for placeholders.

## Blocks

```yml
blocks:
  0:
    # item
    item: "SANDSTONE:0"
    # price (0 = free)
    price: 0
```

- `blocks.<id>.item` The item of the block
- `blocks.<id>.price` The price the block costs. Use 0 for free
- `blocks.<id>.permission` The permission required to purchase or use the block.

## Holograms

```yml
holo:
  enabled: true
  offset:
    x: -2
    y: 2
    z: 2
    mode:
      exampleMode:
        x: 2
        y: 2
        z: 5
  lines:
    - "&3Best time: &e%pb%"
    - "&3Bridging Attempts: &e%overall_attempts%"
    - "&3Successful Attempts: &e%successful_attempts%"
    - "&3Failed Attempts: &e%failed_attempts%"
```

- `holo.enabled` If the holohrams should be enabled.
- `holo.offset` The offset of the hologram relative to the spawn.
- `holo.offset.mode` If you need a seperate position for a different mode you can put them in this.
- `holo.lines` Content of the holograms.

[Next Section (Commands)](./commands)