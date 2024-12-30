Link: https://gist.github.com/TheVeryStarlk/fc2f3067818775c591c4ebb825b0e00c \
Author: TheVeryStarlk\
Saved for backups

This article explains how chunk packets work in Minecraft 1.8 (PVN 47). I was motivated to write this due to the lack of easily understandable documentation for this version of Minecraft. I drew inspiration from the Wiki's [article](https://wiki.vg/index.php?title=Chunk_Format&oldid=7164). This article could have not been possible without the help of the members of the Minecraft Protocol Discord server.

## Terminology

* Section: A 16x16x16 collection of blocks, including block light and skylight.
* Chunk: A vertical stack of 16 sections.

## Packets

* 0x21: Used to send a single chunk column.
* 0x26: Used to send multiple chunk columns.

> Note: Sections may not store skylight, and that depends on the packet's fields.

## Bitmask

Both packets contain an unsigned short field that is used to determine which sections contain air and which do not. Each bit of the two-byte unsigned short represents a section, with the least significant bit representing a section from Y = 0 to Y = 15.

## Format

Each section contains a 16x16x16 collection of blocks, resulting in an 8192-byte array of block types and metadata. The block light and skylight nibble arrays each occupy 2048 bytes. Thus, each section requires 8192 + 2048 + 2048 bytes.

| Section      | Size (bytes)                  |
|--------------|-------------------------------|
| Blocks       | 8192 (for types and metadata) |
| Blocks light | 2048                          |
| Sky light    | 2048                          |

A chunk stores sections and biomes, so its size is calculated as sections + 256 (for biomes). However, the way sections are stored in a chunk is somewhat different than what you might think. First, all of the chunk sections’ blocks are stored, followed by the sections’ block light and finally the sections’ skylight. The format is as follows: 8192 * section count + 2048 * section count + 2048 * section count + 256 (for biomes).

| Chunk                | Size (bytes)         |
|----------------------|----------------------|
| Section blocks       | 8192 * section count |
| Section blocks light | 2048 * section count |
| Section sky light    | 2048 * section count |
| Biomes               | 256                  |

## Storage

* Storing blocks: Each block entry in a section occupies two bytes. The first byte contains `((type << 4) | metadata)` while the second byte contains the remainder `(type >> 4)`. Block light and skylight are stored as nibble arrays, so each byte stores the value of two blocks.

* Positions: To determine the index from a block’s position, use `y << 8 | z << 4 | x`. The values cannot exceed 15, so they range from 0 to 15.

## Implementations

[Vrganj's](https://paste.md-5.net/lawedohusu.go),
[GoPractice](https://github.com/PondWader/GoPractice/blob/master/server/world/chunk.go),
[Prismarine Chunk](https://github.com/PrismarineJS/prismarine-chunk/tree/master/src/pc/1.8),
[Glowstone](https://github.com/GlowstoneMC/Glowstone/blob/d3ed79ea7d284df1d2cd1945bf53d5652962a34f/src/main/java/net/glowstone/GlowChunk.java#L640).