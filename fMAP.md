`fmAP`: The Fragment Name Mapping Chunk
---------------------------------------

| byte | name              | length                        | description
|----- |------------------ |---------------                |------------
| 0    | `sequence_number` | (unsigned int)                | Sequence number of the corresponding `fcTL` chunk
| 4    | `language`        | 2-16 bytes (character string) | BCP-47 language code for picture content and `fragment_name`
| 6-20 | `fragment_name`   | 1-79 bytes (character string) | Fragment data for this fragment

The `sequence_number` matches the equivalent property in `fdAT` and (preferably) `fcTL` chunks. An empty or zero sequence number refers to `IDAT`.

Issue: This does not support named sprites in planar maps yet, i.e. aliases for spatial [Media Fragments](https://www.w3.org/TR/media-frags/). They need either pixel coordinates (_xₐ_, _yₐ_) and dimensions (_wₐ_, _hₐ_) or column width (_w_), row height (_h_) and cell index (_cₐ_ and _rₐ_ or just _i_). This use case may instead be better addressed by reusing (a subset of) MNG's [`eXPI` chunk](http://www.libpng.org/pub/mng/spec/#mng-eXPI) in PNG.
