[Back](../)
<br>

# Interface `FBScoreboard`

```java
public interface FBScoreboard {
  void createScoreboard(Player player, String title);
  void setLine(Player player, String line, int slot);
  void remove(Player player);
}
```

Implementation:

```java
public class YourClass implements FBScoreboard {
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