#### 11.3.5.5 `aPLT` Alternative palette

The four-byte chunk type field contains the decimal values

    97 80 76 84

The `aPLT` chunk contains:

| Palette name	| 1-79 bytes (character string)	|
|-------------	|-----------	|
| Null separator	| 1 byte (null character)	|
| Sample depth	| 1 byte	|
| Red  	| 1 or 2 bytes	|
| Green	| 1 or 2 bytes	|
| Blue 	| 1 or 2 bytes	|
| Alpha	| 1 or 2 bytes	|
| ...etc...	 | 	|

Each palette entry is six bytes or ten bytes containing five unsigned integers (red, blue, green, alpha, and frequency).

There may be any number of entries. A PNG decoder determines the number of entries from the length of the chunk remaining after the sample depth byte. This shall be divisible by 6 if the `aPLT` sample depth is 8, or by 10 if the `aPLT` sample depth is 16. Entries shall appear in decreasing order of frequency. There is no requirement that the entries all be used by the image, nor that they all be different.

The palette name can be any convenient name for referring to the palette (for example "256 colour including Macintosh default", "256 colour including Windows-3.1 default", "Optimal 512"). The palette name may aid the choice of the appropriate suggested palette when more than one appears in a PNG datastream.

The palette name is case-sensitive, and subject to the same restrictions as the keyword parameter for the `tEXt` chunk. Palette names shall contain only printable Latin-1 characters and spaces (only character codes 32-126 and 161-255 decimal are allowed). Leading, trailing, and consecutive spaces are not permitted.

The `aPLT` sample depth shall be 8 or 16.

The red, green, blue, and alpha samples are either one or two bytes each, depending on the `aPLT` sample depth, regardless of the image bit depth. The colour samples are not premultiplied by alpha, nor are they precomposited against any background. An alpha value of 0 means fully transparent. An alpha value of 255 (when the `aPLT` sample depth is 8) or 65535 (when the `aPLT` sample depth is 16) means fully opaque. The `aPLT` chunk may appear for any PNG colour type. Entries in `aPLT` use the same gamma and chromaticity values as the PNG image, but may fall outside the range of values used in the colour space of the PNG image; for example, in a greyscale PNG image, each `aPLT` entry would typically have equal red, green, and blue values, but this is not required. Similarly, `aPLT` entries can have non-opaque alpha values even when the PNG image does not use transparency.

Multiple `aPLT` chunks are permitted, but each must have a different palette name. They should be different from any `sPLT` palette names as well.

----

## 12.12 Alternative palettes

Alternative palettes may appear as `aPLT` chunks in any PNG datastream. Alternative palettes form an essential part of the image data.

For indexed-colour images, `aPLT` can be used to define alternative palettes for the default `PLTE` chunk. The intention of this is to provide a simple method to display differently coloured variants of the same image which is frequently needed in sprite files.

With `aPLT`, multiple alternative palettes may be provided. A PNG decoder should choose an appropriate palette based on the name or external environment parameters, e.g. from fragment identifiers provided in URLs like `wine-glass.png#palette=Rosé`.
