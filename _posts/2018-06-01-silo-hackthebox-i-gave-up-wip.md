---
id: 231
title: 'Silo &#8211; Hackthebox (I gave up/wip)'
date: 2018-06-01T15:50:11+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=231
permalink: /2018/06/01/silo-hackthebox-i-gave-up-wip/
image: /wp-content/uploads/2018/06/Selection_silo.png
categories:
  - Hackthebox
---
START

I decided to use Sn1per on this box to see what that tool was like. <https://github.com/1N3/Sn1per>

<pre><span style="color: #8ae234;">====================================================================================</span>                                                                                                                         
<span style="color: #ef2929;"> RUNNING TCP PORT SCAN </span>                                                                                                                                                                                      
<span style="color: #8ae234;">====================================================================================</span>                                                                                                                         
Starting Nmap 7.70SVN ( https://nmap.org ) at 2018-05-31 15:33 BST                                                                                                                                           
Nmap scan report for 10.10.10.82                                                                                                                                                                             
Host is up (0.035s latency).                                                                                                                                                                                 
Not shown: 467 closed ports, 1 filtered port                                                                                                                                                                 
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit                                                                                                                                  
PORT      STATE SERVICE                                                                                                                                                                                      
80/tcp    open  http                                                                                                                                                                                         
135/tcp   open  msrpc                                                                                                                                                                                        
139/tcp   open  netbios-ssn                                                                                                                                                                                  
445/tcp   open  microsoft-ds                                                                                                                                                                                 
1521/tcp  open  oracle                                                                                                                                                                                       
5985/tcp  open  wsman                                                                                                                                                                                        
47001/tcp open  winrm                                                                                                                                                                                        
49152/tcp open  unknown                                                                                                                                                                                      
                                                                                                                                                                                                             
Nmap done: 1 IP address (1 host up) scanned in 3.48 seconds                                                                                                                                                  
<span style="color: #8ae234;">====================================================================================</span>                                                                                                                         
<span style="color: #ef2929;"> RUNNING UDP PORT SCAN </span>                                                                                                                                                                                      
<span style="color: #8ae234;">====================================================================================</span>                                                                                                                         
Starting Nmap 7.70SVN ( https://nmap.org ) at 2018-05-31 15:33 BST                                                                                                                                           
Nmap scan report for 10.10.10.82                                                                                                                                                                             
Host is up (0.035s latency).                                                                                                                                                                                 
Not shown: 6 closed ports                                                                                                                                                                                    
PORT     STATE         SERVICE                                                                                                                                                                               
53/udp   open|filtered domain                                                                                                                                                                                
67/udp   open|filtered dhcps                                                                                                                                                                                 
68/udp   open|filtered dhcpc                                                                                                                                                                                 
88/udp   open|filtered kerberos-sec                                                                                                                                                                          
123/udp  open|filtered ntp                                                                                                                                                                                   
137/udp  open|filtered netbios-ns                                                                                                                                                                            
138/udp  open|filtered netbios-dgm                                                                                                                                                                           
2049/udp open|filtered nfs                                                                                                                                                                                   
                                                                                                                                                                                                             
Nmap done: 1 IP address (1 host up) scanned in 6.56 seconds                                                                                                                                                  
</pre>

Presents the info quite clearly it does state version infor later on but I just didnt want to hit you with a wall of output. The webserver didnt give anything away even after using dirbuster. Letsee what I can find out about the Oracle service. To look for oracle nmap scripts I used the following:

<pre>[<span style="color: #8ae234;"><b>root</b></span>:<span style="color: #cc0000;">~/Desktop/silo</span>]# locate .nse | grep oracle                                                                                                                                                  (<span style="color: #fce94f;"><b>master</b></span><span style="color: #ef2929;"><b>✱</b></span>) 
/usr/share/nmap/scripts/<span style="color: #ef2929;"><b>oracle</b></span>-brute-stealth.nse
/usr/share/nmap/scripts/<span style="color: #ef2929;"><b>oracle</b></span>-brute.nse
/usr/share/nmap/scripts/<span style="color: #ef2929;"><b>oracle</b></span>-enum-users.nse
/usr/share/nmap/scripts/<span style="color: #ef2929;"><b>oracle</b></span>-sid-brute.nse
/usr/share/nmap/scripts/<span style="color: #ef2929;"><b>oracle</b></span>-tns-version.nse
[<span style="color: #8ae234;"><b>root</b></span>:<span style="color: #cc0000;">~/Desktop/silo</span>]
</pre>

I tried to run oracle-tns-version.nse but it didnt work for some reason &#8211; it just gave me a default nmap scan. Instead I ran the following:

<pre>[<span style="color: #8ae234;"><b>root</b></span>:<span style="color: #cc0000;">~/Desktop/silo</span>]# nmap -p 1521 -A 10.10.10.82                                                                                                                                                (<span style="color: #fce94f;"><b>master</b></span><span style="color: #ef2929;"><b>✱</b></span>)
Starting Nmap 7.70SVN ( https://nmap.org ) at 2018-06-01 14:38 BST
Nmap scan report for 10.10.10.82
Host is up, received reset ttl 127 (0.031s latency).

PORT     STATE SERVICE    REASON          VERSION
1521/tcp open  oracle-tns syn-ack ttl 127 <span style="color: #00ff00;">Oracle TNS listener 11.2.0.2.0 (unauthorized)</span>
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Microsoft Windows Server 2012 (96%), Microsoft Windows Server 2012 R2 (96%), Microsoft Windows Server 2012 R2 Update 1 (96%), Microsoft Windows 7, Windows Server 2012, or Windows 8.1
 Update 1 (96%), Microsoft Windows Server 2012 or Server 2012 R2 (95%), Microsoft Windows Vista SP1 (95%), Microsoft Windows Server 2008 SP2 Datacenter Version (94%), Microsoft Windows Server 2008 R2 (93%)
, Microsoft Windows Home Server 2011 (Windows Server 2008 R2) (93%), Microsoft Windows Server 2008 SP1 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
</pre>

I&#8217;ve highlighted the version number in green. OK good, now lets see what else I can find out from the nmap scripts

<pre>[<span style="color: #8ae234;"><b>root</b></span>:<span style="color: #cc0000;">~/Desktop/silo</span>]#  nmap --script=oracle-sid-brute -p 1521 10.10.10.82                                                                                                                        (<span style="color: #fce94f;"><b>master</b></span><span style="color: #ef2929;"><b>✱</b></span>)

Starting Nmap 7.70SVN ( https://nmap.org ) at 2018-06-01 14:39 BST
Nmap scan report for 10.10.10.82
Host is up, received reset ttl 127 (0.031s latency).

PORT     STATE SERVICE REASON
1521/tcp open  oracle  syn-ack ttl 127
| oracle-sid-brute:
|_  <span style="color: #00ff00;">XE</span>

Nmap done: 1 IP address (1 host up) scanned in 46.78 seconds
</pre>

So we have an sid ( whatever that is )

<pre>[<span style="color: #8ae234;"><b>root</b></span>:<span style="color: #cc0000;">~/Desktop/silo</span>]# nmap --script=oracle-enum-users -p 1521 10.10.10.82 -vvv                                                                                                                   (<span style="color: #fce94f;"><b>master</b></span><span style="color: #ef2929;"><b>✱</b></span>) 
Starting Nmap 7.70SVN ( https://nmap.org ) at 2018-06-01 14:49 BST
NSE: Loaded 1 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 14:49
Completed NSE at 14:49, 0.00s elapsed
Initiating Ping Scan at 14:49
Scanning 10.10.10.82 [4 ports]
Completed Ping Scan at 14:49, 0.23s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 14:49
Completed Parallel DNS resolution of 1 host. at 14:49, 0.00s elapsed
DNS resolution of 1 IPs took 0.00s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 14:49
Scanning 10.10.10.82 [1 port]
Discovered open port 1521/tcp on 10.10.10.82
Completed SYN Stealth Scan at 14:49, 0.34s elapsed (1 total ports)
NSE: Script scanning 10.10.10.82.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 14:49
Completed NSE at 14:49, 0.00s elapsed
Nmap scan report for 10.10.10.82
Host is up, received echo-reply ttl 127 (0.030s latency).
Scanned at 2018-06-01 14:49:41 BST for 1s

PORT     STATE SERVICE REASON
1521/tcp open  oracle  syn-ack ttl 127

<span style="color: #00ff00;">NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 14:49
Completed NSE at 14:49, 0.00s elapsed</span>
Read data files from: /usr/local/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.97 seconds
           Raw packets sent: 6 (240B) | Rcvd: 2 (72B)
[<span style="color: #8ae234;"><b>root</b></span>:<span style="color: #cc0000;">~/Desktop/silo</span>]#</pre>

It looks as if the script ran &#8211; but I&#8217;m not 100% as it hasn&#8217;t given me an indication of users.

I was getting a bit frustrated here so I fireup Metasploit, looked for modules and ran a few. I found this:

<pre><u>msf</u> auxiliary(<span style="color: #ef2929;"><b>scanner/oracle/tnspoison_checker</b></span>) &gt; run

<span style="color: #8ae234;"><b>[+]</b></span> [2018.06.01-14:58:47] 10.10.10.82:1521 - 10.10.10.82:1521 is vulnerable
<span style="color: #729fcf;"><b>[*]</b></span> [2018.06.01-14:58:47] Scanned 1 of 1 hosts (100% complete)
<span style="color: #729fcf;"><b>[*]</b></span> Auxiliary module execution completed
<u>msf</u> auxiliary(<span style="color: #ef2929;"><b>scanner/oracle/tnspoison_checker</b></span>) &gt; 
</pre>

I have to admit I had to check the hackthebox forums for hints at this point. Someone people were saying to install odat <https://github.com/quentinhardy/odat> and it was not easy to install. I gave up. ODAT (Oracle Database Attacking Tool) is an open source penetration testing tool that tests the security of Oracle Databases remotely. Why is this not installed in Kali by default!? I can&#8217;t be bothered registering with oracle to download the tool then run complicated configuration commands. FU Silo, Oracle and Kali. Only joking Kali I cant stay mad at you.