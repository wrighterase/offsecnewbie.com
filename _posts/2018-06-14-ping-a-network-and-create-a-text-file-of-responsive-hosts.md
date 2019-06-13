---
id: 252
title: Ping a network and create a text file of alive hosts
date: 2018-06-14T12:46:17+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=252
permalink: /2018/06/14/ping-a-network-and-create-a-text-file-of-responsive-hosts/
image: /wp-content/uploads/2018/06/thought-2123970_960_720-500x250.jpg
categories:
  - Useful things
---
<img class="size-medium wp-image-264 aligncenter" src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/thought-2123970_960_720.jpg?resize=300%2C208" alt="" width="300" height="208" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/thought-2123970_960_720.jpg?resize=300%2C208 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/thought-2123970_960_720.jpg?resize=768%2C534 768w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/06/thought-2123970_960_720.jpg?w=960 960w" sizes="(max-width: 300px) 100vw, 300px" data-recalc-dims="1" />

This is to make it quick and easy to find machines that are on a network (that are responding to ICMP requests)

<pre>[root:~/Desktop]# fping -g $iprange -a &gt; ips.txt 2&gt; /dev/null</pre>

-g = generate a list

-a = only show alive hosts

2> /dev/null = output unresponsive to dev null instead of to terminal (std1)

In use:

<pre>[root:~/Desktop]# fping -g 10.194.0.0/24 -a &gt; ips.txt 2&gt; /dev/null
[root:~/Desktop]# cat ips.txt 
10.194.0.1
10.194.0.2
10.194.0.3
10.194.0.4
10.194.0.5
10.194.0.6
10.194.0.7
10.194.0.8
10.194.0.9
10.194.0.10
10.194.0.11
10.194.0.12
10.194.0.13
10.194.0.14
10.194.0.15
10.194.0.16
10.194.0.17
10.194.0.18
10.194.0.19
10.194.0.21
10.194.0.22
10.194.0.23
10.194.0.24
10.194.0.25
10.194.0.26
10.194.0.27
10.194.0.28
10.194.0.29
10.194.0.30
10.194.0.32
10.194.0.34
10.194.0.35
10.194.0.38
10.194.0.41
10.194.0.44
10.194.0.45
10.194.0.46
[root:~/Desktop]#</pre>

Once you have a text file you can do things like nmap scans from that file. Let me know what other things you would use this text file for.