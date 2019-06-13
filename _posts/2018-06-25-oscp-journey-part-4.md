---
id: 297
title: OSCP Journey Part 4
date: 2018-06-25T17:43:38+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=297
permalink: /2018/06/25/oscp-journey-part-4/
image: /wp-content/uploads/2018/06/perfexion_logo.png
categories:
  - Progress Bar
---
<pre class="hljs">Day 7
PDF: 2/3
Videos: 3/4 
Boxes: 0
Networks:1</pre>

One week down and I have been concentrating on getting the exercises completed have made good progress. I&#8217;ve skipped over the Buffer Overflow part of the PDF as I know this is all new learning for me and I want to complete as much of the PDF and exercised before I attempt to assimilate that information.

I have been spending some time using The PenTest Workshop [pentest.ws ](http://pentest.ws)and going to try to use this as much as possible to help document my exploration of the Megacorpone network. The developer is actively working on it, adding new features and has been very open to suggestions. Check out the app in action [PenTest Workshop YouTube Channel](https://goo.gl/SZHpVE)

I have still to pop a box but I&#8217;m not letting that get me down as I haven&#8217;t poked any at great length yet. My nmap skills are great now. It&#8217;s such a powerful tool, now I know why people call it the swiss army knife for admins.

Also a useful tip I picked up through Pentest.ws. When working on a machine type

<pre>export ip=123.123.123.123</pre>

obviously change the ip address to whatever machine you&#8217;re working on. That way you can do $ip to refer to the ip address.

For example:

<pre>nmap $ip</pre>

If you open another tmux windows you need to type this in again.