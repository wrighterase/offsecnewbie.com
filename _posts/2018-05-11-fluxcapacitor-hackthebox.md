---
id: 166
title: 'FluxCapacitor &#8211; HacktheBox'
date: 2018-05-11T10:12:44+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=166
permalink: /2018/05/11/fluxcapacitor-hackthebox/
image: /wp-content/uploads/2018/05/flux.png
categories:
  - Hackthebox
---
With 1 day left until the box has retired this is the first time I&#8217;ve looked at this box. I will give it a go to see how far I get.

START

Quick scan to see what ports are open.

<pre>[root:~/Desktop/FluxCapacitor]# nmap -F 10.10.10.69
Starting Nmap 7.70SVN ( https://nmap.org ) at 2018-05-11 11:06 BST
Nmap scan report for 10.10.10.69
Host is up, received echo-reply ttl 63 (0.031s latency).
Not shown: 99 closed ports
Reason: 99 resets
PORT STATE SERVICE REASON
80/tcp open http syn-ack ttl 63

Nmap done: 1 IP address (1 host up) scanned in 1.78 seconds</pre>

Port 80 is open, lets kick off a more detailed scan while I poke about to see what I can see through port 80. The scans I started are listed below.

I loaded up a browser and got this webpage

<img class="alignnone size-full wp-image-172" src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/web-1.png?resize=662%2C186" alt="" width="662" height="186" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/web-1.png?w=662 662w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/web-1.png?resize=300%2C84 300w" sizes="(max-width: 662px) 100vw, 662px" data-recalc-dims="1" /> 

The source looked like this. Notice the tags starting with <em> &#8211; very funny.

<pre id="line1"><span class="doctype">&lt;!DOCTYPE html&gt;</span>
<span id="line2"></span>&lt;<span class="start-tag">html</span>&gt;
<span id="line3"></span>&lt;<span class="start-tag">head</span>&gt;
<span id="line4"></span>&lt;<span class="start-tag">title</span>&gt;Keep Alive&lt;/<span class="end-tag">title</span>&gt;
<span id="line5"></span>&lt;/<span class="end-tag">head</span>&gt;
<span id="line6"></span>&lt;<span class="start-tag">body</span>&gt;
<span id="line7"></span>	OK: node1 alive
<span id="line8"></span>	<span class="comment">&lt;!--
<span id="line9"></span>		Please, add timestamp with something like:
<span id="line10"></span>		&lt;script&gt; $.ajax({ type: "GET", url: '/sync' }); &lt;/script&gt;
<span id="line11"></span>	--&gt;</span>
<span id="line12"></span>	&lt;<span class="start-tag">hr</span>/&gt;
<span id="line13"></span>	FluxCapacitor Inc. info@fluxcapacitor.htb - http://fluxcapacitor.htb&lt;<span class="start-tag">br</span>&gt;
<span id="line14"></span>	&lt;<span class="start-tag">em</span>&gt;&lt;<span class="start-tag">met</span>&gt;&lt;<span class="start-tag">doc</span>&gt;&lt;<span class="start-tag">brown</span>&gt;Roads? Where we're going, we don't need roads.&lt;/<span class="end-tag">brown</span>&gt;&lt;/<span class="end-tag">doc</span>&gt;&lt;/<span class="end-tag">met</span>&gt;&lt;/<span class="end-tag">em</span>&gt;
<span id="line15"></span>&lt;/<span class="end-tag">body</span>&gt;
<span id="line16"></span>&lt;/<span class="end-tag">html</span>&gt;
<span id="line17"></span></pre>

While I was poking about I ran Dirbuster, Gobuster and Dirsearch one after another all came back with 3 pages which I didn&#8217;t have permission to view.

<pre>[root:~]# dirsearch -u 10.10.10.69 -e php -w /usr/share/wfuzz/wordlist/general/megabeast.txt -t 100

_|. _ _ _ _ _ _|_ v0.3.8
(_||| _) (/_(_|| (_| )

Extensions: php | Threads: 100 | Wordlist size: 20469

Error Log: /pentest/intelligence-gathering/dirsearch/logs/errors-18-05-11_13-52-07.log

Target: 10.10.10.69

[13:52:17] 403 - 577B - /synchronism
[13:52:17] 403 - 577B - /synchronization
[13:52:17] 403 - 577B - /synchronizer
[13:52:17] 403 - 577B - /synchronize
[13:52:17] 403 - 577B - /synchronizers
[13:52:17] 403 - 577B - /synchronized
[13:52:17] 403 - 577B - /synchronizes
[13:52:17] 403 - 577B - /synchronizing
[13:52:17] 403 - 577B - /synchronously
[13:52:17] 403 - 577B - /synchrony
[13:52:17] 403 - 577B - /synchrotron
[13:52:17] 403 - 577B - /synchronous
[13:52:17] 403 - 577B - /syncopate

Task Completed

</pre>

I browsed to all pages listed here and got a 403 forbidden error. Just a note, if anyone knows a script that can take the input of a file say the list of directories above and check to see what message you get from the website, let me know.

I opened Burp and checked the responses. As you can see the Server is SuperWAF &#8211; some sort of Web Application Firewall. Google does not tell me what exactly is SuperWAF therefore my thoughts lead me to think it just a custom name for a server that is acting as a firewall &#8211; I maybe wrong here.

<img class="alignnone size-full wp-image-173" src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/response.png?resize=680%2C368" alt="" width="680" height="368" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/response.png?w=853 853w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/response.png?resize=300%2C162 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/response.png?resize=768%2C416 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

Just a side note curl -v would have gave me the same output

<pre>[root:~/Desktop/FluxCapacitor]# curl -v 10.10.10.69
* Rebuilt URL to: 10.10.10.69/
* Trying 10.10.10.69...
* TCP_NODELAY set
* Connected to 10.10.10.69 (10.10.10.69) port 80 (#0)
&gt; GET / HTTP/1.1
&gt; Host: 10.10.10.69
&gt; User-Agent: curl/7.58.0
&gt; Accept: */*
&gt; 
&lt; HTTP/1.1 200 OK
&lt; Date: Fri, 11 May 2018 13:04:06 GMT
&lt; Content-Type: text/html
&lt; Content-Length: 395
&lt; Last-Modified: Tue, 05 Dec 2017 16:02:29 GMT
&lt; Connection: keep-alive
&lt; ETag: "5a26c315-18b"
&lt; Server: SuperWAF
&lt; Accept-Ranges: bytes
&lt; 
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;title&gt;Keep Alive&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
 OK: node1 alive
 &lt;!--
 Please, add timestamp with something like:
 &lt;script&gt; $.ajax({ type: "GET", url: '/sync' }); &lt;/script&gt;
 --&gt;
 &lt;hr/&gt;
 FluxCapacitor Inc. info@fluxcapacitor.htb - http://fluxcapacitor.htb&lt;br&gt;
 &lt;em&gt;&lt;met&gt;&lt;doc&gt;&lt;brown&gt;Roads? Where we're going, we don't need roads.&lt;/brown&gt;&lt;/doc&gt;&lt;/met&gt;&lt;/em&gt;
&lt;/body&gt;
&lt;/html&gt;
* Connection #0 to host 10.10.10.69 left intact</pre>

I looked for nmap scripts to detect what type of firewall and ran them. Not sure why they didn&#8217;t work.

<pre>[root:~/Desktop/FluxCapacitor]# locate .nse | grep waf 
/pentest/vulnerability-analysis/nmap/scripts/http-waf-detect.nse
/pentest/vulnerability-analysis/nmap/scripts/http-waf-fingerprint.nse
[root:~/Desktop/FluxCapacitor]# nmap -p80 --script=http-waf-detect.nse 10.10.10.69
Starting Nmap 7.70SVN ( https://nmap.org ) at 2018-05-11 14:09 BST
Nmap scan report for fluxcapacitor.htb (10.10.10.69)
Host is up, received reset ttl 63 (0.030s latency).

PORT STATE SERVICE REASON
80/tcp open http syn-ack ttl 63

Nmap done: 1 IP address (1 host up) scanned in 0.99 seconds
[root:~/Desktop/FluxCapacitor]# nmap --script=http-waf-fingerprint 10.10.10.69
Starting Nmap 7.70SVN ( https://nmap.org ) at 2018-05-11 14:11 BST
Nmap scan report for fluxcapacitor.htb (10.10.10.69)
Host is up, received reset ttl 63 (0.030s latency).
Not shown: 998 closed ports, 1 filtered port
Reason: 998 resets and 1 no-response
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT STATE SERVICE REASON
80/tcp open http syn-ack ttl 63

Nmap done: 1 IP address (1 host up) scanned in 2.84 seconds</pre>

I was expecting something like this as per the nmap manual.

<pre>Script Output
PORT STATE SERVICE REASON
80/tcp open http syn-ack
| http-waf-fingerprint:
| Detected WAF
|_ BinarySec version 3.2.2</pre>

Clearly there is a firewall that is stopping me getting access to it. How to bypass it is the problem. I&#8217;m pretty sure there are no other vectors I should be attacking.

Back from the Google land I found a tool in Kali that will test to see if I&#8217;m behind a WAF for sure.

<pre>[root:~/Desktop/FluxCapacitor]# wafw00f 10.10.10.69

^ ^
 _ __ _ ____ _ __ _ _ ____
 ///7/ /.' \ / __////7/ /,' \ ,' \ / __/
 | V V // o // _/ | V V // 0 // 0 // _/
 |_n_,'/_n_//_/ |_n_,' \_,' \_,'/_/
 &lt;
 ...'

WAFW00F - Web Application Firewall Detection Tool

By Sandro Gauci && Wendel G. Henrique

Checking http://10.10.10.69
Generic Detection results:
No WAF detected by the generic detection
Number of requests: 14</pre>

What the hell! No WAF detected! Cricky this is stumping me. I&#8217;m going to go with my (big) gut and assume that the above tool is wrong.

I found this pdf document on owasp website &#8211; link below. The <script>alert(1);<script> reminded me of the note in the index.html page.

<img class="size-full wp-image-176" style="font-weight: bold;" src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/owasp.png?resize=680%2C403" alt="" width="680" height="403" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/owasp.png?w=1087 1087w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/owasp.png?resize=300%2C178 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/owasp.png?resize=768%2C456 768w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/owasp.png?resize=1024%2C608 1024w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

https://www.owasp.org/images/b/bf/OWASP\_Stammtisch\_Frankfurt\_WAF\_Profiling\_and\_Evasion.pdf

It looks like I have to inject a payload to bypass the firewall. Perhaps I could inject a reverse shell. Back to my notes and Google while i figure this out.

I Googled &#8216;_OK: node1 alive_&#8216; to see if that was the output of a known service or script but I couldn&#8217;t find anything &#8211; other than people struggling on the hackthebox forums with this machine. Reviewing the source page again I didn&#8217;t understand what it meant by adding a timestamp.

<pre><span class="comment">&lt;!-- <span id="line9"></span> Please, add timestamp with something like: <span id="line10"></span> &lt;script&gt; $.ajax({ type: "GET", url: '/sync' }); &lt;/script&gt; <span id="line11"></span> --&gt;</span></pre>

I tried in Burp to see what response I&#8217;d get.

<img class="alignnone size-full wp-image-180" src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/timestamp.png?resize=680%2C181" alt="" width="680" height="181" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/timestamp.png?w=1325 1325w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/timestamp.png?resize=300%2C80 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/timestamp.png?resize=768%2C204 768w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/timestamp.png?resize=1024%2C272 1024w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

I Googled to see what the response meant by &#8216;padding to disable&#8230;&#8217; and it seems that the dev put that in to force the 403 error &#8211; so Chrome or MSIE would not use their own &#8216;you don&#8217;t have access to this page, page. So I searched for OpenResty and couldn&#8217;t find any exploits.

So I&#8217;d like to say that time beat me on this box but unfortunately I&#8217;ve hit a wall &#8211; a virtual one WAF. Once the machine is retired I&#8217;ll update this post. _`Sad Beep`_

&nbsp;

&nbsp;