# Proposals for extending the (A)PNG specification

- W3C PNG 1.2 (2003-11-10): [Portable Network Graphics (PNG) Specification (Second Edition)](https://www.w3.org/TR/PNG/) = International Standard ISO/IEC 15948:2003 -- Portable Network Graphics (PNG): Functional specification
  * `IDAT`, `IEND`, `IHDR`, `PLTE`
  * `bKGD`, `cHRM`, `gAMA`, `hIST`, `iCCP`, `iTXt`, `pHYs`, `sBIT`, `sPLT`, `sRGB`, `tEXt`, `tRNS`, `zTXt`
  - Don't bother with the [registration info](https://www.w3.org/TR/PNG/#4Concepts.Registration) provided in the W3C/ISO/IEC standard. [png-group@w3.org](https://lists.w3.org/Archives/Public/png-group/) is basically defunct and always has been. Proposals for new chunks or keywords effectively must be run directly through the PNG group mailing list.

- [PNG Extension Registry](http://www.libpng.org/pub/png/spec/register/)
  * `fRAc`, `gIFg`, `gIFt`, `gIFx`, `oFFs`, `sCAL` 1995-03-07 PNGEXT 1.2.0
  * `pCAL` 1997-01-28 PNGEXT 1.2.0
  * `sTER` 2006-04-15 PNGEXT 1.3.0
  * `dSIG` 2008-05-01 PNGEXT 1.4.0
  - The PNG Group has a long history of voting down almost every proposal for extension, often for technicalities or on principle grounds.

- [PNG mailing list archive at Sourceforge](https://sourceforge.net/p/png-mng/mailman/png-mng-misc/) (incredibly user-hostile, like almost everything on SF.net)

- [PNG Group document repository (FTP)](ftp://ftp.simplesystems.org/pub/png-group/) (often slow as fuck)

- [Animated PNG 1.0](https://wiki.mozilla.org/APNG_Specification) currently hosted at Mozilla, hopefully soon being moved to WhatWG or W3C, not endorsed by PNG Group
  * `acTL`: Animation control chunk
  * `fcTL`: Frame control chunk
  * `fdAT`: Frame data chunk

<!--
People
------

- Glenn Randers-Pehrson  @glennrp (libpng)
- Stuart Parmenter @stuartparmenter? pavlov@pavlov.net

- John Bowler @jbowler

- Pavel Zlatovratskii @Scondo

- Vladimir Vukicevic @vvuk vladimir@pobox.com
- Andrew Smith asmith16@littlesvr.ca
- Chris Lilley @svgeesus (W3C)

Projects
--------

- https://github.com/apngasm/apngasm (Max Stepin @maxstepin, 影月 零 @Kagetsuki)

-->
