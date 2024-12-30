# Creating a basic minecraft server in typescript

I have been making a minecraft server written in typescript (for 1.8 or pvn 47) with as little libraries / packages as possible. This is about how I wrote the basic code that reads and writes packet.

The example code is in typescript.

All the infomation about packets from [wiki.vg](https://wiki.vg/index.php?title=Protocal&oldid=7407)

## TCP Socket

Minecraft uses a TCP socket to commincate with the server. The default port is `25565` so we will use that.

Lets create a server and handle incoming conections. When a new connection is made we will create a `Connection` object

```ts
const server = net.createServer()

server.on("connection", socket => {
  // Create custom Connection object
  const connection = new Connection(socket)
  
  socket.on("error", (err: any) => {
    // Handle disconnecting
    if (err.code == "ECONNRESET" || err.code == "ECONNABORTED") return
    console.log("Socket Error", err)
  })
})

server.listen(25565, () => {
  console.log("Minecraft server started on port 25565")
})
```

Connection:
```ts
class Connection {
  constructor(public socket: net.Socket) {}
}
```

## Reading a byte
In typescript data is sent with a event unlike other languages that use `readByte()`. I made my own `readByte()` function, you can skip this section if it does not apply to your language

```ts
class Connection {
  constructor(public socket: net.Socket) {
    socket.on("data", data => {
      // Wrote data to buffer
      for (let i = 0; i < data.byteLength; i++) {
        this.dataBuffer.push(data.readUint8(i))
      }
      // Call any listeners waiting for data
      this.byteListeners.forEach(listener => {
        listener()
        // Delete from array since listeners are only needed once
        this.byteListeners.delete(listener)
      })
    })
  }
  
  private dataBuffer: number[] = []
  private byteListeners: Set<any> = new Set()

  // This will read a single byte from the buffer
  private readByteFromBuffer(): number {
  let byte = this.dataBuffer.shift()
  if (byte == undefined) throw new Error("No data in buffer.")
  return byte
  }

  public readByte(): Promise<number> {
    return new Promise((resolve, reject) => {
      // If there is data in the buffer we can read it
      if (this.dataBuffer.length > 0) {
        resolve(this.readByteFromBuffer())
        return
      }
      // If there is nothing we will wait unitl there is data
      this.byteListeners.add(() => {
        resolve(this.readByteFromBuffer())
      })
    })
  }
}
```

## Writing a byte

Writing a byte is quite simple, I will write it to a buffer, then we will make a `flush()` method that will send the data:

```ts
class Connection {
  private writeBuffer: number[] = []
  public writeByte(byte: number) {
    this.writeBuffer.push(byte)
  }

  public flush() {
  let buffer = Buffer.from(this.writeBuffer)
  this.socket.write(buffer)
  this.writeBuffer = []
  }
}
```

## Buffer Reader & Writer
In my server I made two classes for reading from the socket and writing to the socket. These classes will be extended on later to allow us to read multiple types of data from our socket:

```ts
export interface BufferWritable {
  writeByte(value: number): void
}

class BufferWriter {
  private input?: BufferWritable
  constructor(input: BufferWritable) {
    this.input = input
  }
}

export interface BufferReadable {
  readByte(): Promise<number>
}

class BufferReader {
  private input?: BufferReadable | number[]
  constructor(input: BufferReadable | number[]) {
    this.input = input
  }

  public async readUnsignedByte(): Promise<number> {
    if (isBufferReadable(this.input)) {
      const data = await this.input.readByte()
      return data
    }
    // The input can be an array optionally for later use
    if (this.input instanceof Array) {
      const array = this.input as number[]
      return new Promise((resolve,reject) => {
        const data = array.shift()
        if (data == null) throw new Error("Attempted to read more data than available")
        resolve(data)
      })
    }
    throw new Error("Failed to read from buffer")
  }
}
```

Then we will make them in the connection:
```ts
class Connection implements BufferReadable, BufferWritable {
  public reader: BufferReader = new BufferReader(this)
  public writer: BufferWriter = new BufferWriter(this)
}
```

## Data Types

Now that we can read and write bytes we need to be able to read some data types.

### VarInt
A VarInt is a Data Type that uses the 7 least significant bits to encode the value and the most signifcant bit to indicate if the next byte is part of the Int.

Lets add this to our `BufferReader` and `BufferWriter` [wiki.vg - VarInt and VarLong](https://wiki.vg/index.php?title=Protocal&oldid=7407#VarInt_and_VarLong)

My method of reading VarInt's is to read it with MetaData. Why? Later on you will need to know how many bytes were read and you cannot do that with the value alone.

```ts
class BufferReader {
  // Read VarInt with the MetaData about how many bytes were read
  public async readVarIntWithMetadata(): Promise<{value: number, bytesRead: number}> {
    let value: number = 0
    let position: number = 0
    let currentByte: number
    let bytesRead: number = 0

    while (true) {
      currentByte = await this.readUnsignedByte()
      bytesRead++
      value |= (currentByte & 0x7F) << position

      if ((currentByte & 0x80) === 0) break

      position += 7;

      if (position >= 32) throw new Error("VarInt is too big")
    }

    return { value, bytesRead }
  }

  // readVarInt will just return the value
  public async readVarInt(): Promise<number> {
    return (await this.readVarIntWithMetadata()).value
  }

  public async readVarLong(): Promise<number> {
    let value: number = 0
    let position: number = 0
    let currentByte: number

    while (true) {
      currentByte = await this.readUnsignedByte()
      value |= (currentByte & CONSTANT.DATA.SEGMENT_BITS) << position;

      if ((currentByte & CONSTANT.DATA.CONTINUE_BIT) == 0) break;

      position += 7;

      if (position >= 64) throw new Error("VarLong is too big");
    }

    return value
  }
}
```

### String
Strings are also important for seneding data. They are written with the length prefixed in a VarInt

```ts
class BufferReader {
  public async readString(): Promise<string> {
    const length = await this.readVarInt()
    let string = ""
    for (let i = 0; i < length; i++) {
      string += String.fromCharCode(await this.readUnsignedByte())
    }
    return string
  }
}

class BufferWriter {
  public writeString(string: string) {
    // Write the length
    this.writeVarInt(string.length)
    // Write the chars as bytes
    string.split("").forEach(charString => {
      const char = charString.charCodeAt(0)
      this.writeUnsignedByte(char)
    })
  }
}
```

### Byte Array
This is just a list of bytes
```ts
class BufferReader {
  public async readUnsignedBytes(length: number) {
    let bytes = []
    for (let i = 0; i < length; i++) {
      const byte = await this.readUnsignedByte()
      bytes.push(byte)
    }
    return bytes
  }
}

class BufferWriter {
  public writeUnsignedByteArray(bytes: number[]) {
    for (let i = 0; i < bytes.length; i++) {
      this.writeUnsignedByte(bytes[i])
    }
  }
}
```

The rest of the data types you can implement yourself, this is enough to start us off ([wiki.vg - Data Types](https://wiki.vg/index.php?title=Protocal&oldid=7407#Data_types))

## Packet format
Packets are formatting like the following:

| Field | Field Type | Notes |
| --- | --- | --- |
| Length | VarInt | Length of the packet data + Length of the packet ID
| Packet ID | VarInt | See for list of packet ids [wiki.vg](https://wiki.vg/index.php?title=Protocal&oldid=7407)
| Data | Byte Array | This is the data in the packet, this data depends on the packet id and the [connection state](#connection-state)

Let's read a packet:
```ts
async function readPacket() {
  // Read length and ID
  const length = connection.reader.readVarInt()

  // Read data and put it in a BufferReader
  const data = connection.reader.readUnsignedBytes(length)
  const dataReader = new BufferReader(data)

  // Now we can read the Packet ID
  const id = dataReader.readVarInt()

  // And now we can handle the packet
}
```

## Connection State
The connection can be in a few different states:
```ts
export enum ConnectionState {
  Handshaking = 0,
  Status = 1,
  Login = 2,
  Play = 3,
}
```

By default the server is in the `Handshaking` state.

Depending on this state the Packet ID will mean different packets.

The main state is `Play` and contains packets for movement, block place, etc. The rest are used during the login or while getting the server status

Lets add that to our connection:
```ts
class Connection {
  public state: ConnectionState = ConnectionState.Handshaking
}
```

## Sources & References  
- [wiki.vg - Protocol 47](https://wiki.vg/index.php?title=Protocal&oldid=7407)