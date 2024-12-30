Packets:

# Packet structure:
> All packets are sent like this in bytes:\
> `[Packet ID] [Data Type] [Data] [Packet Splitter]`\
> If a packet has multiple data the packet will look something like this"\
> `[Packet ID] [Data Type] [Data] [Data Splitter] [Data Type] [Data] [Packet Splitter]`\
> Data splitter is 0x0 and splits each data

# Data Types:
| ID | Name |
| --- | --- |
| 0x1 | String |
| 0x2 | Number |
| 0x3 | Boolean |
| 0x99 | Unknown / Error |

# Packets:
> For now we will not include the level packets since we are just trying to get something working \
> `None` means the packet is never sent from the client / server \
> Empty packets I didn't bother to document because we're just getting a basic server then we can add more stuff later

| ID | Name | Length of data  | Data types | Data to server | Data to client | Additional Infomation
| --- | --- | --- | --- | --- | --- | --- 
| 0x1 | Auth | 1 | [string] | [Requested username] | [AuthPacketMeta](#authpacketmeta)
| 0x2 | Location | 3 | [string,string,string] | [ignored, x, y] | [username, x, y] | `x` and `y` are sent as strings not integers. why? idk
| 0x3 | Sync | 2 | [string,string] | None | [client username, other players] | other players are a string joined with `\|$\|`
| 0x4 | Game Update |
| 0x5 | Level List |
| 0x6 | Level Data |
| 0x7 | Level Load |
| 0x9 | Chat | 1 | [string] | [message] | [message] 
| 0x98 | Custom | any | any | any | any | packet is never used
| 0x99 | Bad Packet | 0 | [] | [] | [] | eg. if the client sends a bad packet this can kick them and close the connection. (This packet isn't required)

# Getting the data for a packet
When you recieve you data you will need to split it into packets because they may be stitched together. So you will need to write some code to split them. You will split them at 0xff (255)

# Decoding a packet:

Lets say our packet is a `int[] bytes`

First we will need to get the packet id, easy enough
```java
int packetId = bytes[0]
```

Then you will need to get all of your packet data by spliting the rest of the data at `0x0`

After that you can get the data type:
```java
int dataType = data[0]
switch (dataType) {
  case 0x1:
    // String
    break
  case 0x2:
    // Integer
    // Don't need to handle this right now (also integers are sent werid)
  case 0x3:
    // Boolean
    // Don't need to handle this right now
}
```


# AuthPacketMeta
> Note that auth packet uses string so these are sent as strings \

| ID | Name |
| --- | --- |
| 0 | REJECT_ALREADY_CONNECTED |
| 1 | REJECT_NOT_ALLOWED |
| 2 | REJECT_LENGTH |
| 3 | REJECT_ALREADY_AUTHED |
| 4 | ACCEPT|

# Example

> Lets say we have  a server with `user1` `user2` and a few chat messages. This would be the full packet interaction: \
> C = CLIENT, S = Server 

| Direction | Packet | Data | Explanation |
| --- | --- | --- | --- |
| C -> S | AUTH | ["user3"] | Client requests "user3" name
| S -> C | AUTH | ["4"] | Server accepts username (see [AuthPacketMeta](#authpacketmeta))
| S -> C | SYNC | ["user3", "user1\|\$\|user2"] | Server sends all currently connected players
| S -> C | LOCATION | ["user", "100", "0"] | Server sends user1 location
| S -> C | LOCATION | ["user", "200", "0"] | Server sends user2 location
| S -> C | (Level data) | ... | Not gonna explain this rn
| C -> S | LOCATION | ["", "0", "0"] | Client sends server it's location

Done! Thats the entire packet system.

# Actual packets with packet logger
[this page](./packet_log)