---
id: 208
title: 'Valentine &#8211; HacktheBox'
date: 2018-05-21T11:07:54+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=208
permalink: /2018/05/21/valentine-hackthebox/
image: /wp-content/uploads/2018/05/valentine.png
categories:
  - Hackthebox
---
<pre>PORT STATE SERVICE VERSION</pre>

<pre>22/tcp open ssh OpenSSH 5.9p1 Debian 5ubuntu1.10 (Ubuntu Linux; protocol 2.0)</pre>

<pre>| ssh-hostkey:</pre>

<pre>| 1024 96:4c:51:42:3c:ba:22:49:20:4d:3e:ec:90:cc:fd:0e (DSA)</pre>

<pre>| 2048 46:bf:1f:cc:92:4f:1d:a0:42:b3:d2:16:a8:58:31:33 (RSA)</pre>

<pre>|_ 256 e6:2b:25:19:cb:7e:54:cb:0a:b9:ac:16:98:c6:7d:a9 (ECDSA)</pre>

<pre>80/tcp open http Apache httpd 2.2.22 ((Ubuntu))</pre>

<pre>| http-methods:</pre>

<pre>|_ Supported Methods: GET HEAD POST OPTIONS</pre>

<pre>|_http-server-header: Apache/2.2.22 (Ubuntu)</pre>

<pre>|_http-title: Site doesn't have a title (text/html).</pre>

<pre>443/tcp open ssl/http Apache httpd 2.2.22 ((Ubuntu))</pre>

<pre>| http-methods:</pre>

<pre>|_ Supported Methods: GET HEAD POST OPTIONS</pre>

<pre>|_http-server-header: Apache/2.2.22 (Ubuntu)</pre>

<pre>|_http-title: Site doesn't have a title (text/html).</pre>

<pre>| ssl-cert: Subject: commonName=valentine.htb/organizationName=valentine.htb/stateOrProvinceName=FL/countryName=US</pre>

<pre>| Issuer: commonName=valentine.htb/organizationName=valentine.htb/stateOrProvinceName=FL/countryName=US</pre>

<pre>| Public Key type: rsa</pre>

<pre>| Public Key bits: 2048</pre>

<pre>| Signature Algorithm: sha1WithRSAEncryption</pre>

<pre>| Not valid before: 2018-02-06T00:45:25</pre>

<pre>| Not valid after: 2019-02-06T00:45:25</pre>

<pre>| MD5: a413 c4f0 b145 2154 fb54 b2de c7a9 809d</pre>

<pre>|_SHA-1: 2303 80da 60e7 bde7 2ba6 76dd 5214 3c3c 6f53 01b1</pre>

<pre>|_ssl-date: 2018-05-21T09:32:33+00:00; -1s from scanner time.</pre>

<pre>No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).</pre>

Ran dirsearch and found a dev directory

<pre>[10:48:29] 301 - 308B - /dev -&gt; http://10.10.10.79/dev/</pre>

<pre>[10:48:29] 200 - 1KB - /dev/</pre>

<pre>[10:48:29] 403 - 283B - /doc/</pre>

<pre>[10:48:29] 403 - 298B - /doc/en/changes.html</pre>

<pre>[10:48:29] 403 - 297B - /doc/stable.version</pre>

<pre>[10:48:31] 200 - 38B - /index</pre>

<pre>[10:48:31] 200 - 38B - /index.php</pre>

<pre>[10:48:31] 200 - 38B - /index.php/login/</pre>

<pre>Task Completed</pre>

# Index of /dev

<table>
  <tr>
    <th>
      <img src="https://i0.wp.com/10.10.10.79/icons/blank.gif?w=680" alt="[ICO]" data-recalc-dims="1" />
    </th>
    
    <th>
      <a href="http://10.10.10.79/dev/?C=N;O=D">Name</a>
    </th>
    
    <th>
      <a href="http://10.10.10.79/dev/?C=M;O=A">Last modified</a>
    </th>
    
    <th>
      <a href="http://10.10.10.79/dev/?C=S;O=A">Size</a>
    </th>
    
    <th>
      <a href="http://10.10.10.79/dev/?C=D;O=A">Description</a>
    </th>
  </tr>
  
  <tr>
    <th colspan="5">
      <hr />
    </th>
  </tr>
  
  <tr>
    <td valign="top">
      <img src="https://i0.wp.com/10.10.10.79/icons/back.gif?w=680" alt="[DIR]" data-recalc-dims="1" />
    </td>
    
    <td>
      <a href="http://10.10.10.79/">Parent Directory</a>
    </td>
    
    <td>
    </td>
    
    <td align="right">
      &#8211;
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <img src="https://i1.wp.com/10.10.10.79/icons/unknown.gif?w=680" alt="[ ]" data-recalc-dims="1" />
    </td>
    
    <td>
      <a href="http://10.10.10.79/dev/hype_key">hype_key</a>
    </td>
    
    <td align="right">
      13-Dec-2017 16:48
    </td>
    
    <td align="right">
      5.3K
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <img src="https://i2.wp.com/10.10.10.79/icons/text.gif?w=680" alt="[TXT]" data-recalc-dims="1" />
    </td>
    
    <td>
      <a href="http://10.10.10.79/dev/notes.txt">notes.txt</a>
    </td>
    
    <td align="right">
      05-Feb-2018 16:42
    </td>
    
    <td align="right">
      227
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <th colspan="5">
      <hr />
    </th>
  </tr>
</table>

<address>
  Apache/2.2.22 (Ubuntu) Server at 10.10.10.79 Port 80
</address>

<address>
  Notes
</address>

<address>
  <pre>To do:

1) Coffee.
2) Research.
3) Fix decoder/encoder before going live.
4) Make sure encoding/decoding is only done client-side.
5) Don't use the decoder/encoder until any of this is done.
6) Find a better way to take notes.
</pre>
</address>