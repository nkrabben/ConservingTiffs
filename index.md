---
layout: page
title: Problems with TIFF Tags
permalink: /
---

I've had some troublesome TIFFs come across my desk recently that led me to dive deeper into the format spec than I ever thought I would. Thought I might share the results of that dive.

## What is TIFF
The scanner industry developed the Tagged Image File Format (TIFF) in the 80s as a standard language for their equipment. In a scanner, a sensor rakes across an image line-by-line and records strips of color values. Depending on the resolution of the scanner, the orientation of the object being scanned, the colors being scanned, and other factors, the strips may look very different. A TIFF file has to say a few things to be rendered correctly.

1. I'm a TIFF!
2. Tags to interpret my image data
3. My image data

Because TIFF was developed by scanner manufacturers, a single file can contain multiple images just like a document can contain multiple pages. Since each image may have its own tags, TIFF puts the tags for each image into an Image File Directory (IFD).

1. I'm a TIFF!
2. My Tags
   1. Tags to interpret my first image (IFD1)
   2. Tags to interpret my second image (IFD1)
   3. ...
3. My images
   1. Data for my first image
   2. Data for my second image
   3. ...

That overview of TIFF is sufficient for my purposes. If you'd like to go into more depths, read the [Wikipedia article on TIFFs](https://en.wikipedia.org/wiki/Tagged_Image_File_Format), [AWare Systems' TIFF FAQ](http://www.awaresystems.be/imaging/tiff.html), and the [Library of Congress's TIFF Sustainability page](http://www.digitalpreservation.gov/formats/fdd/fdd000022.shtml) or dive into the [formal specification](http://partners.adobe.com/public/developer/en/tiff/TIFF6.pdf).

## Preserving TIFFs
There's a definite hierarchy of importance of the information in a TIFF. The image data is paramount, the tags to interpret that data are secondary, and the format is least important. Maybe it's because of what I've been reading lately, but it reminds me of [Asimov's Three Laws of Robotics](https://en.wikipedia.org/wiki/Three_Laws_of_Robotics). In my terms,

1. A repository may not alter image data or, through inaction, allow image data to be altered.
2. A repository may not alter image tags, except where such tags conflict with the interpretation of image data.
3. A repository may not alter image format, except where such format conflict with the interpretation of image data.

Translating these into common digital preservation strategies we have

1. Bitstream fixity
2. ???
3. Format migration

There's a gap in our strategies. What can we do when a file only needs repair, not wholesale reformatting? Analog materials in libraries, archives, and museums have conservators. What would their [code of ethics](http://www.conservation-us.org/about-us/core-documents/code-of-ethics-and-guidelines-for-practice) look like for digital materials?

## Conserving a TIFF

To make this idea concrete, I'll create, break, and fix some TIFF images. All examples TIFFs are stored in the [GitHub repository](https://github.com/nkrabben/ConservingTiffs/tree/gh-pages/images). Ironically, most browsers don't render TIFFs, so here is a PNG of the TIFF I'm using.
![Base Example]({{ site.github.url }}/images/example.png)

### Toolset
To examine, break, and fix the TIFF, I'll use the following tools.

[tiffdump](http://www.remotesensing.org/libtiff/man/tiffdump.1.html)
: To view TIFF tags, installed by default on OS X
[exiftool](http://www.sno.phy.queensu.ca/~phil/exiftool/)
: To view and manipulate image metadata, installed via homebrew on OS X
[ImageMagick](http://www.imagemagick.org/script/index.php)
: To manipulate image files, installed via homebrew on OS X
[Hex Fiend](http://ridiculousfish.com/hexfiend/)
: To view and edit byte streams, installed via homebrew on OS X

Most of these tools are run via a terminal. I'll use the following syntax for commands and results:

~~~
$ command arugment1 argurment2 etc...
~~~
~~~
output
...
...
~~~

### Our Example TIFF Tags

~~~
$ tiffdump example.tiff
~~~
~~~
example.tiff:
Magic: 0x4d4d <big-endian> Version: 0x2a <ClassicTIFF>
Directory 0: offset 397220 (0x60fa4) next 0 (0)
ImageWidth (256) SHORT (3) 1<419>
ImageLength (257) SHORT (3) 1<237>
BitsPerSample (258) SHORT (3) 4<8 8 8 8>
Compression (259) SHORT (3) 1<1>
Photometric (262) SHORT (3) 1<2>
StripOffsets (273) LONG (4) 4<8 130736 261464 392192>
Orientation (274) SHORT (3) 1<1>
SamplesPerPixel (277) SHORT (3) 1<4>
RowsPerStrip (278) SHORT (3) 1<78>
StripByteCounts (279) LONG (4) 4<130728 130728 130728 5028>
PlanarConfig (284) SHORT (3) 1<1>
ExtraSamples (338) SHORT (3) 1<2>
SampleFormat (339) SHORT (3) 4<1 1 1 1>
ICC Profile (34675) UNDEFINED (7) 3144<00 00 0xc 0x48 0x4c 0x69 0x6e 0x6f 0x2 0x10 00 00 0x6d 0x6e 0x74 0x72 0x52 0x47 0x42 0x20 0x58 0x59 0x5a 0x20 ...>
~~~
