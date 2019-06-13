---
id: 90
title: 'Canape &#8211; HacktheBox'
date: 2018-05-10T20:29:35+01:00
author: rowbot
layout: post
guid: https://offsecnewbie.wordpress.com/?p=90
permalink: /2018/05/10/canape-hackthebox/
image: /wp-content/uploads/2018/05/canape.png
categories:
  - Hackthebox
---
START

First I did a standard nmap scan and the following ports were identified. Port 80 and ssh but on an obscure port instead of 22.

&nbsp;

<pre>[root:~/Desktop/canope]# nmap -sC -sV -p- 10.10.10.70 
Starting Nmap 7.70 ( https://nmap.org ) at 2018-04-28 13:24 BST
Stats: 0:00:00 elapsed; 0 hosts completed (0 up), 0 undergoing Script Pre-Scan 
NSE Timing: About 0.00% done 
Nmap scan report for 10.10.10.70 
Host is up, received echo-reply ttl 63 (0.028s latency). 
Not shown: 65533 filtered ports
Reason: 65533 no-responses 
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT STATE SERVICE REASON VERSION
80/tcp open http syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
| http-git: 
| 10.10.10.70:80/.git/ 
| Git repository found! 
| Repository description: Unnamed repository; edit this file 'description' to name the...
| Last commit message: final # Please enter the commit message for your changes. Li...
| Remotes: 
|_ http://git.canape.htb/simpsons.git 
|_http-server-header: Apache/2.4.18 (Ubuntu) 
|_http-title: Simpsons Fan Site
|_http-trane-info: Problem with XML parsing of /evox/about
65535/tcp open ssh syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
| 2048 8d:82:0b:31:90:e4:c8:85:b2:53:8b:a1:7c:3b:65:e1 (RSA)
| 256 22:fc:6e:c3:55:00:85:0f:24:bf:f5:79:6c:92:8b:68 (ECDSA) 
|_ 256 0d:91:27:51:80:5e:2b:a3:81:0d:e9:d8:5c:9b:77:35 (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel 
 
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 112.54 seconds</pre>

&nbsp;