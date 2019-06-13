---
id: 487
title: 'Irked &#8211; HacktheBox'
date: 2018-11-27T12:36:21+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=487
permalink: /2018/11/27/irked-hackthebox/
image: /wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-55-08.png
categories:
  - Hackthebox
tags:
  - hackthebox
  - irked
---
I learned about SUID with this box. The user access I found easy, I think I got user in under 10 minutes &#8211; that&#8217;s a first for me. The PE part took me sometime, which a few nudges!  


**Skills Required**  
SUID knowledge  
  


**Skills Learned**  
Searching for sticky bits  
Understanding a bit more about standard linux binaries  
Adding echo command to a file to see if it executes it.  
  


<pre class="wp-block-preformatted">START<br /></pre>

nmap -sC -sV -oA all -vv -p- 10.10.10.117<figure class="wp-block-image">

<img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-09-41-00.png?w=680" alt="" class="wp-image-528" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-09-41-00.png?w=938 938w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-09-41-00.png?resize=300%2C125 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-09-41-00.png?resize=768%2C320 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> <figcaption>nmap imported into pentest.ws</figcaption></figure> 

I see that there is an IRC server and take took a look for a metasploit module.  
<figure class="wp-block-image">

<img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-23-44.png?fit=680%2C139" alt="" class="wp-image-493" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-23-44.png?w=1571 1571w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-23-44.png?resize=300%2C61 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-23-44.png?resize=768%2C157 768w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-23-44.png?resize=1024%2C209 1024w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-23-44.png?w=1360 1360w" sizes="(max-width: 680px) 100vw, 680px" /> <figcaption>metasploit search on pentest.ws</figcaption></figure> <figure class="wp-block-image"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-27-59.png?w=680" alt="" class="wp-image-497" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-27-59.png?w=589 589w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-27-59.png?resize=300%2C172 300w" sizes="(max-width: 589px) 100vw, 589px" data-recalc-dims="1" /><figcaption>set RHOST and RPORT</figcaption></figure> <figure class="wp-block-image"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-29-53.png?w=680" alt="" class="wp-image-500" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-29-53.png?w=941 941w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-29-53.png?resize=300%2C125 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-29-53.png?resize=768%2C321 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /><figcaption>have shell as ircd &#8211; need to upgrade shell.</figcaption></figure> <figure class="wp-block-image"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-30-47.png?w=680" alt="" class="wp-image-501" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-30-47.png?w=361 361w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-30-47.png?resize=277%2C300 277w" sizes="(max-width: 361px) 100vw, 361px" data-recalc-dims="1" /><figcaption>I chose python 2&nbsp; and upgraded to a better shell</figcaption></figure> 

After upgrading the shell I browsed to the home directory to see what users there were. I seen there was ircd &#8211; which I was logged in as and djmardov. Traversing into the djmardov user there were a few files &#8211; laid out in the Windows style  


<pre class="wp-block-preformatted">ircd@irked:/home/djmardov$ ls <br />ls <br />Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos<br />ircd@irked:/home/djmardov$ </pre>

I ran ls -laR to search for files within all the directories in djmardov and found the user.txt file as well as another hidden file called ._backup_.  
<figure class="wp-block-image">

<img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-37-47.png?w=680" alt="" class="wp-image-502" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-37-47.png?w=536 536w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-37-47.png?resize=300%2C228 300w" sizes="(max-width: 536px) 100vw, 536px" data-recalc-dims="1" /> </figure> 

I tried to view user.txt but couldnt &#8211; I&#8217;m not the user djmardov.  


<pre class="wp-block-preformatted">ircd@irked:/home/djmardov/Documents$ cat user.txt<br />cat user.txt<br />cat: user.txt: Permission denied<br />ircd@irked:/home/djmardov/Documents$ </pre>

I checked to see what was in the contents of .backup.txt  


<pre class="wp-block-preformatted">ircd@irked:/home/djmardov/Documents$ cat .backup<br />cat .backup<br />Super elite steg backup pw<br />UPupDOWNdownLRlrBAbaSSss</pre>

Humm so I have a steg password for something &#8211; probably an image.  


Back to review the nmap scan and see there is a http server.  
<figure class="wp-block-image">

<img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-42-53.png?w=680" alt="" class="wp-image-505" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-42-53.png?w=786 786w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-42-53.png?resize=300%2C15 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-42-53.png?resize=768%2C37 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> </figure> 

It reveals an image &#8211; I guessed that this is the image that the steg password is for.  
<figure class="wp-block-image">

<img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-42-25.png?fit=680%2C471" alt="" class="wp-image-506" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-42-25.png?w=1033 1033w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-42-25.png?resize=300%2C208 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-42-25.png?resize=768%2C532 768w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-42-25.png?resize=1024%2C709 1024w" sizes="(max-width: 680px) 100vw, 680px" /> <figcaption>image on homepage</figcaption></figure> 

I downloaded the image to kali  
<figure class="wp-block-image">

<img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-45-49.png?w=680" alt="" class="wp-image-507" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-45-49.png?w=583 583w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-45-49.png?resize=300%2C116 300w" sizes="(max-width: 583px) 100vw, 583px" data-recalc-dims="1" /> <figcaption>I notice that the file name is irked &#8211; adding to my suspicions that this is the image that i need to decode</figcaption></figure> 

For some reason steghide is not installed by default in Kali so I did an apt-get install steghide to install it. Following the help page of it I extracted the data from the file as shown below.  
<figure class="wp-block-image">

<img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-50-03.png?w=680" alt="" class="wp-image-508" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-50-03.png?w=660 660w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-50-03.png?resize=300%2C60 300w" sizes="(max-width: 660px) 100vw, 660px" data-recalc-dims="1" /> <figcaption>captured another password &#8211; probably for the user djmardov</figcaption></figure> <figure class="wp-block-image"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-45-19.png?w=680" alt="" class="wp-image-525" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-45-19.png?w=803 803w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-45-19.png?resize=300%2C37 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-45-19.png?resize=768%2C95 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /><figcaption>added creds to pentest.ws</figcaption></figure> 

I ssh&#8217;d in as dj  
<figure class="wp-block-image">

<img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-51-39.png?w=680" alt="" class="wp-image-509" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-51-39.png?w=668 668w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-16-51-39.png?resize=300%2C110 300w" sizes="(max-width: 668px) 100vw, 668px" data-recalc-dims="1" /> </figure> 

<pre class="wp-block-preformatted">djmardov@irked:~$ cat Documents/user.txt <br />4a66a78b12dc0e661a59d3f5c0267a8e<br />djmardov@irked:~$ </pre>

The PE part took me a lot of time and I needed a few nudges. I&#8217;ll go through some my thoughts, some commands I used and what I was looking for.  


<pre class="wp-block-preformatted">djmardov@irked:/tmp$ sudo -l<br />-bash: sudo: command not found<br />djmardov@irked:/tmp$ </pre>

Test to see what sudo command djmardov can run. He has not been given access to sudo. Sometimes you get lucky with this PE route.  
<figure class="wp-block-image">

<img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-14-42.png?fit=680%2C237" alt="" class="wp-image-517" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-14-42.png?w=1028 1028w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-14-42.png?resize=300%2C104 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-14-42.png?resize=768%2C267 768w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-14-42.png?resize=1024%2C357 1024w" sizes="(max-width: 680px) 100vw, 680px" /> <figcaption>nothing of note running as root</figcaption></figure> <figure class="wp-block-image"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-30-12.png?w=680" alt="" class="wp-image-518" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-30-12.png?w=806 806w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-30-12.png?resize=300%2C31 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-30-12.png?resize=768%2C80 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /><figcaption>`What is the server's Architecture cat&nbsp;/proc/version;&nbsp;uname&nbsp;-a;&nbsp;uname&nbsp;-mrs;&nbsp;rpm&nbsp;-q&nbsp;kernel;&nbsp;dmesg&nbsp;|&nbsp;grep&nbsp;Linux;&nbsp;ls&nbsp;/boot&nbsp;|&nbsp;grep&nbsp;vmlinuz-;&nbsp;file&nbsp;/bin/ls;&nbsp;cat&nbsp;/etc/lsb-release`</figcaption></figure> <figure class="wp-block-image"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-33-02.png?fit=680%2C258" alt="" class="wp-image-519" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-33-02.png?w=1576 1576w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-33-02.png?resize=300%2C114 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-33-02.png?resize=768%2C292 768w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-33-02.png?resize=1024%2C389 1024w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-33-02.png?w=1360 1360w" sizes="(max-width: 680px) 100vw, 680px" /><figcaption>Find apps installed;  
`ls&nbsp;-alh&nbsp;/usr/bin/;&nbsp;ls&nbsp;-alh&nbsp;/sbin/;&nbsp;dpkg&nbsp;-l;&nbsp;rpm&nbsp;-qa;&nbsp;ls&nbsp;-alh&nbsp;/var/cache/apt/archivesO;&nbsp;ls&nbsp;-alh&nbsp;/var/cache/yum/*;`  
  
nothing looks out of place here &#8211; checking versions of some apps.</figcaption></figure> <figure class="wp-block-image"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-35-53.png?w=680" alt="" class="wp-image-522" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-35-53.png?w=586 586w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-35-53.png?resize=300%2C22 300w" sizes="(max-width: 586px) 100vw, 586px" data-recalc-dims="1" /><figcaption>`Looking for&nbsp;`writeable config files &#8211; none found  
 `find&nbsp;/etc/&nbsp;-writable&nbsp;-type&nbsp;f&nbsp;2>/dev/null`</figcaption></figure> <figure class="wp-block-image"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-41-15.png?w=680" alt="" class="wp-image-523" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-41-15.png?w=581 581w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-26-20-41-15.png?resize=242%2C300 242w" sizes="(max-width: 581px) 100vw, 581px" data-recalc-dims="1" /><figcaption>What Scheduled jobs? &#8211; None  
`crontab&nbsp;-l;&nbsp;ls&nbsp;-alh&nbsp;/var/spool/cron;&nbsp;ls&nbsp;-al&nbsp;/etc/&nbsp;|&nbsp;grep&nbsp;cron;&nbsp;ls&nbsp;-al&nbsp;/etc/cron*;&nbsp;cat&nbsp;/etc/cron*;&nbsp;cat&nbsp;/etc/at.allow;&nbsp;cat&nbsp;/etc/at.deny;&nbsp;cat&nbsp;/etc/cron.allow;&nbsp;cat&nbsp;/etc/cron.deny`</figcaption></figure> <figure class="wp-block-image"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-09-55-03.png?fit=680%2C60" alt="" class="wp-image-530" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-09-55-03.png?w=1119 1119w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-09-55-03.png?resize=300%2C27 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-09-55-03.png?resize=768%2C68 768w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-09-55-03.png?resize=1024%2C91 1024w" sizes="(max-width: 680px) 100vw, 680px" /><figcaption>check to see if the system saves any history &#8211; maybe capture a password or username or interesting file in there &#8211; no luck  
  
`cat&nbsp;~/.bash_history;&nbsp;cat&nbsp;~/.nano_history;&nbsp;cat&nbsp;~/.atftp_history;&nbsp;cat&nbsp;~/.mysql_history;&nbsp;cat&nbsp;~/.php_history`</figcaption></figure> <figure class="wp-block-image"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/imageedit_2_2236803244.png?w=680" alt="" class="wp-image-531" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/imageedit_2_2236803244.png?w=1020 1020w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/imageedit_2_2236803244.png?resize=300%2C117 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/imageedit_2_2236803244.png?resize=768%2C300 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /><figcaption>Look for binaries with the SUID or GUID bits set.  
  
`find&nbsp;/&nbsp;-perm&nbsp;-g=s&nbsp;-o&nbsp;-perm&nbsp;-4000&nbsp;!&nbsp;-type&nbsp;l&nbsp;-maxdepth&nbsp;6&nbsp;-exec&nbsp;ls&nbsp;-ld&nbsp;{}&nbsp;\;&nbsp;2>/dev/null`</figcaption></figure> 

What I initially noticed was the date of /usr/bin/viewuser- it was 2018 meaning recently modified. I copied and pasted the other commands in to see what would happen. I also ran the command on my Kali machine  
<figure class="wp-block-image">

<img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-10-42-31.png?fit=680%2C328" alt="" class="wp-image-534" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-10-42-31.png?w=1061 1061w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-10-42-31.png?resize=300%2C145 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-10-42-31.png?resize=768%2C371 768w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-10-42-31.png?resize=1024%2C494 1024w" sizes="(max-width: 680px) 100vw, 680px" /> <figcaption>I compared it to my Kali machine as it is Debian based. Again viewuser stood out so requires more investigation&nbsp;</figcaption></figure> <figure class="wp-block-image"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-19-47.png?w=680" alt="" class="wp-image-535" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-19-47.png?w=618 618w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-19-47.png?resize=300%2C55 300w" sizes="(max-width: 618px) 100vw, 618px" data-recalc-dims="1" /><figcaption>running the binary says its looking for /tmp/listusers and can&#8217;t find it.</figcaption></figure> <figure class="wp-block-image"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-21-19.png?fit=680%2C177" alt="" class="wp-image-536" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-21-19.png?w=1070 1070w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-21-19.png?resize=300%2C78 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-21-19.png?resize=768%2C200 768w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-21-19.png?resize=1024%2C266 1024w" sizes="(max-width: 680px) 100vw, 680px" /><figcaption>contents of /tmp</figcaption></figure> <figure class="wp-block-image"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-22-29.png?w=680" alt="" class="wp-image-537" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-22-29.png?w=706 706w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-22-29.png?resize=300%2C64 300w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /><figcaption>I created listusers with &#8216;echo this is a test&#8217; inside it and ran. Permission denied. Meaning it found the file but obviously doesn&#8217;t have the permission to run it. I need to chmod it.</figcaption></figure> <figure class="wp-block-image"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-24-22.png?w=680" alt="" class="wp-image-538" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-24-22.png?w=631 631w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-24-22.png?resize=300%2C73 300w" sizes="(max-width: 631px) 100vw, 631px" data-recalc-dims="1" /><figcaption>It worked! but what permissions does this file run as. Added whoami to the file</figcaption></figure> <figure class="wp-block-image"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-25-32.png?w=680" alt="" class="wp-image-539" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-25-32.png?w=635 635w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-25-32.png?resize=300%2C72 300w" sizes="(max-width: 635px) 100vw, 635px" data-recalc-dims="1" /><figcaption>Success! It is running as root. Now to capture root.txt</figcaption></figure> <figure class="wp-block-image"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-26-49.png?w=680" alt="" class="wp-image-540" data-recalc-dims="1" /></figure> <figure class="wp-block-image"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-27-00.png?w=680" alt="" class="wp-image-541" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-27-00.png?w=617 617w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/Screenshot-from-2018-11-27-11-27-00.png?resize=300%2C80 300w" sizes="(max-width: 617px) 100vw, 617px" data-recalc-dims="1" /><figcaption>root captured</figcaption></figure> 

You could get that binary to execute a reverse shell with root access.

I learned some important stuff with this box. Check for sticky bits and look for binaries which are not standard in that version of linux. Also if your not sure what file is doing, add and echo&nbsp; command to see if it executes it.