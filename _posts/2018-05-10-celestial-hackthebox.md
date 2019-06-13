---
id: 94
title: 'Celestial &#8211; Hackthebox'
date: 2018-05-10T20:29:35+01:00
author: rowbot
layout: post
guid: https://offsecnewbie.wordpress.com/?p=94
permalink: /2018/05/10/celestial-hackthebox/
image: /wp-content/uploads/2018/05/Celestial.png
categories:
  - Hackthebox
---
nmap scan of box

<pre>Nmap scan report for 10.10.10.85
Host is up, received reset ttl 63 (0.033s latency).

PORT STATE SERVICE REASON VERSION
3000/tcp open http syn-ack ttl 63 Node.js Express framework
|_http-title: Site doesn't have a title (text/html; charset=utf-8).

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Apr 30 11:31:19 2018 -- 1 IP address (1 host up) scanned in 13.24 seconds
[root:~/Desktop/celestial]#</pre>

I tried all manner of scans and this was the only port that I could find.

Browsing to the webpage gave me this.

<img class="alignnone size-full wp-image-98" src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/firefox.png?resize=385%2C250" alt="firefox" width="385" height="250" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/firefox.png?w=385 385w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/firefox.png?resize=300%2C195 300w" sizes="(max-width: 385px) 100vw, 385px" data-recalc-dims="1" /> 

The source code didnt give me anything to go on. I decided to open burpsuite to see what i could find.

<img class="alignnone size-full wp-image-97" src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burp1.png?resize=680%2C361" alt="burp1" width="680" height="361" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burp1.png?w=1113 1113w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burp1.png?resize=300%2C159 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burp1.png?resize=768%2C408 768w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burp1.png?resize=1024%2C544 1024w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

I noticed there was a cookie profile in the response. I wanted to see if i could decode this.

<img class="alignnone size-full wp-image-96" src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burpe2.png?resize=680%2C126" alt="burpe2" width="680" height="126" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burpe2.png?w=1830 1830w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burpe2.png?resize=300%2C55 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burpe2.png?resize=768%2C142 768w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burpe2.png?resize=1024%2C189 1024w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burpe2.png?w=1360 1360w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

I did a URL decode followed by Base64 decode

<img class="alignnone size-full wp-image-100" src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burp4.png?resize=680%2C210" alt="burp4" width="680" height="210" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burp4.png?w=1883 1883w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burp4.png?resize=300%2C93 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burp4.png?resize=768%2C237 768w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burp4.png?resize=1024%2C316 1024w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/burp4.png?w=1360 1360w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

As you can see it gave me something readable. At this point I&#8217;m not sure what to do with this new information.