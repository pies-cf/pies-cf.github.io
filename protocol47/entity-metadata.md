# Entity Metadata Packet

1. Write entity id (VarInt)
```ts
writeVarInt(entityId)
```

2. There is now a list of metadata

In the list you have a byte containing the index and type
```ts
// type is first 5 bits, index is remaining bits
writer.writeByte(type >> 5 | (index & 31))
```
- Index: This refers to the type of information being sent. If its a skin layer then the index is 12
- Type: This is the type of data being sent.

3. Now you have written the index and type you need to write the actual data

```ts
    switch (type) {
      case MetadataType.Byte:
        writer.writeByte(value)
        break
    }
```

4. Finally the packet ends with a 127 to say the list is over
```ts
writer.writeByte(127)
```

## Example code

```ts
export enum MetadataIndex {
  SkinLayer = 10
}

export enum MetadataType {
  Byte = 0
}

class Entity {
  // ...
  writeMetadata(writer: BufferWriter, packet: OutEntityMetadataPacket) {
    packet.writeItem(writer, MetadataIndex.SkinLayer, MetadataType.Byte, 127)

    // End of list
    writer.writeByte(127)
  }
}

export class OutEntityMetadataPacket implements Packet {
  // ...
  write(writer: BufferWriter): void {
    writer.writeVarInt(this.entity.eid)
    
    this.entity.writeMetadata(writer, this)
  }
  writeItem(writer: BufferWriter, index: MetadataIndex, type: MetadataType, value: any) {
    writer.writeByte(type >> 5 | (index & 31))

    switch (type) {
      case MetadataType.Byte:
        writer.writeByte(value)
        break
    }
  }
}
```