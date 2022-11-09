---
robots: norobots
permalink: /tools/
hub: okay
layout: kao_midnite_3
title: Tools
date: 2022-11-09
description: "Running list of some of the things I'm using right now for work and life. Noting a few techniques I use, and a few problems I'm looking to solve, too."
lede: print
nav: |  
  + Table of Contents
  + [Time and mind management](https://www.zachmccabe.com/tools/#time-and-mind-management) 
  + [Writing](https://www.zachmccabe.com/tools/#writing)
  + [Photography](https://www.zachmccabe.com/tools/#photography)
  + [Digital asset management](https://www.zachmccabe.com/tools/#digital-asset-management)
  + [Play outside or die](https://www.zachmccabe.com/tools/#play-outside-or-die)

---



{% include page_nav.html %}



## Time and mind management

Nix notifications, thx.

Quick 10 minutes of sitting meditation and a good hour of walking every day.

Using Pomodoro for screen-based stuff less than in the past.




## Writing

AP Stylebook, often.

Collaboration via Google Docs, because that's what people ask me to use 🙃 And I do like the new "page-less" mode.

Notetaking in plaintext: using Markor, NeutriNote and Orgzly on Android; On MacOS, TextEdit usually does the job.

Website and ebook stuff formatted using Markdown syntax, and then processed via Jekyll or Pandoc.

- To do: I'd love to find an option on MacOS somewhere between TextEdit and big tools, like Obsidian or BBEdit, whose UI expects you to work within a single 16:9 window. I often write with 2 or 3 windows side by side as columns, so TextEdit is good, but I'd love a few more features like document templates, text expansion, Markdown previews and links to other notes.




## Photography

Sony RX100-series cameras, right now. Fits in my pocket and produces pretty good images. That's enough to satisfy the critical requirements for my on-going project, [Laughing Lost in Briars.]

[Laughing Lost in Briars.]: https://www.zachmccabe.com/briars/#publishing-workflow




### Image processing

When a project allows for it, JPG straight-out-of-camera is my favorite workflow.

DXO Photo Lab, especially for converting raw files from the Sony RX100 cameras. (The on-camera JPG engine on these cameras is disappointing; I nearly sold them until I realized that the raw files were okay. And DXO handles these files better than anything else I've tried.)

RPP64, especially for converting my old Canon raw files to TIF before handing off to other software for converting to black-and-white. Outstanding level of control. No lens profiles or that sort of thing, AFAIK. It's hard to find so here's the link: <https://www.raw-photo-processor.com>




### Image optimization

Kornel Lesinski's app, imageOptim, is better than anything else I've tried at optimizing JPG and PNG file size; I like to use it with an macOS Automator workflow. (Probably would be smart to let a CDN handle this auto-magically but that's overkill for my current use case.)

- To do: I need to tweak the CSS on this website as it seems to be serving images at larger dimensions than necessary. Sorry!

  And, while serving images via B2 + Cloudflare is free, it also feels kinda slow, doesn't it? Better check my cache settings again.



## Digital asset management

The DAM Book, by Peter Krogh, completely changed how I manage my picture workflow and archive. The latest version of The DAM Book not on Amazon, AFAIK. Get it via the author's website: <https://thedambook.com/>

ExifTool for metadata spelunking; here is my note-to-self on [learning ExifTool.]

Photo Mechanic for organizing stills photos. It's been my go-to for 10+ years. For editing (e.g. choosing the right photo) and for working with metadata, Photo Mechanic is the best tool I've used.

Rclone for backups on Mac.

Syncthing on Android + Mac. Been using it for several years, and it's awesome.

- To do: Not sure I've got optimal Rclone set up. It's so slow. Keeping an eye out for alternatives…
- To do: Need to write up my notes after trying out new workflows for reading and writing IPTC and XMP metadata on Andriod, iOS, MacOS.

  Tl;dr: even in 2022, mobile is still no place to manage picture archives or image metadata, I've found 🎲

[learning ExifTool.]: https://www.zachmccabe.com/tools/exiftool-notes




## Web and ebook publishing

Mac Mini 2014 + floor desk + pandemic fern, now well-shaggy.

This website is built on Github Pages; image assets are on Backblaze B2; and everything is delivered over CloudFlare.

You can read all the gory details on building The Photographers Field Guide to Beijing in the book's [colophon.]

[colophon.]: https://www.zachmccabe.com/beijing/bts/colophon




## Play outside or die

Always looking for a better hat 🤠

Dish soap and cold water to wash off the poison ivy. Generic Zanfel if things get real itchy.

iNaturalist and Stellarium apps on Android because the universe is fucking amazing, and I like learning about it.

Weather and climate stuff on Android, too, because I work outside:

+ https://droughtmonitor.unl.edu
+ https://texaswildfirerisk.com
+ https://public.tfswildfires.com
+ Radarscope app
+ wX app, an awesome front end for NWS forecast data by developer [Joshua Tee] 
+ yr.no app

Wells Lamot work gloves for fixing fence, picking rocks and that time we beat out a brush fire with shovels. The ratio between price:dexterity:durability is good.

[Joshua Tee]: https://gitlab.com/joshua.tee/wx/-/blob/master/doc/ChangeLog_User.md



<img src="https://www.zachmccabe.com/assets/viz/ipod-linux-300.gif" alt="hacked my ipod and running linux, circa 2006" style="padding:5em 0;" />



Whenever I travel, visit a friend's workshop or read a bit of [archaeology,] it is always the day-to-day stuff people use that raises my eyebrow and makes me smile. Only fair to ante up, eh?

[archaeology,]: https://archive.org/details/StoneAgeEconomics_201611/page/n31

We tool-making apes live in a near symbiotic state with the stuff we use day-after-day, and that's worth a closer look.

After the /About page, the first thing I look for when visiting someone's website is the /Tools or /Uses page. This gives me a peak into that person's day-to-day, and I'm always learning about new ways to work and problem-solving strategies, too. It's fun.

I can't promise you'll learn anything here but I will try to keep it \*not\* boring :)

Because I find that writing this list is a good way to take a fresh look at my own tools and workflows, I try to update this page at least twice a year.
