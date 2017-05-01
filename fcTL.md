## Chunk Sequence Numbers

The `fcTL` and `fdAT` chunks have a 4-byte sequence number. Both chunk types share the sequence. The purpose of this number is to make frames addressable.

The first `fcTL` chunk must contain sequence number 0, and the sequence numbers in the remaining `fcTL` and `fdAT` chunks must be in order, with no gaps or duplicates.

A leading `fcTL` chunk is optional for the `IDAT` chunk(s), but mandatory for any set of `fdAT` chunks.

The tables below illustrates the use of sequence numbers for images with more than one frame and more than one `fdAT` chunk.

| Sequence number | Sequence number | Chunk
|---------------- |---------------- |------
| &mdash;         | 0               | `fcTL` first frame
| _none_          | _none_          | `IDAT` first frame / default image
| 0               | 1               | `fcTL` second frame
| 1               | 2               | first `fdAT` for second frame
| 2               | 3               | second `fdAT` for second frame
| ...             | ...             |

Decoders must treat out-of-order APNG chunks as an error. PNG editors should restore them to correct order using the sequence numbers.

## `fcTL`: The Frame Control Chunk

The `fcTL` chunk is an ancillary chunk as defined in the PNG Specification. It must appear before the `IDAT` or `fdAT` chunks of the frame to which it applies, specifically:

For the default image, if a `fcTL` chunk is present it must appear before the first `IDAT` chunk.
For the first frame excluding the default image (which may be either the first or second frame), the `fcTL` chunk must appear after all `IDAT` chunks and before the `fdAT` chunks for the frame.
For all subsequent frames, the `fcTL` chunk for frame N must appear after the `fdAT` chunks from frame N-1 and before the `fdAT` chunks for frame N.
Other ancillary chunks are allowed to appear among the APNG chunks, including between `fdAT` chunks.
Exactly one `fcTL` chunk is required for each frame.

### Format:

| byte  |                 | length         |
|-----  |---------------- |--------------- |-----
|  0    | sequence_number | (unsigned int) | Sequence number of the animation chunk, starting from 0
|  4    | width           | (unsigned int) | Width of the following frame
|  8    | height          | (unsigned int) | Height of the following frame
| 12    | x_offset        | (unsigned int) | X position at which to render the following frame
| 16    | y_offset        | (unsigned int) | Y position at which to render the following frame
| 20-25 | reserved        |                |
   
The frame must be rendered within the region defined by `x_offset`, `y_offset`, `width`, and `height`. The offsets must be non-negative, the dimensions must be positive, and the region may not fall outside of the default image.

### Constraints on frame regions:

   `x_offset` >= 0
   `y_offset` >= 0
   `width`    > 0
   `height`   > 0
   `x_offset` + `width`  <= `IHDR` width
   `y_offset` + `height` <= `IHDR` height

The `fcTL` chunk corresponding to the default image, if it exists, has these restrictions:

The `x_offset` and `y_offset` fields must be 0.
The `width` and `height` fields must equal the corresponding fields from the `IHDR` chunk.
As noted earlier, the output buffer must be completely initialized to fully transparent black at the beginning of each play. This is to ensure that each play of the animation will be identical. Decoders are free to avoid an explicit clear step as long as the result is guaranteed to be identical. For example, if the default image is included in the animation, and uses a `blend_op` of APNG_BLEND_OP_SOURCE, clearing is not necessary because the entire output buffer will be overwritten.

Note, at the end of the fcTL chunk is a 32-bit CRC checksum. The checksum is calculated using the fcTL chunk and includes the 'fcTL' bytes.

## `fdAT`: The Frame Data Chunk

The `fdAT` chunk has the same purpose as an `IDAT` chunk. It has the same structure as an `IDAT` chunk, except preceded by a sequence number.

At least one `fdAT` chunk is required for each non-initial frame. The compressed datastream is then the concatenation of the contents of the data fields of all the `fdAT` chunks within a frame. When decompressed, the datastream is the complete pixel data of a PNG image, including the filter byte at the beginning of each scanline, similar to the uncompressed data of all the `IDAT` chunks. It utilizes the same bit depth, color type, compression method, filter method, interlace method, and palette (if any) as the default image.

### Format:

| byte |                 | length         |
|----- |---------------- |-------         |-------------
| 0    | sequence_number | (unsigned int) | Sequence number of the animation chunk, starting from 0
| 4    | frame_data      | X bytes        | Frame data for this frame

Each frame inherits every property specified by any critical or ancillary chunks before the first `IDAT` in the file, except the width and height, which come from the `fcTL` chunk.

If the PNG `pHYs` chunk is present, the APNG images and their `x_offset` and `y_offset` values must be scaled in the same way as the main image. Conceptually, such scaling occurs while mapping the output buffer onto the canvas.
