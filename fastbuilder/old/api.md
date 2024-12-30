# Fastbuilder API

***Last updated for FastBuilder v2.1.0***

- [Fastbuilder API](#fastbuilder-api)
  - [Getting the API Object](#getting-the-api-object)
  - [Custom scoreboard manager](#custom-scoreboard-manager)
  - [Listening to events](#listening-to-events)
  - [Fastbuilder Manager](#fastbuilder-manager)

## Getting the API Object

```java
// somewhere in your code
FastbuilderAPI api = FastbuilderProvider.getApi();
```

## Custom scoreboard manager

Create a new class called `CustomScoreboard` (or whatever you wish to call yours) and implement the FBScoreboard interface.
```java
import FastbuilderPlugin.FBScoreboard;
import org.bukkit.entity.Player;

public class FBCustomScoreboard implements FBScoreboard {
    @Override
    public void createScoreboard(Player player, String title) {
      // This method is called once when the player joins or a scoreboard needs to be made
    }

    @Override
    public void setLine(Player player, String line, int slot) {
      // This method gets called every tick
      // line - the parsed line with colour and everything.
      // slot - the slot the line is on
    }

    @Override
    public void remove(Player player) {
      // This method is called when the player leaves or whenever the scoreboard should be deleted
    }
}
```

Then we need to set the scoreboard to our custom class

```java
api.setScoreboardManager(new FBCustomScoreboard());
```

## Listening to events

```java
EventManager eventManager = api.getEventManager();

// Change the Events.RESET to other events accordingly
eventManager.listen(Events.RESET, e -> {
  // This is called every time this event is fired.
  // To access data about the event you must cast FBEvent to FB(eventname) so our will be like the following:
  FBResetEvent event = (FBResetEvent) e;
  // Use the event to access information about the event
  if (event.hasScored()) {
    event.getPlayer().sendMessage("hello");
  }
});
```

## Fastbuilder Manager

The fastbuilder manager is a class to access useful methods.

```java
FBManager manager = api.manager();

if (manager.playing(player)) {
  // Do something
}

if (manager.currentMode(player).equals("something")) {
  // Do something
}

// there is more methods
```