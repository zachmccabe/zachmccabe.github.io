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

<pre>
<code>exiftool -a -u -g1 foo.jpg</code>
</pre>

I use this all the time to see how different cameras and apps write metadata to image files. It's great for verifying what's in the file and for troubleshooting problems. It might be all you need.

---

Checks which version you're running, and shows other helpful details:

<pre>
<code>exiftool -ver -v2</code>
</pre>

Note: Security researchers [found] a serious security vulnerability in older versions of ExifTool. (It's over my head but I believe the greatest risk is to servers?) This was patched in v 12.24. ExifTool is well-maintained, and has been for a long time. Bugs are fixed and new improvements are added at a steady pace. You'll want to stay up-to-date with the latest version.

To update the app, see the official [install instructions.]

[found]: https://twitter.com/wcbowling/status/1393311625709441024?cxt=HHwWgMCq_dPihNYmAAAA

[install instructions.]: https://exiftool.org/install.html

---

Restores your borked metadata for foo.jpg in the current directory:

<pre>
<code>exiftool -restore_original foo.jpg</code>
</pre>

Note: This works because ExifTool automatically takes a metadata snapshot the first time you write to foo.jpg using ExifTool. If you first write a caption using, e.g. Photo Mechanic, and then write over that tag with ExifTool, the snapshot will only go as far back as when ExifTool first wrote the file.

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

---

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

---

Copies text from foo.txt to XMP description tag in bar.jpg:

<pre>
<code>exiftool "-xmp-dc:description<=foo.txt" bar.jpg</code>
</pre>

Copies text from foo.txt to XMP description tag in all JPGs in current directory (writes the same content to all images):

<pre>
<code>exiftool "-xmp-dc:description<=foo.txt" -ext jpg .</code>
</pre>

---

Displays any files in current directory that have XMP label tag:

<pre>
<code>exiftool -if '$xmp-xmp:Label' -xmp-xmp:label .</code>
</pre>

This will display both the file name and the color of the label tag. For MacOS, remember to add the single quotes because <code>$</code> sigil is used.

---

Finds any JPG files in current directory that have Red XMP label, then, for files that match, writes the label and XMP description tags to foo.csv:

<pre>
<code>exiftool -ext jpg -if '$xmp-xmp:label =~ /Red/i' -csv -xmp-xmp:label -xmp-dc:description . >foo.csv</code>
</pre>

Note: The <code>i</code> in <code>/Red/i</code> makes if statement case insensitive. However, Photo Mechanic is picky and only recognizes XMP label if it is capitalized. Use Red, Yellow, Green, Blue, Purple, Cyan, Brown, Trash, None if you're writing to file. I'm not sure about how these values are mapped by other apps like Adobe Bridge, Capture One.

---

Finds XMP sidecar files in current directory that have XMP city tag, then prepends those tags to the XMP description tag. (I.e. "City...Description". I'm using an ellipses here but format it however you like.):

<pre>
<code>exiftool -ext xmp '-xmp-dc:description<${xmp-iptcext:locationcreatedcity}...${xmp-dc:description}' .</code>
</pre>

For MacOS, remember to add the single quotes because <code>$</code> sigil is used.

There are multiple ways to store the same location info in XMP. (Within the XMP group alone, location tags can be written to xmp-photoshop, xmp-iptccore, xmp-iptcext.)

For JPGs, location tags might also be in EXIF or IPTC groups. Photo Mechanic and some other apps can read and write these tags, and will try to sync all of them. Redundancy is important in metadata because most apps only read a few specific tags. Fiendishly arcane. If you want to use ExifTool to write location tags, you'll want to be specific.





## Writing and copying GPS in video files

Copies GPS coordinates from all MP4 files in current directory to foo.csv:

<pre>
<code>exiftool -csv -gpslatitude -gpslongitude -gpsaltitude -n -ext mp4 . >foo.csv</code>
</pre>

Note: You can output to CSV or TXT, e.g.: bar.txt instead of the above example. This snippet will also work for other types of image files that have embedded GPS coordinates, like JPGs, MOVs or XMP sidecar files.

---

If you are going to write geolocation info directly into video files, it can help to write to UserData with MP4s and to Keys for MOV files. Like with all metadata, it really depends on your workflow and what is supported by the apps you use to manage your picture library. (Personally, I either write to XMP directly within the file or write to an XMP sidecar file.)

For an MP4 file, try writing GPS coords into UserData:

<pre>
<code>exiftool -userdata:gpscoordinates="45.0536,1.17,187" foo.mp4</code>
</pre>

For an MOV file, try writing GPS coords into Keys:

<pre>
<code>exiftool -keys:gpscoordinates="36.0831,-107.9999,1856" bar.mp4</code>
</pre>

UserData and Keys are subgroups of the Quicktime group. Writing to these tags will work with some video files (specifically MP4 and MOV). These tags won't work at all for JPGs or other stills files that don't support the Quicktime metadata block.

The GPSCoordinates tag is a composite tag related to the Quicktime group and, again, won't work on JPGs or other types of files. It's very handy for MP4 and MOV files because it allows you to enter your GPS as an x,y,z string all in the same flag.

X,Y,Z = latitude, longitude, altitude.

Z = altitude in meters above sea level.

Coordinates in the southern or western hemispheres use a minus symbol, e.g, 9.5566 South, 78.2358 West:

-9.5566, -78.2358

Negative values for elevation work, too, if your location is below sea level.

---

Copies GPS to XMP group directly in foo.mp4:

<pre>
<code>exiftool "-xmp:gpslongitude<gpslongitude" "-xmp:gpslatitude<gpslatitude" "-xmp:gpsaltitude<gpsaltitude" foo.mp4</code>
</pre>

---

Copies GPS from foo.mp4 to an XMP sidecar:

<pre>
<code>exiftool "-xmp:gpslongitude<gpslongitude" "-xmp:gpslatitude<gpslatitude" "-xmp:gpsaltitude<gpsaltitude" -srcfile %d%f.xmp foo.mp4</code>
</pre>

If you have other tags in your file, make sure you copy those over, too. There is an example below.

---

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




## Inject altitude into GPS coords string in an MP4

You can inject an arbitrary altitude into GPS metadata. (Some of the apps I use have trouble with video files that don't include full coords as an x,y,z string. This is useful, for example, for videos shot on Android mobile phones, where altitude isn't captured in MP4 files.)

I hesitate to add arbitrary altitude into the coords string but, in some situations, it is more helpful than not.

Injects altitude of 0 meters above sea level into UserData:GPSCoordinates string in foo.mp4, while maintaining the file's existing x,y coordinates:

<pre>
<code>exiftool -ext mp4 '-userdata:gpscoordinates<$gpslatitude,$gpslongitude,0' foo.mp4</code>
</pre>

You can write this snippet, and then write one of the snippets above to copy full x,y,z coords to an XMP sidecar file.

---

Injects altitude of 4207 meters above sea level into UserData:GPSCoordinates string for all MP4 files in current directory. Then, copies GPS x,y,z coords to XMP sidecar files for all MP4s in current directory. (I'm using a <code>|</code> pipe to string 2 commands together, one after another. There is probably a more efficient way to do this, though.):

<pre>
<code>exiftool -ext mp4 '-userdata:gpscoordinates<$gpslatitude,$gpslongitude,4207' . | exiftool ext -mp4 '-xmp:gpslongitude<gpslongitude' '-xmp:gpslatitude<gpslatitude' '-xmp:gpsaltitude<gpsaltitude' -srcfile %d%f.xmp .</code>
</pre>

If you have other tags embedded in your files, you will need to add flags to copy them over to the XMP sidecar files, too.
