# Contents
- [Contents](#contents)
- [Introduction](#introduction)
- [Creating a TCP server](#creating-a-tcp-server)
- [Packet data types](#packet-data-types)
    - [VarInt](#varint)
- [Packet structure](#packet-structure)
    - [Length](#length)
    - [Packet ID](#packet-id)
    - [Data](#data)
- [State](#state)
    - [Handshaking](#handshaking)
    - [Status](#status)
    - [Login](#login)
    - [Play](#play)
- [Reading a packet](#reading-a-packet)
- [Writing a packet](#writing-a-packet)


# Introduction
This is about how a minecraft server and client communicate with each other. We will walk through the steps to create a basic packet reader, packet writer, and server.

# Creating a TCP server
To create a server you will need to make a http server that listens on `tcp/25565`

Once you have a server, you will need to listen for incoming connections.

# Packet data types
Once the client has connnected to the server, it will start sending some data. This data is encoded in the minecraft protocol format.

### VarInt
One of the data types sent is called a VarInt. A VarInt is a integer but instead of sending the 4 byte integer, a VarInt uses the first bit of each byte to say if the next byte is part of the integer.

For example the client could send this:

```
1st Byte (0x81): 10000001
2nd Byte (0x05): 00000101
```

How would we decode this?

1. First read 1 byte from the socket (`10000001`)
2. Then store the last 7 bits (as `value`) and ignore the first bit. (`value = 0000001`)
3. Now we can look at that first bit, since it contains a 1 we would read another byte (**__1__** 0000001)
4. We repeat this, appending all the values until we would get the final number

Our final value would be: `00001010000001` (`0x281` or `641`)

> Note: VarInt should only be 4 bytes maximum, any longer should throw any error!\
> Note: VarLong are only 8 bytes at maximum.

For more information (and code example): [wiki.vg - VarInt and VarLong](https://wiki.vg/index.php?title=Protocol#VarInt_and_VarLong)

# Packet structure
Now that we can read a VarInt we nee to read an actual packet.

Packets in minecraft all follow the same structure:

| Name | Type | Notes |
| --- | --- | --- |
| Length | VarInt | This is the length of the packet |
| Packet ID | VarInt | This is the id of the packet |
| Data | Byte Array | The remaining data is part of the packet |

What exactly does this mean? Lets break it down.

### Length
First we have a VarInt for `length`. This is the total byte size of the packet.

- For example we could read a VarInt to see the length of the packet, then read that amount of bytes.
- After reading all the data bytes we could turn it into some kind of packet serializer that we can use to read the packet.
- Then we could read a VarInt from that packet seralizer to get the packet id, and the rest is the packet data

### Packet ID
A packet ID is a way of identifying which packet is being sent. For example we could send a `0x00` which would mean a handshake packet.

> Note: Which packet the packet id means depends on the socket state. [See state](#state)

### Data
This is an byte array of data that is seralized using the protocol format. More on this later.

# State
State is a way minecraft uses to seperate different types of packets.

When the player connects to the server they are in the `handshaking` state. Certain packets may change the state. Most packets do not change state but some do.

There are 4 states:
- handshaking
- status
- login
- play

### Handshaking
When a client first connect to the server, they are the `handshaking` state. This state contains one packet which the client will send. Once this packet is recieved the packet contains a field which will switch into either `status` or `login`

More on this in reading packets.

### Status
This state is for the server list ping, to get the status of the server.

### Login
This status is to login to mojangs auth servers and encrypt the socket.

### Play
This is the main state which contains the packets for movement, placing, breaking, etc.

# Reading a packet

Since we now know the packet structure and states we can start reading packets!

Lets use a handshake packet for an example.

1. Client connnects, the state is `handshaking`
2. Client sends `handshake (0x00, handshaking)` packet


> Note: Code samples are not real code, just for examples. They are in "java" (java but simple)

Now we need to read the packet:
1. Read a VarInt from the socket
    ```java
    int length = readVarInt();
    ```
2. Read the data
    ```java
    byte[] data = readBytes(length);
    // For this example we will use DataReader to read data
    DataReader reader = new DataReader(data);
    ```
3. Read the packet id
    ```java
    int packetId = reader.readVarInt()

    if (state == Handshaking && packetId == 0x00) {
      // Now we can read the packet data
    }
    ```
4. Handle the packet now, you can see all packets on [wiki.vg](https://wiki.vg/index.php?title=Protocol)
5. Example:
    ```java
    byte[] data = readBytes(length);
    // For this example we will use DataReader to read data
    DataReader reader = new DataReader(data);

    int packetId = reader.readVarInt()

    if (state == Handshaking && packetId == 0x00) {
      // Now we can read the packet data
      // This is the handshake packet ( https://wiki.vg/index.php?title=Protocol&oldid=7368#Handshake )
      int protocolVersion = reader.readVarInt()
      String address = reader.readString()
      short port = reader.readUnsignedShort()
      int nextState = reader.readVarInt()
    }
    ```

# Writing a packet

This is very similar to reading a packet.

1. Create some kind of `DataWriter` or `Buffer` that we can write to.
2. Write the packet id:
   ```java
   buffer.writeVarInt(packetId)
   ```
3. 