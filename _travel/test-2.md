---
robots: norobots
layout: kao_midnite_3
title: Just a test 2
date: 2021-09-06
update: 2022-05-13
description: "This is only a test. Test page 2."
lede: print
nav: | 
 + just a test

---



<a href="tel:+11234567891">+1 123 456 7891</a>

---

Crumb: {% include breadcrumbs_collection_only.html %}

Site url: {{ site.url }}

Site baseurl, with absolute_url filter: {{ site.baseurl | absolute_url }}

Page baseurl, with absolute_url filter: {{ page.baseurl | absolute_url }}

Page url: {{ page.url }}

Site collection: {{ site.collection }}

Page collection: {{ page.collection }}

---

<div markdown="1">

*Is this markdown?*

</div>

<div markdown="1">
*And is this markdown?*
</div>

<div style="color:green" markdown="1">
*Last Q, is this still markdown?*

1. Easy
2. As
3. One two three

Did it work?
</div>
