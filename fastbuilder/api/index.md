# Fastbuilder API

API Last updated for: `v2.10.3`

*You can view the old docs [here](../old/api/)*

# Current Addons
These are the current addons that exist for fastbuilder.
You can use these for referance.

Custom GUI: https://github.com/hi12167pies/Fastbuilder-CustomGUI \
Bed Break Score: https://github.com/hi12167pies/Fastbuilder-BedBreakScore \
Schematics: https://github.com/hi12167pies/Fastbuilder-Schematics

# Getting the API Object
To get the api object you will do the following:
```java
FastbuilderAPI api = FastbuilderProvider.getAPI();
``` 

The api object itself, contains methods to get the FastbuilderPlayer, FastbuilderMode, and other classes.\
The most important one is the FastbuilderPlayer, This class is used to do most things in the api.

```java
FastbuilderAPI api = FastbuilderProvider.getApi();

// Get by player
FastbuilderPlayer examplePlayer = api.getPlayer(Bukkit.getPlayer("exampleUser"));
// Get by UUID
FastbuilderPlayer examplePlayer = api.getPlayer(UUID.fromString("123-456-789"));

FastbuilderMode exampleMode = api.getMode("short");

examplePlayer.getPersonalBest(exampleMode);
```

> **Note:** If you get by UUID some methods will not work as they require a player that is online. Any methods relating to date should work but anything relating to the in game player won't.

# Events
To listen to events you will need to do the following:
```java
@EventHandler
public void onScore(PlayerScoreEvent event){
    // Do something with the event
    Player player = event.getPlayer();
    // Cancel default message
    event.setSendScoreMessage(false);
    event.setSendPBMessage(false);
    // Send a new message
    player.sendMessage("You scored a time of " + event.getTime());
}
```

All the events in the plugin are:
- `PlayerJoinArenaEvent`
- `PlayerLeaveArenaEvent`
- `PlayerResetEvent`
- `PlayerScoreEvent`

*Additional support can be found in the [discord](https://pies.cf/contact)*