# Setting up first map (Old method, do not use unless you cannot use the interactive fbsetup)
> For this tutorial we wil be creating maps in the mode `test`
1. Stand at where you spawn on the map and type `/fbmap set test 0 spawn`
    - `/fbmap set` - setting a location in the map
    - `test` - the mode that this map is in, when we join all maps in this mode will be available to join
    - `0` since this is our first island we will use zero, when we repeat this proccess for another map, change this to 1, 2, etc...

    **Example of spawn:**

    <img src="/img/2023-01-29_11.09.39.png" alt="Screenshot" width=384 height=203>
2. Stand at the lower-left hand side of your map type `/fbmap set test 0 pos1` ***This also sets the void height for that map***

    **Example of pos1:**
    <img src="/img/2023-01-29_11.10.26.png" alt="Screenshot" width=384 height=203>
3. Stand at the upper-right hand side of the map and type `/fbmap set test 0 pos2`

    **Example of pos2:**
    <img src="/img/2023-01-29_11.10.50.png" alt="Screenshot" width=384 height=203>

4. Now that you have created a map in mode `test`, join it with `/fb join test`
5. Please check [config](./config) to see how to setup scoring with plates