## Chunk Sequence Numbers

The `fcTL` and `fdAT` chunks have a 4-byte sequence number. Both chunk types share the sequence. The purpose of this number is to make fragments addressable, e.g. from fragment identifiers in URLs like `foo.png#2`.

The first `fcTL` chunk must contain sequence number 0, and the sequence numbers in the remaining `fcTL` and `fdAT` chunks must be in order, with no gaps or duplicates.

A leading `fcTL` chunk is optional for the `IDAT` chunk(s), but mandatory for any set of `fdAT` chunks.

The tables below illustrates the use of sequence numbers for images with more than one frame and more than one `fdAT` chunk.

| Sequence number | Sequence number | Chunk
|---------------- |---------------- |------
| &mdash;         | 0               | `fcTL` first fragment
| _none_          | _none_          | `IDAT` first fragment / default image
| 0               | 1               | `fcTL` second fragment
| 1               | 2               | first `fdAT` for second fragment
| 2               | 3               | second `fdAT` for second fragment
| 3               | 4               | `fcTL` third fragment
| 4               | 5               | first `fdAT` for thirdd fragment
| ...             | ...             |

Decoders must treat out-of-order APNG chunks as an error. PNG editors should restore them to correct order using the sequence numbers.

## `fcTL`: The Fragment Control Chunk

The `fcTL` chunk is an ancillary chunk as defined in the PNG Specification. It must appear before the `IDAT` or `fdAT` chunks of the fragment to which it applies, specifically:

For the default image, if a `fcTL` chunk is present it must appear before the first `IDAT` chunk.
For the first fragment excluding the default image (which may be either the first or second fragment), the `fcTL` chunk must appear after all `IDAT` chunks and before the `fdAT` chunks for the fragment.
For all subsequent fragments, the `fcTL` chunk for fragment _N_ must appear after the `fdAT` chunks from fragment _N-1_ and before the `fdAT` chunks for fragment _N_.
Other ancillary chunks are allowed to appear among the PNG chunks, including between `fdAT` chunks.
Exactly one `fcTL` chunk is required for each fragment.

### Format

| byte  | name              | length         | description
|-----  |------------------ |--------------- |------------
|  0    | `sequence_number` | (unsigned int) | Sequence number of the fragment chunk, starting from 0
|  4    | `width`           | (unsigned int) | Width of the following fragment
|  8    | `height`          | (unsigned int) | Height of the following fragment
| 12    | `x_offset`        | (unsigned int) | X position at which to render the following fragment
| 16    | `y_offset`        | (unsigned int) | Y position at which to render the following fragment
| 20-25 | _reserved_        |                |

The fragment must be rendered within the region defined by `x_offset`, `y_offset`, `width`, and `height`. The offsets must be non-negative, the dimensions must be positive, and the region may not fall outside of the default image.

### Constraints on frame regions:

 - `x_offset` ≥ 0
 - `y_offset` ≥ 0
 - `width`    > 0
 - `height`   > 0
 - `x_offset` + `width`  ≤ `IHDR` width
 - `y_offset` + `height` ≤ `IHDR` height

The `fcTL` chunk corresponding to the default image, if it exists, has these restrictions:

- The `x_offset` and `y_offset` fields must be 0.
- The `width` and `height` fields must equal the corresponding fields from the `IHDR` chunk.

Note, at the end of the `fcTL` chunk is a 32-bit CRC checksum. The checksum is calculated using the `fcTL` chunk and includes the 'fcTL' bytes.

## `fdAT`: The Fragment Data Chunk

The `fdAT` chunk has the same purpose as an `IDAT` chunk. It has the same structure as an `IDAT` chunk, except preceded by a sequence number.

At least one `fdAT` chunk is required for each non-initial fragment. The compressed datastream is then the concatenation of the contents of the data fields of all the `fdAT` chunks within a fragment. When decompressed, the datastream is the complete pixel data of a PNG image, including the filter byte at the beginning of each scanline, similar to the uncompressed data of all the `IDAT` chunks. It utilizes the same bit depth, color type, compression method, filter method, interlace method, and palette (if any) as the default image.

### Format

| byte | name              | length         | description
|----- |------------------ |--------------- |------------
| 0    | `sequence_number` | (unsigned int) | Sequence number of the fragment chunk, starting from 0
| 4    | `fragment_data`   | _X_ bytes      | Fragment data for this fragment

Each fragment inherits every property specified by any critical or ancillary chunks before the first `IDAT` in the file, except the width and height, which come from the `fcTL` chunk.

If the PNG `pHYs` chunk is present, the PNG images and their `x_offset` and `y_offset` values must be scaled in the same way as the main image. Conceptually, such scaling occurs while mapping the output buffer onto the canvas.

## `fMAP`: The Fragment Name Mapping Chunk

| byte | name              | length                        | description
|----- |------------------ |---------------                |------------
| 0    | `sequence_number` | (unsigned int)                | Sequence number of the corresponding `fcTL` chunk
| 4    | `fragment_name`   | 1-79 bytes (character string) | Fragment data for this fragment

...
