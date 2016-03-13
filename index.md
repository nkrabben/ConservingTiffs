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

## Working with TIFFs

To make these ideas concrete, I'll create, break, and fix some TIFF images. All examples TIFFs are stored in the [GitHub repository](https://github.com/nkrabben/ConservingTiffs/tree/gh-pages/images). Ironically, most browsers don't render TIFFs, so here is a PNG of the TIFF I'm using.

![Base Example]({{ site.github.url }}/images/example.png)

### Toolset
To examine, break, and fix the TIFF, I'll use the following tools.

* [`tiffdump`](http://www.remotesensing.org/libtiff/man/tiffdump.1.html) - To view TIFF tags, installed by default on OS X
* [`exiftool`](http://www.sno.phy.queensu.ca/~phil/exiftool/) - To view and manipulate image metadata, installed via homebrew on OS X
* [`ImageMagick`](http://www.imagemagick.org/script/index.php) - To manipulate image files, installed via homebrew on OS X
* [`cmp`](http://pubs.opengroup.org/onlinepubs/9699919799/utilities/cmp.html) - To compare file byte streams, installed by default on OS X
* [Hex Fiend](http://ridiculousfish.com/hexfiend/) - To view and edit byte streams, installed via homebrew on OS X

Most of these tools are run via a terminal. I'll use the following syntax for commands and results:

~~~
$ command arugment1 argurment2 etc...
~~~
~~~
output
...
...
~~~

### Looking at TIFF Tags
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

Let's walk through `tiffdump`'s output.
* Line 1 - file name
* Line 2 - file format information, the "I'm a TIFF!"
* Line 3 - How many bytes until the first Image File Directory (IFD) begins and how many bytes until the next IFD. This file only has one image and IFD.
* Line 4-14 - Each tag in the IFD in the following format: TagName (TagIDNumber) DatatypeOfTagValue (DatatypeIDNumber) NumberOfValues<Values>

The tags for this file only describe how to interpret the image data, but tags can contain other information. For example, if I open `example.tiff` with image editing software and save it as a new file, the tags now include the name of the software.

~~~
$ tiffdump example_seashore.tiff
~~~
~~~
example_seashore.tiff:
...
Software (305) ASCII (2) 15<Seashore 0.1.9\0>
...
~~~

A new software name is not the only change that's happened. Looking at the full output shows a disturbing amount of changes.

~~~
example_seashore.tiff:
Magic: 0x4949 <little-endian> Version: 0x2a <ClassicTIFF>
Directory 0: offset 9910 (0x26b6) next 0 (0)
ImageWidth (256) SHORT (3) 1<419>
ImageLength (257) SHORT (3) 1<237>
BitsPerSample (258) SHORT (3) 4<8 8 8 8>
Compression (259) SHORT (3) 1<5>
Photometric (262) SHORT (3) 1<2>
StripOffsets (273) LONG (4) 48<8 156 304 452 600 748 896 1044 1192 1340 1488 1636 1911 2174 2437 2694 2940 3186 3432 3678 3924 4170 4416 4672 ...>
Orientation (274) SHORT (3) 1<1>
SamplesPerPixel (277) SHORT (3) 1<4>
RowsPerStrip (278) SHORT (3) 1<5>
StripByteCounts (279) LONG (4) 48<148 148 148 148 148 148 148 148 148 148 148 275 263 263 257 246 246 246 246 246 246 246 256 255 ...>
XResolution (282) RATIONAL (5) 1<72>
YResolution (283) RATIONAL (5) 1<72>
PlanarConfig (284) SHORT (3) 1<1>
ResolutionUnit (296) SHORT (3) 1<2>
Software (305) ASCII (2) 15<Seashore 0.1.9\0>
Predictor (317) SHORT (3) 1<2>
ICC Profile (34675) UNDEFINED (7) 3892<00 00 0xf 0x34 0x61 0x70 0x70 0x6c 0x2 0x10 00 00 0x6d 0x6e 0x74 0x72 0x52 0x47 0x42 0x20 0x58 0x59 0x5a 0x20 ...>
~~~

These changes include:
* Line 2 - The file is now a little-endian TIFF instead of a big-endian TIFF
* Line 7 - The compression has changed from 1 (no compression) to 5 (LZW compression). The [AWare Systems Tag Reference](http://www.awaresystems.be/imaging/tiff/tifftags/baseline.html) is invaluable for translating these values.
* Lines 9, 12, and 13 - The strips of image data have been chopped up from 3 long streams into 48 shorter streams.

Is this still the same image? Using ImageMagick's `compare` tool, it's possible to see if any pixels have changed. The peak absolute error metric returns the greatest color difference between any two pixels. Identical images should return 0.

~~~
$ compare -metric pae example.tiff example_seashore.tiff :null
~~~
~~~
0 (0)%
~~~

Visually, these are still the same image, but from a preservation point of view, we've performed a format migration. Typically, a repository shouldn't re-save its images. But what if the file is a broken or invalid?

## Conserving a TIFF

In the middle of a stream of TIFFs produced by a digitization project, seemingly random files began failing on ingest. Our process creates derivative access images during ingest. When it failed, we logged the following error.
~~~
convert: example_broken.tiff: Bad value 0 for "Orientation" tag. `_TIFFVSetField' @ error/tiff.c/TIFFErrors/557.
~~~
(Here, example_broken.tiff is a copy of example.tiff that I've purposefully broken.)

The value of the Orientation tag describes if the image data should be rotated, mirrored, or rotated and mirrored. It's value can only be 1-8. When our ingest process tries to create derivatives of images with invalid orientation values, it purposely fails. If it doesn't know the orientation of the original image, it will not know how to orient the derivatives.

It's still not clear what caused this error. It's tempting to tell everyone that we have a real example of bit-flipping, but the same error appears in tens (out of thousands) of files. My working hypothesis is that the scanner software has a bug or a bad implementation of TIFF.

Regardless, we would like to fix these images, or rather, we would like to conserve these images. We could create visually identical TIFFs by opening and saving them in an image editor, but that would be akin to repairing a torn parchment page in a book by replacing it with a photocopy on modern paper. I'd prefer to surgically edit the single broken value.

One option to edit only the metadata is `exiftool`.
~~~
$ exiftool -Orientation=x01 -n example_broken.tiff -o example_broken_exiftool.tiff
~~~
We can see that we've fixed the tag by comparing the results of `tiffdump` for each file, but if we compare the byte stream of the repaired file with the unbroken file byte streams with `cmp`, it's not the same.
~~~
$ cmp example.tiff example_broken_exiftool.tiff
~~~
~~~
example.tiff example_broken_exiftool.tiff differ: char 6, line 1
~~~
In addition to writing the metadata, `exiftool` is rewriting other parts of the data in the file. These rewrites are not always predictable. In my tests, I discovered one file where `exiftool` added a space into the value of the software tag, ie. "Seashore  0.1.9" instead of "Seashore 0.1.9". That example is both harmless and concerning.

I haven't found any other tools that perform the surgical edit that I want, so I dove into the byte stream itself with a hex editor. 





change completely. in the image editor
