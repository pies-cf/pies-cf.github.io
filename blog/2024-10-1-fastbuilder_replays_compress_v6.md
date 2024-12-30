# Making fastbuilder replays smaller... again (v6)

> I am writing this at 12:30 am so if some of this sounds weird it's because my brain isn't working.

Once again I have managed to make my replays smaller. If you do not know what I am talking about you should read my [first blog about this](https://blog.pies.cf/2024-09-30-fastbuilder_replays_compress)

Now I cannot take credit for everything, as my friend Kuwg helped me out a bit.

Here is the new compression methods we came up with.

## Delta encoding.
In the location ticks array I used range encoding. For the other arrays that still use a normal VarInt list, I swapped to delta encoding.

Delta encoding works by saving the difference since the last item.
```txt
[1, 2, 3, 5]

[1, 1, 1, 2]

Starts at 1
Next is 1, so we add 1 to the last and get 2.
Then there is another 1, the last value is 2 so now it's 3.
And then the number is 2, which gets added to 3 and becomes 5
```

## VarFloats
VarInts are a way of having smaller ints use less bytes. So I applied this same logic to VarFloats.

These VarFloats were invented by me, as chatgpt did not give me any good ways of making VarFloats.

First I split the number into two parts, the whole part and the decimal part. `123.456` -> `123` and `456`.

Then I remove any extra nuumbers from the decimal part as I only need 3 points of accuracy

Now I save this as two VarInts and this gave a HUGE reduction in size.

## Deduplicating locations
I thought this already existed but apparently it didn't. This is extremely simply, if you do not move, do not save the location. My old system would save your location every tick. While this does not directly reduce the size, in long replays if there is periods of no movement it will reduce the size.
