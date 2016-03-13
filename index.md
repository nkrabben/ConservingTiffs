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

I don't want to stay in a land of hypotheticals, so let's make some images.
![Base Example]({{ site.github.url }}/images/example.tiff)
![Base Example]({{ site.github.url }}/images/example.png)
