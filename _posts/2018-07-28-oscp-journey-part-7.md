---
id: 332
title: OSCP Journey Part 7
date: 2018-07-28T17:45:46+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=332
permalink: /2018/07/28/oscp-journey-part-7/
image: /wp-content/uploads/2018/06/perfexion_logo.png
categories:
  - Progress Bar
---
<pre class="hljs css"><span class="hljs-selector-tag">Day:</span> -56
<span class="hljs-selector-tag">PDF</span>: 90%
<span class="hljs-selector-tag">Videos</span>: 95%
<span class="hljs-selector-tag">Boxes</span>: 4
<span class="hljs-selector-tag">Networks</span><span class="hljs-selector-pseudo">:1</span></pre>

I&#8217;ve decided to change the way i count the days &#8211; going by how long until my lab runs out. I&#8217;ll go back at some stage and change the other posts to match this new format.

I&#8217;ve just completed the write up for a box after finally getting root. Wish I could go into more details here about this but I&#8217;ll not. Working on a github repo to formulate my Priv Esc techniques, once I&#8217;ve put some decent content on there I&#8217;ll share. Tbh I&#8217;m new to publishing anything on github so I&#8217;ll have to learn that process.

I had been feeling quite down about my progress and tbh quite alone about going through this. I woke up to a few comments on my previous post which jeered me on. Its kinda nice to feel as if people are following my progress as I had no idea if anyone was reading this. So &#8216;ave at it feel free to post a comment and push me on even if you post up how you found my site.

It maybe be useful to go over what I&#8217;ve learned of late. The /etc/passwd file superseeds the /etc/shadow file. So in essence if you can edit the /etc/passwd file you can add yourself as a user then su to become root. Then do su to become root. You can only do this if the /etc/passwd file is writeable. Eg add user test to passwd file.

<pre><span class="TextRun SCXO216684294" lang="EN-US" xml:lang="EN-US"><span class="NormalTextRun SCXO216684294">echo test::0:0:test:/root:/bin/bash &gt;&gt; /</span><span class="SpellingError SCXO216684294">etc</span><span class="NormalTextRun SCXO216684294">/</span><span class="SpellingError SCXO216684294">passwd</span></span><span class="EOP SCXO216684294"> </span></pre>

Removing `x` means root requires no password anymore