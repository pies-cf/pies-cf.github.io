# Class `FBManager`

| Method | Description | Extra info
| --- | --- | --- |
| <pre><code class="hljs language-java">public double getPersonalBest(Player player, String mode)</code></pre> | Get the players current personal best in a mode.
| <pre><code class="hljs language-java">public void setPersonalBest(Player player, String mode, double newpb)</code></pre> | Set the players current personal best in a mode.
| <pre><code class="hljs language-java">public double getCoins(UUID playerUUID)</code></pre> | Get the amount of coins the player currently has.
| <pre><code class="hljs language-java">public void addCoins(UUID playerUUID, double amountToAdd)</code></pre> | Add or remove player coins / currency | `amountToAdd` - Use negitive to remove coins.
| <pre><code class="hljs language-java">public void buyBlock(Player player, Material material, int type)</code></pre> | Buy a block from the shop.
| <pre><code class="hljs language-java">public void selectBlock(Player player, Material material, byte type)</code></pre> | Set the player selected block.
| <pre><code class="hljs language-java">public boolean hasBlock(Player player, Material material, int type)</code></pre> | Check if player owns a block.
| <pre><code class="hljs language-java">public void setPickaxe(Player player, Material material)</code></pre> | Set the player selected pickaxe.
| <pre><code class="hljs language-java">public Material getPickaxe(Player player)</code></pre> | Player current selected pickaxe.
| <pre><code class="hljs language-java">public boolean isPlaying(Player player)</code></pre> | Player is currently in game or not.
| <pre><code class="hljs language-java">public String getTime(Player player)</code></pre> | Get the current time on the players timer formatteed to 3 decimals.
| <pre><code class="hljs language-java">public int getBlocksPlaced(Player player)</code></pre> | Get a number of how many blocks the player has placed.
| <pre><code class="hljs language-java">public String getCurrentMode(Player player)</code></pre> | Get the current mode the player is in.
| <pre><code class="hljs language-java">public String getFormattedLBPosition(Player player, int pos)</code></pre> | Get the formatted style of a leaderboard postion in any slot.
| <pre><code class="hljs language-java">public DecimalFormat getDefaultDF()</code></pre> | Util.
| <pre><code class="hljs language-java">public void resetPlayer(Player player, boolean resetBlocks, boolean scored)</code></pre> | Reset the player to spawn
| <pre><code class="hljs language-java">public void resetPlayerInventory(Player player)</code></pre> | Reset the inventory in the player.
| <pre><code class="hljs language-java">public String getNextValidArena(String mode)</code></pre> | Get the next arena in a mode that is not used and is setup correctly
| <pre><code class="hljs language-java">public void leave(Player player, boolean ignore, boolean sendLeaveMessage, boolean runEndCommands)</code></pre> | Leave the player from the current game | `ignore` - If true it will not send the player to lobby if bungeecord mode is enabled.
| <pre><code class="hljs language-java">public void join(Player player, String mode)</code></pre> | Join the player to the specified mode.
| <pre><code class="hljs language-java">public void join(Player player, String mode, String map)</code></pre> | Join the player to the specified mode and map.
| <pre><code class="hljs language-java">public boolean isWatchingReplay(Player player)</code></pre> | Check if the player is currently watching a replay.
| <pre><code class="hljs language-java">public boolean isReplayExist(Player player)</code></pre> | Check if there is a replay currently attached to the user.
| <pre><code class="hljs language-java">public String getPrefix(boolean replay)</code></pre> | Get the prefix of the plugin | `replay` - Get the replay prefix.
| <pre><code class="hljs language-java">public String getModeFullMessage()</code></pre> | Get the message sent wehn the mode is full.
| <pre><code class="hljs language-java">public FileConfiguration getConfig()</code></pre> | Get the plugins configuration file.