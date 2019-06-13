---
id: 343
title: OSCP Journey Part 8
date: 2018-08-02T15:34:27+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=343
permalink: /2018/08/02/oscp-journey-part-8/
image: /wp-content/uploads/2018/06/perfexion_logo.png
categories:
  - Progress Bar
  - Useful things
---
<pre class="hljs css"><span class="hljs-selector-tag">Day:</span> <span class="hljs-selector-tag">-51</span>
<span class="hljs-selector-tag">PDF</span>: 90%
<span class="hljs-selector-tag">Videos</span>: 95%
<span class="hljs-selector-tag">Boxes</span>: 5
<span class="hljs-selector-tag">Networks</span><span class="hljs-selector-pseudo">:1</span></pre>

Well I&#8217;m a bit more ubeat as I&#8217;ve put in a good amount of hours this week though would have liked to have done more. Work getting in the way of my learning! Anyways, I&#8217;ve just popped a Windows Box and it was a real struggle but finally got there in the end. Anyways just a blog while I&#8217;ve got the NT Authority\System horn. Here is some info I&#8217;ve used while popping this box;

 

Good resource =[ http://www.fuzzysecurity.com/tutorials/16.html ](http://www.fuzzysecurity.com/tutorials/16.html)

The difference between Staged and Non-Staged payloads:

**Staged = windows/shell/reverse_tcp** = spawn a shell connect back to attacker  
try to use this one more&#8230;i think.

**Non-Staged = windows/shell\_reverse\_tcp** = connect back to attacker and spawn a shell  
useful if bandwidth is an issue

Once you get logged in as a low priv shell find what hotfixes have been installed and see if they have any exploits

<pre>systeminfo</pre>

A useful command I&#8217;ve come across to pull info about the box + the proofs is:

<pre>echo. & echo. & <span class="red">echo</span> whoami: & <span class="red">whoami</span> 2&gt; nul & <span class="red">echo</span> %username% 2&gt; nul & echo. & <span class="red">echo</span> Hostname: & hostname & echo. & ipconfig /all & echo. & <span class="red">echo</span> proof.txt: &  type <span class="green">"C:\Documents and Settings\Administrator\Desktop\proof.txt"</span></pre>

Sometimes though the whoami or %hostname% won&#8217;t work for some reason. If it doesn&#8217;t do

<pre>tasklist /v</pre>

and get look for the user that your shell is logged in as.

<span style="color: #ff0000;">Side note, always set FTP to binary mode by typing binary. This will stop anything you upload being corrupted, eg nc.exe.</span>

Windows XP add admin user

<pre>c:\&gt; net user /add rowbot rowbotpassword

c:\&gt; net localgroup administrators rowbot /add</pre>

I need to learn more about post exploitation, as in quickly grab anything of interest. If anyone can point me to a resource that would be great.

Now to start the write up for this box.<figure class="wp-block-image aligncenter">

<img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/08/happy-rowbot.png?w=680" alt="" class="wp-image-379" data-recalc-dims="1" /> </figure>