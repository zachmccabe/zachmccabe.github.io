---
robots: norobots
layout: kaoliang_nite_3
title: ExifTool notes
date: 2022-05-20
description: "Snippets I use and notes on what I'm learning about image metadata."
lede: print
nav: |
  + [Useful basics](https://www.zachmccabe.com/tools/exiftool#)
  + [Writing directly into video files](https://www.zachmccabe.com/tools/exiftool#writing-directly-into-video-files)
  + [Working with captions, labels and location tags](https://www.zachmccabe.com/tools/exiftool#working-with-captions-labels-and-location-tags)
  + [Writing and copying GPS in video files](https://www.zachmccabe.com/tools/exiftool#writing-and-copying-GPS-in-video-files)
  + [Inject altitude into GPS coords string in an MP4](https://www.zachmccabe.com/tools/exiftool#inject-altitude-into-gps-coords-string-in-an-mp4)

---



{% include page_nav.html %}




ExifTool is command line app for working with metadata stored in photos, videos and other types of files. It is legendary and incredibly useful.

While I manage most of my captioning and other metadata using an app called Photo Mechanic, ExifTool still comes in handy for all sorts of tasks.

I use a directory of dummy image files for experiments, and am learning from the excellent [forum] and [faq.] This documentation page on [XMP sidecar files] is helpful, too.

The app is cross-platform but some syntax varies by platform. I'm using MacOS. Tweak accordingly. All the snippets included here work for me but that doesn't mean doing things my way is a good way to do things. You've been warned :)

Okay, let's go!

[forum]: https://exiftool.org/forum/index.php

[faq.]: https://exiftool.org/faq.html

[XMP sidecar files]: https://exiftool.org/metafiles.html




## Useful basics

Displays the metadata in a file:

<pre><code>
exiftool -a -u -g1 foo.jpg
</code></pre>

I use this all the time to see how different cameras and apps write metadata to image files. It's great for verifying what's in the file and for troubleshooting problems. It might be all you need.

---

Checks which version you're running, and shows other helpful details:

<pre><code>
exiftool -ver -v2
</code></pre>

Note: Security researchers [found] a serious security vulnerability in older versions of ExifTool. (It's over my head but I believe the greatest risk is to servers?) This was patched in v 12.24. ExifTool is well-maintained, and has been for a long time. Bugs are fixed and new improvements are added at a steady pace. You'll want to stay up-to-date with the latest version.

To update the app, see the official [install instructions.]

[found]: https://twitter.com/wcbowling/status/1393311625709441024?cxt=HHwWgMCq_dPihNYmAAAA

[install instructions.]: https://exiftool.org/install.html

---

Restores your borked metadata for <code>foo.jpg</code> in the current directory:

<pre><code>
exiftool -restore_original foo.jpg
</code></pre>

Note: This works because ExifTool automatically takes a metadata snapshot the first time you write to <code>foo.jpg</code> using ExifTool. If you first write a caption using, e.g. Photo Mechanic, and then write over that tag with ExifTool, the snapshot will only go as far back as when ExifTool first wrote the file.

Prevents capture of metadata snapshot when writing to <code>foo.jpg</code>:

<pre><code>
exiftool -overwrite_original [some flag here] foo.jpg
</code></pre>





## Writing directly into video files

1. It is safe to write to the XMP block with MP4 and MOV files with ExifTool. <code>UserData</code> and <code>Keys</code> work fine, too.
2. Trying to write to other blocks might cause file corruption!
3. Few apps can read XMP block from within MP4 or MOV files, though. (Watch out for syncing issues and collisions.) XMP sidecar files have wider support.





## Working with captions, labels and location tags

Personally, I use Photo Mechanic or another GUI app to do most of my tagging and organization. There are still situations where I want to use ExifTool to read or write these tags, though. Here are some examples.

---

Copies file name and XMP description tag from <code>foo.mp4</code> to a TXT file with the same name. (If <code>foo.txt</code> already exists, the <code>!</code> operator overwrites the file):

<pre><code>
exiftool -textOut! txt -FileName -xmp-dc:description foo.mp4
</code></pre>

Do the same thing for all MP4s in the current directory, writing TXT files to subdirectory <code>foo</code>:

<pre><code>
exiftool -FileName -xmp-dc:description -textOut! foo/%f.txt -ext mp4 .
</code></pre>

If you are using ExifTool in the current directory, include a <code>.</code> period at the end of the line. Otherwise, you need to include your <code>path/to/dir</code>.

Note: The default formatting of ExifTool's CSV output works fine for me but I think you can use a flag for binary output or set up a template, if that's what you need.

---

Copies text from <code>foo.txt</code> to XMP description tag in <code>bar.jpg</code>:

<pre><code>
exiftool "-xmp-dc:description<=foo.txt" bar.jpg
</code></pre>

Copies text from <code>foo.txt</code> to XMP description tag in all JPGs in current directory (writes the same content to all images):

<pre><code>
exiftool "-xmp-dc:description<=foo.txt" -ext jpg .
</code></pre>

---

Displays any files in current directory that have XMP label tag:

<pre><code>
exiftool -if '$XMP-xmp:Label' -XMP-xmp:label .
</code></pre>

This will display both the file name and the color of the label tag. For MacOS, remember to add the single quotes because <code>$</code> sigil is used.

---

Finds any JPG files in current directory that have Red XMP label, then, for files that match, writes the label and XMP description tags to <code>foo.csv</code>:

<pre><code>
exiftool -ext jpg -if '$xmp-xmp:label =~ /Red/i' -csv -xmp-xmp:label -xmp-dc:description . >foo.csv
</code></pre>

Note: The <code>i</code> in <code>/Red/i</code> makes if statement case insensitive. However, Photo Mechanic is picky and only recognizes XMP label if it is capitalized. Use Red, Yellow, Green, Blue, Purple, Cyan, Brown, Trash, None if you're writing to file. I'm not sure about other apps like Bridge, C1.

---

Finds XMP sidecar files in current directory that have XMP city tag, then prepends those tags to the XMP description tag. (I.e. "City...Description". I'm using an ellipses here but format it however you like.):

<pre><code>
exiftool -ext xmp '-xmp-dc:description<${xmp-iptcext:locationcreatedcity}...${xmp-dc:description}' .
</code></pre>

For MacOS, remember to add the single quotes because <code>$</code> sigil is used.

There are multiple ways to store the same location info in XMP. (Within the XMP group alone, location tags can be written to <code>xmp-photoshop</code>, <code>xmp-iptccore</code>, <code>xmp-iptcext</code>.)

For JPGs, location tags might also be in EXIF or IPTC groups. Photo Mechanic and some other apps can read and write these tags, and will try to sync all of them. Redundancy is important in metadata because most apps only read a few specific tags. Fiendishly arcane. If you want to use ExifTool to write location tags, you'll need to be specific. Here is a table of example location tags from just the XMP group:

<table class="kaoStripey">
 <tr>
 <th scope="row" colspan="2">XMP-photoshop
 </th>
 </tr>
 <tr>
  <td>City
  </td>
  <td>Timbuktu
  </td>
 </tr>
 <tr>
  <td>State
  </td>
  <td>Timbuktu
  </td>
 </tr>
 <tr>
  <td>Country
  </td>
  <td>Mali
  </td>
 </tr>
</table>

<table class="kaoStripey">
 <tr>
  <th scope="row" colspan="2">XMP-iptcCore
  </th>
 </tr>
 <tr>
  <td>Country Code
  </td>
  <td>MLI
  </td>
 </tr>
 <tr>
  <td>Location
  </td>
  <td>Rue de Chemnitz
  </td>
 </tr>
</table>

<table class="kaoStripey">
 <tr>
  <th scope="row" colspan="2">XMP-iptcExt
  </th>
 </tr>
 <tr>
  <td>Location Created Sublocation
  </td>
  <td>Rue de Chemnitz
  </td>
 </tr>
 <tr>
  <td>Location Created City
  </td>
  <td>Timbuktu
  </td>
 </tr>
 <tr>
  <td>Location Created Province State
  </td>
  <td>Timbuktu
  </td>
 </tr>
 <tr>
  <td>Location Created Country Name
  </td>
  <td>Mali
  </td>
 </tr>
 <tr>
  <td>Location Created Country Code
  </td>
  <td>MLI
  </td>
 </tr>
</table>





## Writing and copying GPS in video files

Copies GPS coordinates from all MP4 files in current directory to <code>foo.csv</code>:

<pre><code>
exiftool -csv -gpslatitude -gpslongitude -gpsaltitude -n -ext mp4 . >foo.csv
</code></pre>

Note: You can output to CSV or TXT, e.g.: <code>bar.txt</code> instead of the above example. This snippet will also work for other types of image files that have embedded GPS coordinates, like JPGs, MOVs or XMP sidecar files.

---

If you are going to write geolocation info directly into video files, it can help to write to <code>UserData</code> with MP4s and to <code>Keys</code> for MOV files. Like with all metadata, it really depends on your workflow and what is supported by the apps you use to manage your picture library. (Personally, I either write to <code>XMP</code> directly within the file or write to an XMP sidecar file.)

For an MP4 file, try writing GPS coords into <code>UserData</code>:

<pre><code>
exiftool -UserData:GPSCoordinates="16.7665,-3.0025,261" foo.mp4
</code></pre>

For an MOV file, try writing GPS coords into <code>Keys</code>:

<pre><code>
exiftool -Keys:GPSCoordinates="16.7665,-3.0025,261" bar.mp4
</code></pre>

UserData and Keys are subgroups of the Quicktime group. Writing to these tags will work with some video files (specifically MP4 and MOV). These tags won't work at all for JPGs or other stills files that don't support the Quicktime metadata block.

The <code>GPSCoordinates</code> tag is a composite tag related to the related to the Quicktime group and, again, won't work on JPGs or other types of files. It's very handy for MP4 and MOV files because it allows you to enter your GPS as an x,y,z string all in the same flag.

X,Y,Z = latitude, longitude, altitude.

Z = altitude in meters above sea level. Negative altitude works, too, if you're in Death Valley or somewhere real exciting.

For coordinates in the southern or western hemisphere, you can either use <code>-</code> negative symbol before your coordinate, or use <code>S</code> or <code>W</code> after your coordinate. E.g.:

<pre><code>
GALAPAGOS ISLANDS
0.5S,90.5W
-0.5,-90.5
<code></pre>

Heads up! This syntax does depend on what commands you're using to write coords to file as well as if you're storing the coords in UserData or XMP; it also depends on file format. If you're writing to JPGs you will need to use a different command.

---

Copies GPS to XMP group directly in <code>foo.mp4</code>:

<pre><code>
exiftool "-xmp:GPSLongitude<GPSLongitude" "-xmp:GPSLatitude<GPSLatitude" "-xmp:GPSAltitude<GPSAltitude" foo.mp4
</code></pre>

---

Copies GPS from <code>foo.mp4</code> to an XMP sidecar:

<pre><code>
exiftool "-xmp:GPSLongitude<GPSLongitude" "-xmp:GPSLatitude<GPSLatitude" "-xmp:GPSAltitude<GPSAltitude" -srcfile %d%f.xmp foo.mp4
</code></pre>

If you have other tags in your file, make sure you copy those over, too. There is an example below.

---

Creates an XMP sidecar file for <code>foo.mp4</code>, copying any existing XMP tags, and then writes arbitrary GPS coords to that sidecar file (see notes below):

<pre><code>
exiftool "-all:all<xmp:all" -GPSLatitude=16.7665 -GPSLongitude=-3.0025 -GPSAltitude=261 -srcfile %d%f.xmp foo.mp4
</code></pre>

Notes: First, make sure <code>foo.mp4</code> doesn't have GPS data written somewhere in the original file. If it does, use a different command. Otherwise you'll have conflicts between the host MP4 and the XMP sidecar files. Not good.

Also, if there are no existing XMP tags embedded in <code>foo.mp4</code>, no problem. You'll get a warning and just the GPS coords will be written to sidecar file.

Last, this particular command only copies XMP tags. It does not copy other metadata groups, like UserData. (Check your original files for other metadata and set a flag to copy that over to the sidecar file if you need it.)

For example, this beastly snippet does all of the above, and also copies <code>UserData:description</code> and appends it to <code>xmp-dc:description</code> tag. (I.e. "XMP tag content...UserData tag content". I'm using an ellipses here but format it however you like.):

<pre><code>
exiftool '-all:all<xmp:all' '-xmp-dc:description<${xmp-dc:description}...${userdata:description}' -GPSLatitude=16.7665 -GPSLongitude=-3.0025 -GPSAltitude=261 -srcfile %d%f.xmp foo.mp4
</code></pre>

For MacOS, remember to add the single quotes because <code>$</code> sigil is used.





## Inject altitude into GPS coords string in an MP4

You can inject an arbitrary altitude into GPS metadata. (Some of the apps I use have trouble with video files that don't include full coords as an x,y,z string. This is useful, for example, for videos shot on Android mobile phones, where altitude isn't captured in MP4 files.)

I hesitate to add arbitrary altitude into coords string but, in some situations, it is more helpful than not.

Injects altitude of 0 meters above sea level into <code>UserData:GPSCoordinates</code> string in <code>foo.mp4</code>, while maintaining the file's existing x,y coordinates:

<pre><code>
exiftool -ext mp4 '-UserData:GPSCoordinates<$GPSLatitude,$GPSLongitude,0' foo.mp4
</code></pre>

You can write this snippet, and then write one of the snippets above to copy full x,y,z coords to an XMP sidecar file.

---

Injects altitude of 9999 meters above sea level into <code>UserData:GPSCoordinates</code> string for all MP4 files in current directory. Then, copies GPS x,y,z coords to XMP sidecar files for all MP4s in current directory. (I'm using a <code>|</code> pipe to string 2 commands together, one after another. There is probably a more efficient way to do this, though.):

<pre><code>
exiftool -ext mp4 '-UserData:GPSCoordinates<$GPSLatitude,$GPSLongitude,9999' . | exiftool ext -mp4 '-xmp:GPSLongitude<GPSLongitude' '-xmp:GPSLatitude<GPSLatitude' '-xmp:GPSAltitude<GPSAltitude' -srcfile %d%f.xmp .
</code></pre>

If you have other tags embedded in your files, you will need to add flags to copy them over to the XMP sidecar files, too.
