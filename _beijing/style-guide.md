---
permalink: /beijing/bts/style-guide
robots: norobots
layout: kaoliang_nite_3
date: 2020-09-14
title: Style guide
description: Style guide for The Photographers Field Guide to Beijing
lede: print
nav: |
  + [Mostly AP style?](https://www.zachmccabe.com/beijing/bts/style-guide.html#mostly-ap-style)
  + [Styling content](https://www.zachmccabe.com/beijing/bts/style-guide.html#styling-content)
  + [Styling code](https://www.zachmccabe.com/beijing/bts/style-guide.html#styling-code)

---


Like every project worth its salt, the field guide needed its own style guide. I took my sticky notes and organized them here.



{% include page_nav.html %}



## Mostly AP style?

Chinese names and places always use pinyin romanization. See: [Chinese,] below.

Dates use ISO 8601 format: YYYY-MM-DD.

Dialogue is formatted using italics and double quotes for clarity, e.g. _"Hello world,"_ she said. Also see: [quotes and citations] and [chunkify.]

Ellipses are used as a narrative device – not just to truncate quotes. Because this is not a newspaper…

Metric and imperial units are included in the ebook. Beijingers themselves will expect you not only to use the metric system but also local standards like <mark>jin <span lang="zh">斤</span></mark> and <mark>wan <span lang="zh">万</span></mark>.

Numbers are never spelled out. Line length is short, and each character counts. (Names might be an exception, e.g. Three Shadows Gallery, and not 3 Shadows Gallery.) See: [line length.]

[Chinese,]: https://www.zachmccabe.com/beijing/bts/style-guide.html#chinese

[quotes and citations]: https://www.zachmccabe.com/beijing/bts/style-guide.html#quotes-and-citations

[chunkify.]: https://www.zachmccabe.com/beijing/bts/style-guide.html#chunkify

[line length.]: https://www.zachmccabe.com/beijing/bts/style-guide.html#line-length



## Styling content

### Chinese

Include Chinese when it will help the reader, e.g.: names, landmarks. Imagine the reader pointing to this text when asking a passerby for directions, or when negotiating with a cabbie.

Pinyin romanization is always used. Do not include tone marks.

Chinese characters should follow the pinyin the first time a word or phrase is used. Chinese characters should be included in the location index, too. Use elsewhere as needed.

Chinese punctuation should be used for titles, dialogue and quotation. Again, this isn't for the reader as much as it is for native speakers who the reader might consult, like a concierge or a fixer. Also see: [chunkify] and [lang,] both below.  

[chunkify]: https://www.zachmccabe.com/beijing/bts/style-guide.html#chunkify

[lang,]: https://www.zachmccabe.com/beijing/bts/style-guide.html#lang



### Photos

1. Pictures should support the copy.
2. Each additional picture adds weight to the ebook's file size on the readers device.
3. Most of all, each picture needs to tell a story.



### Small screens

Write for mobile. Keep paragraphs short, structure copy and visual queues to make reading in this environment easier.

Kindle and other reading apps do not give authors control over presentation width or line length. This can make reading feel breathless on narrower, mobile screens and exhausting on wider displays. 

Thankfully, most apps give the reader some options within the settings to adjust margins, or switch to a 2-column layout. (How many readers know that option is there? How many toggle it on?)



### Structure copy for quick answers

This is a reference book. The reader should be able to easily navigate info, and to hop back-and-forth without thinking about it.



### Tone

Personal and fun. Move it along because people need answers to their questions. The reader may already be on the ground.

1. Don't assume that the reader is from the US, or that her native language is English.
2. TBH try to go easy on the abbreviations.
3. For fuckssake, don't say "fuck." The ebook is meant for a wide-ish audience. Some people prefer plain prose without the pepper.



### Quotes and citations

Quotes should be used sparingly because the reader is searching for quick answers. Paraphrase. Try to limit supporting info -- e.g. links and quotations -- to footnotes for any especially curious readers.

+ Book and composition titles: follow AP style.
+ Dialogue: use italics and double quotes.
+ Foreign-language words and phrases: follow AP style, plus format per [Chinese.]

Citations in the ebook, like the rest of this website, use a bastardized variant of Chicago. (It's a work in progress, I admit.)

+ Citations that link to audio or video content should include a timestamp. See: [Link strategy.]
+ Citation info needs to be easy to scan for the reader; full Chicago citations can get very dense.
+ Citations should be easy for me to format using markdown.

[Chinese.]: https://www.zachmccabe.com/beijing/bts/style-guide.html#chinese

[Link strategy.]: https://www.zachmccabe.com/beijing/bts/style-guide.html#link-strategy




## Styling code

### Chunkify

Make the book easy to scan. See #2 and #3 under [10 Assumptions.]

Use headers, short paragraphs, blockquote and bold text to help chunkify information. This is important for human readers, and also so that screen readers and reading apps can parse your text.

Be careful with `<aside>` because iBooks is idiosyncratic about displaying contents wrapped in this tag. For more, see Apple's [Apple Books asset guide.]

[10 Assumptions.]: https://www.zachmccabe.com/beijing/bts/10-assumptions.html

[Apple Books asset guide.]: https://help.apple.com/itc/booksassetguide/




### Blockquote

Don't use blockquote for sidebars, info boxes or anything other than quotations. It's semantically incorrect (even if markdown makes it easy). To visually separate info for the reader, use `<span>` or `<div>` and a bit of CSS.

Be careful with `<blockquote>` because Kindle appears to overrides your CSS. See: [Kindle CSS,] below.

[Kindle CSS,]: https://www.zachmccabe.com/beijing/bts/style-guide.html#kindle-css




### Icons

All the old, inline icons were removed for the v2 field guide.

Iconography within the V1 field guide was in GIF format because, after much testing, Kindle's support for either PNG or SVG was a no go. Annoyingly there were still some issues with the GIF files that made them distracting enough to limit their usefulness.

Lesson learned? Keep it simple, folks.




### Images

Within the ebook, images are 1:1 at 1000px. Photographs are JPG. Other visuals are PNG.




### Kindle CSS

In my testing, it seems like Kindle overrides CSS styling for many `html5` tags. I've had trouble with these:

* `<b>`
* `<blockquote>`
* `<figcaption>`
* `<h5>`
* `<h6>`
* `<mark>`
* `rgb() color` doesn't seem to work? I ran out of patience and stuck with hex. Generally, be very careful with color in Kindle. There are usability issues for readers to deal with, and possibly bugs for you to deal with.




### Lang

Multilingual words and phrases are noted with `<span>` tags and CSS. Kindle doesn't seem to support `<mark>` tags.

Chinese gets `<span lang="zh">` tags.

The result looks like this:

`<span class="hilite">English <span lang="zh">汉字</span></span>`




### Line length

Line length can be a problem, watch out! Use CSS so that and printed URLs can break to a new line at the edge of the box. Also see: [small screens.](https://https://www.zachmccabe.com/beijing/bts/style-guide.html#small-screens)



### Links

`<a href>` is old-fashioned, underlined and `#00f`. Within the ebook, this is important for any users with monochromatic, e-ink Kindle devices. The website uses `#268bd2`, inspired by the always-stylish [Solarized](http://ethanschoonover.com/solarized).
