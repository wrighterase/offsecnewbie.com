---
id: 61
title: 'Inception &#8211; HacktheBox'
date: 2018-04-14T13:58:07+01:00
author: rowbot
layout: post
guid: https://offsecnewbie.wordpress.com/?p=61
permalink: /2018/04/14/inception-hack-the-box/
timeline_notification:
  - "1523714289"
image: /wp-content/uploads/2018/04/inception-1.png
categories:
  - Hackthebox
---
LEARNED  
Using a proxy with nikto  
Gobuster can not search recursively  
Searchsploit can search output of nmap

&nbsp;

START

What ports are open on this machine

<pre>[root:~/Desktop/inception]# nmap -p- 10.10.10.67 -T5
Starting Nmap 7.70 ( https://nmap.org ) at 2018-04-12 09:54 BST
Nmap scan report for 10.10.10.67
Host is up, received echo-reply ttl 63 (0.034s latency).
Not shown: 65533 filtered ports
Reason: 65533 no-responses
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT STATE SERVICE REASON
80/tcp open http syn-ack ttl 62
3128/tcp open squid-http syn-ack ttl 62

Nmap done: 1 IP address (1 host up) scanned in 56.14 seconds</pre>

A more comprehensive scan needed focusing on those ports

<pre>[root:~/Desktop/inception]# nmap -p80,3128 -sC -sV -oA detailed.portscan -T4 10.10.10.67
Starting Nmap 7.70 ( https://nmap.org ) at 2018-04-12 09:57 BST
Nmap scan report for 10.10.10.67
Host is up, received echo-reply ttl 63 (0.034s latency).

PORT STATE SERVICE REASON VERSION
80/tcp open http syn-ack ttl 62 Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Inception
3128/tcp open http-proxy syn-ack ttl 62 Squid http proxy 3.5.12
|_http-server-header: squid/3.5.12
|_http-title: ERROR: The requested URL could not be retrieved

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 42.07 seconds</pre>

OK it didn&#8217;t reveal anything extra really other than the http-proxy can not be retrieved.

GoBuster time.

<pre>[root:~/Desktop/inception]# gobuster -u 10.10.10.67 -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt -t 100 -r -f

Gobuster v1.2 OJ Reeves (@TheColonial)
=====================================================
[+] Mode : dir
[+] Url/Domain : http://10.10.10.67/
[+] Threads : 100
[+] Wordlist : /usr/share/wordlists/dirbuster/directory-list-1.0.txt
[+] Status codes : 302,307,200,204,301
[+] Add Slash : true
[+] Follow Redir : true
=====================================================
/images/ (Status: 200)
/assets/ (Status: 200)
=====================================================</pre>

Note the T 100 sets the treads to 100, not recommended to use on the free hack the box as it may impact the server. I&#8217;m on the VIP which means less users are on it so the T 100 wont affect it as much. I added the -f to follow any redirects, i did this as the website could send us to the proxy site. Unfortunately i can not see how to make the search recursive &#8211; meaning search the contents of each folder it finds. I did a GoBuster search using http://10.10.10.67:3128 but nothing came back.

After several attempt nikto with 10.10.10.67 coming back with nothing and 10.10.10.67:3128 coming back with ALL the vulnerabilities, i managed to get the command correct.

<pre>[root:~/Desktop/inception]# nikto -h 10.10.10.67 -useproxy http://10.10.10.67:3128
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP: 10.10.10.67
+ Target Hostname: 10.10.10.67
+ Target Port: 80
+ Proxy: 10.10.10.67:3128
+ Start Time: 2018-04-12 10:40:47 (GMT1)
---------------------------------------------------------------------------
+ Server: squid/3.5.12
+ Retrieved via header: 1.1 Inception (squid/3.5.12)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ Uncommon header 'x-squid-error' found, with contents: ERR_ACCESS_DENIED 0
+ Uncommon header 'x-cache-lookup' found, with contents: NONE from Inception:3128
+ Uncommon header 'x-cache' found, with contents: MISS from Inception
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ All CGI directories 'found', use '-C none' to test none

+ 26152 requests: 0 error(s) and 7 item(s) reported on remote host
+ End Time: 2018-04-12 10:56:28 (GMT1) (941 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested</pre>

But it didn&#8217;t give me anything useful (that i could tell). More web enumeration needed

<pre>[root:~]# gobuster -u http://10.10.10.67 -w /usr/share/wordlists/dirb/big.txt -s '200,204,301,302,307,403,500' -e 
 
 
Gobuster v1.2 OJ Reeves (@TheColonial)
=====================================================
[+] Mode : dir
[+] Url/Domain : http://10.10.10.67/
[+] Threads : 10
[+] Wordlist : /usr/share/wordlists/dirb/big.txt
[+] Status codes : 204,301,302,307,403,500,200
[+] Expanded : true 
===================================================== 
http://10.10.10.67/.htpasswd (Status: 403)
http://10.10.10.67/.htaccess (Status: 403)
http://10.10.10.67/assets (Status: 301)
http://10.10.10.67/dompdf (Status: 301)
http://10.10.10.67/images (Status: 301)
http://10.10.10.67/server-status (Status: 403)
=====================================================</pre>

I could not see anything useful on those sites:

Need to figure out what type of service is running on 3128

<pre>[root:~/Desktop/inception]# amap 10.10.10.67 3128
amap v5.4 (www.thc.org/thc-amap) started at 2018-04-12 15:30:53 - APPLICATION MAPPING mode

Protocol on 10.10.10.67:3128/tcp matches http
Protocol on 10.10.10.67:3128/tcp matches http-apache-2
Protocol on 10.10.10.67:3128/tcp matches http-proxy

Unidentified ports: none.

amap v5.4 finished at 2018-04-12 15:30:59</pre>

As expected a http proxy &#8211; we knew this already from previous scans.

During my attempts at enumeration i found a useful thing searchsploit does. It can search your nmap outputs. Make sure you output as .xml ( I normally do -oA which outputs all formats that includes xml).

<pre>[root:~/Desktop/inception]# searchsploit --nmap detailed.portscan.xml
[i] SearchSploit's XML mode (without verbose enabled). To enable: searchsploit -v --xml...
[i] Reading: 'detailed.portscan.xml'

[i] /usr/bin/searchsploit -t apache httpd 2 4 18
[i] /usr/bin/searchsploit -t squid http proxy 3 5 12</pre>

But no additional info was forthcoming.

The source of the web page is interesting but i cant seem to get burp to change user agent to IE8 &#8211; not even sure if that will reveal anything

<pre>html&gt;html&gt; &lt;head&gt; &lt;title&gt;Inception&lt;/title&gt; &lt;meta charset="utf-8" /&gt; &lt;meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no" /&gt; &lt;!--[if lte IE 8]&gt;<a href="http://assets/js/ie/html5shiv.js">http://assets/js/ie/html5shiv.js</a>&lt;![endif]--&gt; &lt;link rel="stylesheet" href="assets/css/main.css" /&gt; &lt;!--[if lte IE 8]&gt;&lt;link rel="stylesheet" href="assets/css/ie8.css" /&gt;&lt;![endif]--&gt; &lt;!--[if lte IE 9]&gt;&lt;link rel="stylesheet" href="assets/css/ie9.css" /&gt;&lt;![endif]--&gt; &lt;/head&gt; &lt;body&gt;
 &lt;!-- Header --&gt; &lt;header id="header"&gt; &lt;h1&gt;Inception&lt;/h1&gt; &lt;p&gt;Dreams feel real while your in them right? &lt;br /&gt; Its only when you wake up when you realize they were actually strange. &lt;/p&gt; &lt;/header&gt;
 &lt;!-- Signup Form --&gt; &lt;form id="signup-form" method="post" action="#"&gt; &lt;input type="email" name="email" id="email" placeholder="Email Address" /&gt; &lt;input type="submit" value="Sign Up" /&gt; &lt;/form&gt;
 &lt;!-- Footer --&gt; &lt;footer id="footer"&gt; &lt;ul class="icons"&gt; &lt;li&gt;&lt;a href="#" class="icon fa-twitter"&gt;&lt;span class="label"&gt;Twitter&lt;/span&gt;&lt;/a&gt;&lt;/li&gt; &lt;li&gt;&lt;a href="#" class="icon fa-instagram"&gt;&lt;span class="label"&gt;Instagram&lt;/span&gt;&lt;/a&gt;&lt;/li&gt; &lt;li&gt;&lt;a href="#" class="icon fa-github"&gt;&lt;span class="label"&gt;GitHub&lt;/span&gt;&lt;/a&gt;&lt;/li&gt; &lt;li&gt;&lt;a href="#" class="icon fa-envelope-o"&gt;&lt;span class="label"&gt;Email&lt;/span&gt;&lt;/a&gt;&lt;/li&gt; &lt;/ul&gt; &lt;ul class="copyright"&gt; &lt;li&gt;&copy; Inception, Inc.&lt;/li&gt;&lt;li&gt;Credits: Dominic Cobb&lt;/a&gt;&lt;/li&gt; &lt;/ul&gt; &lt;/footer&gt;
 &lt;!-- Scripts --&gt; &lt;!--[if lte IE 8]&gt;<a href="http://assets/js/ie/respond.min.js">http://assets/js/ie/respond.min.js</a>&lt;![endif]--&gt; <a href="http://assets/js/main.js">http://assets/js/main.js</a></pre>

At the bottom of the main page&#8217;s source ( you have to scroll through blank space ) you see and I use searchsploit on it

<pre id="line1"><span id="line1051"></span><span class="comment">&lt;!-- Todo: test dompdf on php 7.x --&gt;</span></pre>

And the box went live! This is how far I got after about 5 hours of working at it. Probably more than 5 hours but thats all i want to admit to.

I&#8217;ll post up later the walkthrough once i finally get this box under my belt.