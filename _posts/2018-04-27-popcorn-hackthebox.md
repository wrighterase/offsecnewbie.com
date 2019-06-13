---
id: 39
title: 'Popcorn &#8211; HacktheBox'
date: 2018-04-27T23:15:02+01:00
author: rowbot
layout: post
guid: https://offsecnewbie.wordpress.com/?p=39
permalink: /2018/04/27/popcorn-hackthebox/
publicize_linkedin_url:
  - 'https://www.linkedin.com/updates?discuss=&scope=566107844&stype=M&topic=6395772131031937024&type=U&a=Zk3x'
timeline_notification:
  - "1524870903"
image: /wp-content/uploads/2018/04/popcorn-1.png
categories:
  - Hackthebox
---
Work in Progress

START

Port scan to identify open ports

<pre>[root:~/Desktop/popcorn]# nmap -p- 10.10.10.6 -oA ports
Starting Nmap 7.70 ( https://nmap.org ) at 2018-04-11 12:25 BST
Nmap scan report for 10.10.10.6
Host is up, received timestamp-reply ttl 63 (0.029s latency).
Not shown: 65488 closed ports, 45 filtered ports 
Reason: 65488 resets and 45 no-responses
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT STATE SERVICE REASON 
22/tcp open ssh syn-ack ttl 63 
80/tcp open http syn-ack ttl 63
 
Nmap done: 1 IP address (1 host up) scanned in 13.16 seconds</pre>

Port 22 and 80 open &#8211; now for detailed scan

<pre>nmap -sC -sV -p22,80 -oA detailedportscan 10.10.10.6
Starting Nmap 7.70 ( https://nmap.org ) at 2018-04-11 12:32 BST
Nmap scan report for 10.10.10.6 
Host is up, received reset ttl 63 (0.041s latency).
 
PORT STATE SERVICE REASON VERSION
22/tcp open ssh syn-ack ttl 63 OpenSSH 5.1p1 Debian 6ubuntu2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
| 1024 3e:c8:1b:15:21:15:50:ec:6e:63:bc:c5:6b:80:7b:38 (DSA)
|_ 2048 aa:1f:79:21:b8:42:f4:8a:38:bd:b8:05:ef:1a:07:4d (RSA)
80/tcp open http syn-ack ttl 63 Apache httpd 2.2.12 ((Ubuntu))
|_http-server-header: Apache/2.2.12 (Ubuntu) 
|_http-title: Site doesn't have a title (text/html).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel 
 
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.86 seconds</pre>

Looks like an  old version of ssh &#8211; normally I see 7.2 on there. Lets take a quick peak at what directories there are

<pre>[root:~/dirsearch]# ./dirsearch.py -u 10.10.10.6 -e php (master)

_|. _ _ _ _ _ _|_ v0.3.8
(_||| _) (/_(_|| (_| )

Extensions: php | Threads: 10 | Wordlist size: 5961

Error Log: /root/dirsearch/logs/errors-18-04-11_12-37-28.log

Target: 10.10.10.6

[12:37:28] Starting: 
[12:37:29] 403 - 289B - /.ht_wsr.txt
[12:37:29] 403 - 282B - /.hta
[12:37:29] 403 - 291B - /.htaccess-dev
[12:37:29] 403 - 293B - /.htaccess-local
[12:37:29] 403 - 293B - /.htaccess-marco
[12:37:29] 403 - 291B - /.htaccess.BAK
[12:37:29] 403 - 292B - /.htaccess.bak1
[12:37:29] 403 - 291B - /.htaccess.old
[12:37:29] 403 - 292B - /.htaccess.orig
[12:37:29] 403 - 294B - /.htaccess.sample
[12:37:29] 403 - 292B - /.htaccess.save
[12:37:29] 403 - 291B - /.htaccess.txt
[12:37:29] 403 - 293B - /.htaccess_extra
[12:37:29] 403 - 292B - /.htaccess_orig
[12:37:29] 403 - 290B - /.htaccess_sc
[12:37:29] 403 - 290B - /.htaccessBAK
[12:37:29] 403 - 290B - /.htaccessOLD
[12:37:29] 403 - 291B - /.htaccessOLD2
[12:37:29] 403 - 288B - /.htaccess~
[12:37:29] 403 - 286B - /.htgroup
[12:37:29] 403 - 291B - /.htpasswd-old
[12:37:29] 403 - 292B - /.htpasswd_test
[12:37:29] 403 - 288B - /.htpasswds
[12:37:29] 403 - 286B - /.htusers
[12:37:36] 403 - 286B - /cgi-bin/
[12:37:38] 403 - 282B - /doc/
[12:37:38] 403 - 297B - /doc/en/changes.html
[12:37:38] 403 - 296B - /doc/stable.version
[12:37:40] 200 - 177B - /index
[12:37:40] 200 - 177B - /index.html
[12:37:47] 200 - 48KB - /test
[12:37:47] 200 - 47KB - /test.php
[12:37:47] 200 - 48KB - /test/
[12:37:55] 403 - 291B - /server-status
[12:37:55] 403 - 292B - /server-status/

Task Completed
[root:~/dirsearch]#</pre>

Nothing really revealing after browsing the webpages. It revealed the version of php thats running on the server and the OS

<pre>PHP Version 5.2.10-2ubuntu6.10</pre>

&nbsp;