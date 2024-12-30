# Making fastbuilder replays smaller.

## What are fastbuilder replays?
Fastbuiler is a practice mode for minecraft where you must bridge to the other side. A feature of this practice mode is replays. where it tracks exactly what you did and it can replay it later to see how you did.

The issue was that these save files used to be very big and server owners using my plugin was complaining or resetting their replays folder very often due to it being 
massive.

To fix this I made a replay version system, where each version I added something new to make the compression smaller.

## Sneak ticks
Before I even talk about the file format, this was a small little way of making replays smaller that is in all versions.

There is two arrays saved for everything the player does, a list of `ticks` and a list of `actions`. For example in the tick array it would have `1` and in the actions array it would have `true` which means at tick 1, the player is sneaking.

Now since this is just a boolean I simply only saved the first action for sneaking and then every tick that the player sneaked or unseaked I swap that boolean which means I don't have to save a list of every sneak/unseak.

## Version 1 - Original saving
In Bukkit (Minecraft server software) a very common way of saving player data and data in general in plugins is using `yml`. And as I did not know of any better way I also saved the replays in yml.

Now I was smart enough to realise if I saved using pure yml then it would be massive. So I saved using strings. For example:
```yml
locations:
  move: "1;2;3;4;5,1;2;3;4;5"
```
This is a list of locations, split at a `,`. Then in the list each location is split by `l` and is formatted `x;y;z;yaw;pitch`

This was already good (at least I thought) as I did not have to save it as:
```yml
locations:
  move:
    - x: 1
      y: 2
      z: 3
      yaw: 4
      pitch: 5
```

The size of these replays were massive though, for a 40 second replay it was around `38kb`. We will use this as our baseline.

## Version 2 - Discovering binary.
A long time after making version 1, I was remaking a [Minecraft server from scratch](https://github.com/hi12167pies/mc-server-ts2) which taught be about how Minecraft protocol works and how binary works. And I thought it was awsome, I could apply this same binary compression to saving files and save the exact same data but way smaller AND it looks like random text which means it's surely a real file format 😎

So I simply saved it as binary now. Using our location example, the code would look like this.
```java
stream.writeDouble(location.x)
stream.writeDouble(location.y)
stream.writeDouble(location.z)
stream.writeFloat(location.yaw)
stream.writeFloat(location.pitch)
```
This uses bytes instead of readable text, which is much smaller. As well as this there is no longer a need for using split characters as we know exactly how the data is formatted.

There was one more thing I used in this version to make the file size smaller which was GZip compression. It may not be custom, but it definitely reduced the file size.

For the same 40 second replay (exact same data) these replays took `16.4kb` more than half the size of the version 1!

## Version 3 - Less Precision.
As of currently, all the data is saved exactly the same as in the replay object. Integers are integers, locations are 3 doubles and 2 floats. But for some of these data types I don't need the extra accuracy of doubles/ints.

### Using shorts instead of ints.
I currently have 4 integer arrays which contain a list of ticks where the player is doing certain actions. Since they are using integers, each integer uses `4 bytes` and most of thoses byte are empty! The max size of a `short` is `65536` and in seconds it is `3932.16` which is definitely long enough as the replays max out at 120 seconds.

### Using floats instead of doubles
The current locations are all saved relative to the spawn location of the island. And due to this the locations are mostly between `0-50` and with the decimal point needing to be accurate to at least 3 points.

At first I was going to make my own number format as I only needed 3 points of  decimal accuracy. The issue was that the decimal needs 2 bytes (1 byte is 255, 2 is 65536) and then the whole number part needs at least 2 bytes which is same as float. Floats can store much bigger nnumbers that 65536 which I do not understand, so I went with using floats instead of doubles.

Using floats went from `8 bytes (double)` to `4 bytes (float)`

For that same 40 second replay the file size was `11kb` (v2 was `16.4kb`) That's pretty decent change!

## Version 4 - VarInts
Now this is when I remembered in the Minecraft protocol, Mojang uses `VarInts`. These are the same as integers but use a variable length encoding. That means the size of bytes it takes up changes based on how much data needs to be stored.

### How VarInts work
VarInts work by using the first bit of each number to say if the next byte is part of the integer.
```txt
00000001
^
Here. This is 0 which means this is the entire int.

10000001 00000001
^
Here. This is 1 which means this is the first 7 bits of the int and the next byte has the rest of the data.

10000001 00001001
         ^
The next byte has a zero which means the full int is 0001001 0000001. (The last 7 bits of each byte joined together)
```
Since the first bit is used to tell if the next byte is part of the int, it does make the max size of a integer smaller, but it does not matter for this use.

I applied VarInts to the tick arrays, and any other int that is used in the format.

Now there was not much of a difference between this and version 3. Only around 100 bytes difference but it's still better than nothing - And now we aren't using short lists incase there is a super long replay.

```
11337 3.fbr  v3
11204 4.fbr4 v4
```

## Version 5 - Very cool compression tricks.
At this point I had no idea how I could make these replays smaller. Then when viewing the replays in a text editor I noticed that there was some human readable text. It looked like:
```txt
!"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}
```
I instantly realised this was the location tick array. Since in a replay you are moving almost all the time, the location ticks look like `1, 2, 3, 4, 5, 6, 7, 8, 9, 10, etc...` - You are moving every tick.

### Range Encoding (I don't know what this is actually called)
I also do not care what this is called (shhh). I am also 100% not the person that came up with this but I did come up with it by myself :D.

This works by changing `1, 2, 3, 4, 5, 6, 7, 8, 9, 10` to just `1, 10` and saying that it ranges from 1 to 10. This was a great way of making size smaller. If there was time where you aren't moving simply just add another range. `(1, 10), (15, 30), (35, 50)`

### Block locations
Block locations were being saved as the full 5 floats. Which is a crazy waste since a block cannot be at `0.5` and does not have a yaw or pitch. I swapped to using 3 VarInts for blocks locations `x, y, z` which is much better.

Finally v5 saving that same 40 second replay is `9.7kb`.

Compared to every other version.
```txt
bytes  name          version
38 158 replay-1.yml  v1
16 401 2.fbreplay    v2
11 337 3.fbr         v3
11 204 4.fbr4        v4
 9 769 5.fbr5        v5
```

# Conclusion
I have learned a lot about saving binary, and compression methods. I went from saving data as strings in a `yml` file to making my own file format with multiple versions, each getting smaller.

Thank you for reading.

P.S If you have any other ideas for making this smaller please contact me on discord - hi12167pies :)