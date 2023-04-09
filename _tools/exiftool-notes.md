---
robots: norobots
layout: kao_midnite_3
title: ExifTool notes
date: 2022-05-26
update: 2023-04-04
description: "Code snippets and notes-to-self as I explore image metadata with this legendary tool."
lede: print
nav: |  
  + Table of Contents
  + [Useful basics](https://www.zachmccabe.com/tools/exiftool-notes#useful-basics)
  + [Writing directly into video files](https://www.zachmccabe.com/tools/exiftool-notes#writing-directly-into-video-files)
  + [Working with captions, labels and location tags](https://www.zachmccabe.com/tools/exiftool-notes#working-with-captions-labels-and-location-tags)
  + [Writing and copying GPS in video files](https://www.zachmccabe.com/tools/exiftool-notes#writing-and-copying-gps-in-video-files)
  + [Dealing with altitude metadata in video files](https://www.zachmccabe.com/tools/exiftool-notes#dealing-with-altitude-metadata-in-video-files)

---



{% include page_nav.html %}



ExifTool is an app for working with metadata stored in photos, videos and other types of files. It is a useful command line tool, written in Perl. 

Code is creative, like photography, and it's really about imagining what you want to make. To figure out how to put the pieces together, I use the excellent [forum] and [faq.] The documentation page on [XMP sidecar files] is very helpful, too.

Before running commands on my image archive, I use a directory of dummy image files for experiments. Everything included here works for me but that doesn't mean doing things my way is a good way to do things. Your best option is to use the FAQ, listed above :)

[forum]: https://exiftool.org/forum/index.php

[faq.]: https://exiftool.org/faq.html

[XMP sidecar files]: https://exiftool.org/metafiles.html




## Useful basics
### Try this first

Prints all findable metadata for a file called foo.jpg, sorted by group:

<pre>
<code>exiftool -a -u -g1 foo.jpg</code>
</pre>

I use this all the time to see how different cameras and apps write metadata to image files. It's great for verifying what's in the file and for troubleshooting problems.



### Update your software

Checks which version you're running, and shows other helpful details:

<pre>
<code>exiftool -ver -v2</code>
</pre>

To update the app, see the official [install instructions.]

Note: Security researchers [found] a serious vulnerability in older versions of ExifTool. (It's over my head but I believe the greatest risk is to servers?) This was patched in v 12.24. For more on safely coding with ExifTool, and Perl more generally, read this [security note] from the developer. ExifTool is well-maintained. Bugs are fixed and new improvements are added at a steady pace. You'll want to stay up-to-date with the latest version.

[install instructions.]: https://exiftool.org/install.html

[found]: https://twitter.com/wcbowling/status/1393311625709441024?cxt=HHwWgMCq_dPihNYmAAAA

[security note]: https://exiftool.org/index.html#security



### Metadata snapshots by default

Restores your borked metadata for foo.jpg in the current directory:

<pre>
<code>exiftool -restore_original foo.jpg</code>
</pre>

Note: This works because ExifTool automatically takes a metadata snapshot the first time you write to foo.jpg using ExifTool. If you first write a caption using, e.g. Photo Mechanic, and then write over that tag with ExifTool, the snapshot will only go as far back as when ExifTool first wrote the file.


### Disable metadata snapshot

Prevents capture of metadata snapshot when writing to foo.jpg:

<pre>
<code>exiftool -overwrite_original [some flag here] foo.jpg</code>
</pre>





## Writing directly into video files

1. It is safe to write to the XMP block with MP4 and MOV files with ExifTool. UserData and Keys work fine, too.
2. Trying to write to other blocks might cause file corruption!
3. Few apps can read XMP block from within MP4 or MOV files, though. (Watch out for syncing issues and collisions.) XMP sidecar files have wider support.




## Working with captions, labels and location tags

Personally, I use Photo Mechanic or another GUI app to do most of my tagging and organization. There are still situations where I want to use ExifTool to read or write these tags, though. Here are some examples.



### Some ideas for copying from metadata tags into text file

Copies file name and XMP description tag from foo.mp4 to a TXT file with the same name. (If foo.txt already exists, the <code>!</code> operator overwrites the file):

<pre>
<code>exiftool -textOut! txt -FileName -xmp-dc:description foo.mp4</code>
</pre>

Do the same thing for all MP4s in the current directory, writing TXT files to subdirectory foo:

<pre>
<code>exiftool -FileName -xmp-dc:description -textOut! foo/%f.txt -ext mp4 .</code>
</pre>

If you are using ExifTool in the current directory, include a <code>.</code> period at the end of the line. Otherwise, you need to include your <code>path/to/dir</code>.

Note: The default formatting of ExifTool's CSV output works fine for me but I think you can use a flag for binary output or set up a template, if that's what you need.



### Copying from a text file to metadata tag in an image file

Copies text from foo.txt to XMP description tag in bar.jpg:

<pre>
<code>exiftool "-xmp-dc:description<=foo.txt" bar.jpg</code>
</pre>

Copies text from foo.txt to XMP description tag in all JPGs in current directory (writes the same content to all images):

<pre>
<code>exiftool "-xmp-dc:description<=foo.txt" -ext jpg .</code>
</pre>



### Display labels for images

Print file name and label value for any files in current directory that have XMP label tag:

<pre>
<code>exiftool -if '$xmp-xmp:Label' -xmp-xmp:label .</code>
</pre>

This will display both the file name and the color of the label tag. For MacOS, remember to add the single quotes because <code>$</code> sigil is used.



### Searching for files by label

Finds any JPG files in current directory that have Red XMP label, then, for files that match, writes the label and XMP description tags to foo.csv:

<pre>
<code>exiftool -ext jpg -if '$xmp-xmp:label =~ /Red/i' -csv -xmp-xmp:label -xmp-dc:description . >foo.csv</code>
</pre>

Note: The <code>i</code> in <code>/Red/i</code> makes if statement case insensitive. However, Photo Mechanic is picky and only recognizes XMP label if it is capitalized. Use Red, Yellow, Green, Blue, Purple, Cyan, Brown, Trash, None if you're writing to file. I'm not sure about how these values are mapped by other apps like Adobe Bridge, Capture One.



### Searching for files by city

Finds XMP sidecar files in current directory that have XMP city tag, then prepends those tags to the XMP description tag. (I.e. "City...Description". I'm using an ellipses here but format it however you like.):

<pre>
<code>exiftool -ext xmp '-xmp-dc:description<${xmp-iptcext:locationcreatedcity}...${xmp-dc:description}' .</code>
</pre>

For MacOS, remember to add the single quotes because <code>$</code> sigil is used.

There are multiple ways to store the same location info in XMP. (Within the XMP group alone, location tags can be written to xmp-photoshop, xmp-iptccore, xmp-iptcext.)

For JPGs, location tags might also be in EXIF or IPTC groups. Photo Mechanic and some other apps can read and write these tags, and will try to sync all of them. Redundancy is important in metadata because most apps only read a few specific tags. Fiendishly arcane. If you want to use ExifTool to write location tags, you'll want to be specific.




## Writing and copying GPS in video files

### Copy coords from MP4 files to a CSV file

Copies GPS coordinates from all MP4 files in current directory to foo.csv:

<pre>
<code>exiftool -csv -gpslatitude -gpslongitude -gpsaltitude -n -ext mp4 . >foo.csv</code>
</pre>

Note: You can output to CSV or TXT, e.g.: bar.txt instead of the above example. This snippet will also work for other types of image files that have embedded GPS coordinates, like JPGs, MOVs or XMP sidecar files.



### Different metadata blocks for different use cases

If you are going to write geolocation info directly into video files, it can help to write to UserData with MP4s and to Keys for MOV files. Like with all metadata, it really depends on your workflow and what is supported by the apps you use to manage your picture library. (Personally, I either write to XMP directly within the file or write to an XMP sidecar file.)

### Write coords to UserData for MP4

For an MP4 file, try writing GPS coords into UserData:

<pre>
<code>exiftool -userdata:gpscoordinates="45.0536,1.17,187" foo.mp4</code>
</pre>



### Write coords to Keys for MOV

For an MOV file, try writing GPS coords into Keys:

<pre>
<code>exiftool -keys:gpscoordinates="36.0831,-107.9999,1856" bar.mp4</code>
</pre>

UserData and Keys are subgroups of the Quicktime group. Writing to these tags will work with some video files (specifically MP4 and MOV). These tags won't work at all for JPGs or other stills files that don't support the Quicktime metadata block.



### Using ExifTool's handy GPSCoordinates composite tag

The GPSCoordinates tag is a composite tag related to the Quicktime group and, again, won't work on JPGs or other types of files. It's very handy for MP4 and MOV files because it allows you to enter your GPS as an x,y,z string all in the same flag.

- X,Y,Z = latitude, longitude, altitude
- Z = altitude in meters above sea level

Coordinates in the southern or western hemispheres use a minus symbol.

- 9.5566 South, 78.2358 West
- -9.5566, -78.2358

Negative values for elevation work, too, if your location is below sea level. FWIW ExifTool allows you to customize the presentation of how coords are printed to screen. Check the docs.



### Copy coords between metadata blocks

Copies GPS to XMP group directly in foo.mp4:

<pre>
<code>exiftool "-xmp:gpslongitude<gpslongitude" "-xmp:gpslatitude<gpslatitude" "-xmp:gpsaltitude<gpsaltitude" foo.mp4</code>
</pre>



### Copy coords from image file to a sidecar file

Copies GPS from foo.mp4 to an XMP sidecar:

<pre>
<code>exiftool "-xmp:gpslongitude<gpslongitude" "-xmp:gpslatitude<gpslatitude" "-xmp:gpsaltitude<gpsaltitude" -srcfile %d%f.xmp foo.mp4</code>
</pre>

If you have other tags in your file, make sure you copy those over, too. There is an example below.


### Create, copy tags into sidecar file

Creates an XMP sidecar file for foo.mp4, copying any existing XMP tags, and then writes arbitrary GPS coords to that sidecar file (see notes below):

<pre>
<code>exiftool "-all:all<xmp:all" -gpslatitude=22.7679 -gpslongitude=-30.9117 -gpsaltitude=224 -srcfile %d%f.xmp foo.mp4</code>
</pre>

Notes: First, make sure foo.mp4 doesn't have GPS data written somewhere in the original file. If it does, use a different command. Otherwise you'll have conflicts between the host MP4 and the XMP sidecar files. Not good.

Also, if there are no existing XMP tags embedded in foo.mp4, no problem. You'll get a warning and just the GPS coords will be written to sidecar file.

Last, this particular command only copies XMP tags. It does not copy other metadata groups, like UserData. (Check your original files for other metadata and set a flag to copy that over to the sidecar file if you need it.)

For example, this beastly snippet does all of the above, and also copies UserData:description and appends it to the XMP-dc:Description tag. (I.e. "XMP tag content...UserData tag content". I'm using an ellipses here but format it however you like.):

<pre>
<code>exiftool '-all:all<xmp:all' '-xmp-dc:description<${xmp-dc:description}...${userdata:description}' -gpslatitude=54.6761 -gpslongitude=-89.6963 -gpsaltitude=410 -srcfile %d%f.xmp foo.mp4</code>
</pre>

For MacOS, remember to add the single quotes because <code>$</code> sigil is used.



## Dealing with altitude metadata in video files

You can inject an arbitrary altitude into GPS metadata. (Some of the apps I use have trouble with video files that don't include full coords as an x,y,z string. This is useful, for example, for videos shot on Android mobile phones, where altitude isn't captured in MP4 files.)

I hesitate to add arbitrary altitude into the coords string but, in some situations, it is more helpful than not; Nearly all of the gallery apps and image browsers I use will only read location coords from a file if it is a full x,y,z string that is stored in specific metadata blocks. In particular, this has been a problem with MP4 files. There are many potential ways of dealing with this issue, depending on use case.



### You can inject alititude value into a coord string

Injects altitude of 0 meters above sea level into UserData:GPSCoordinates string in foo.mp4, while maintaining the file's existing x,y coordinates:

<pre>
<code>exiftool -ext mp4 '-userdata:gpscoordinates<$gpslatitude,$gpslongitude,0' foo.mp4</code>
</pre>

You can write this snippet, and then write one of the snippets above to copy full x,y,z coords to an XMP sidecar file.



### More ideas for injecting altitude into file metadata

Injects altitude of 9999 meters above sea level into UserData:GPSCoordinates string for all MP4 files in current directory. Then, copies GPS x,y,z coords to XMP sidecar files for all MP4s in current directory. (I'm using a <code>|</code> pipe to string 2 commands together, one after another. There is probably a more efficient way to do this, though.):

<pre>
<code>exiftool -ext mp4 '-userdata:gpscoordinates<$gpslatitude,$gpslongitude,9999' . | exiftool ext -mp4 '-xmp:gpslongitude<gpslongitude' '-xmp:gpslatitude<gpslatitude' '-xmp:gpsaltitude<gpsaltitude' -srcfile %d%f.xmp .</code>
</pre>

If you have other tags embedded in your files, you will need to add flags to copy them over to the XMP sidecar files, too.
