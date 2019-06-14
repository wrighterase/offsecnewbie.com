---
description: Discover as much about the target without revealing your IP address
---

# Passive OSINT

## Netcraft.com

Finds underlying OS, web server version uptime 

## Find subdomains

Sometimes SSL is a goldmine of information

```text
crt.sh
```

Example

```text
search
%.offsecnewbie.com
```

```text
crt.sh ID 	 Logged At  ⇧ 	Not Before 	Not After 	Identity 	Issuer Name
1376615351 	2019-04-11 	2019-04-09 	2020-04-09 	www.offsecnewbie.com 	C=US, ST=CA, L=San Francisco, O="CloudFlare, Inc.", CN=CloudFlare Inc ECC CA-2
1367205102 	2019-04-09 	2019-04-09 	2020-04-09 	www.offsecnewbie.com 	C=US, ST=CA, L=San Francisco, O="CloudFlare, Inc.", CN=CloudFlare Inc ECC CA-2
1018384237 	2018-12-10 	2018-12-06 	2019-12-06 	guide.offsecnewbie.com 	C=US, ST=CA, L=San Francisco, O="CloudFlare, Inc.", CN=CloudFlare Inc ECC CA-2
1007403529 	2018-12-06 	2018-12-06 	2019-12-06 	guide.offsecnewbie.com 	C=US, ST=CA, L=San Francisco, O="CloudFlare, Inc.", CN=CloudFlare Inc ECC CA-2
1003354525 	2018-12-05 	2018-12-05 	2019-03-05 	guide.offsecnewbie.com 	C=US, O=Let's Encrypt, CN=Let's Encrypt Authority X3
1003355062 	2018-12-05 	2018-12-05 	2019-03-05 	guide.offsecnewbie.com 	C=US, O=Let's Encrypt, CN=Let's Encrypt Authority X3
630174191 	2018-08-03 	2018-05-10 	2019-05-10 	www.offsecnewbie.com 	C=US, ST=CA, L=San Francisco, O="CloudFlare, Inc.", CN=CloudFlare Inc ECC CA-2
453490279 	2018-05-10 	2018-05-10 	2019-05-10 	www.offsecnewbie.com 	C=US, ST=CA, L=San Francisco, O="CloudFlare, Inc.", CN=CloudFlare Inc ECC CA-2
```

## Read

```text
https://www.bugcrowd.com/blog/discovering-subdomains/
```

FireFox addon - passive recon

## Google hacking

```text
examples here
site:offsecnewbie.com doctype:docx
inurl:/etc/passwd%00 intext:root
also check out doc meta info, ie doc creator, where doc was stored, created with Office 2010, saved on this network share eg its IP address
```

also heck out doc meta info, gives info such as where doc was stored - network share ip address, who created it, what was it created with etc 

```text
https://github.com/ElevenPaths/FOCA
```

