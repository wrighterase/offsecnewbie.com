# Web

## Reconnaissance

{% page-ref page="test-group/automated-tools.md" %}

## Active Info Gathering

## DNS Enumeration

```text
host -t ns $website #gets nameservers
host -t mx $website #gets mail servers
```

### Forward DNS Lookup Bruteforce

{% hint style="info" %}
guess valid names of servers by attempting to resolve a given name
{% endhint %}

Get possible IP range from above to discover more hostnames and ip addresses belonging to $website using below

```text
for i in $(cat /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100K.txt); do host $i.$website | grep "has address" | cut -d" " -f1 ;done
```

### Reverse DNS Lookup Bruteforce

{% hint style="info" %}
might find missing domain names in the forward lookup by probing the IP address range.
{% endhint %}

```text
for ip in $(seq 1 10); do host $199.199.199.$ip > network.txt; cat network.txt | grep "$website" | cut -d" " -f5; done #need to change some variables
```

### DNS Zone Transfer

{% hint style="info" %}
Like a database replication act between dns servers.   
Should be limited to authorised secondary dns servers - misconfigured will allow zone transfer!   
Can give complete map of internal and external network structure
{% endhint %}

```text
#!/bin/bash
#identifies name servers and attempts zone transfer on each of them 
if [ -z "$1" ]; then
echo "[*] Zone Transfer Script"
echo "[*] Usage: $0 <domain name>"
exit 0
fi

for server in $(host -t ns $1 | cut -d" " -f4);do

host -l $1 $server | grep "has address"
done
```

```text
test using 
./$scriptname zonetransfer.me
```

```text
dnsrecon -d $website -t axfr
```

Is target protected by WAF?  
may stop responding if probed too hard

* Map the attack surface
* Find subdomains, Ip blocks, email addresses, the harvester is a nice tool. I.e: **python theHarvester.py -d example.org -n -c -t -b google**
* There is a nice collection of OSINT tools at **http://osintframework.com/** feed the harvester results there and recurse.
* Find services, Banners and versions. Research CVEs and **exploit-db** for those.
* Find newest features, and forgotten endpoints at **https://web-beta.archive.org**
* Check **robots.txt** **crossdomain.xml** and **clientaccesspolicy.xml**
* Find hosts:

  ```text
  dig $website a; @8.8.8.8 # types: a, mx, ns, soa, srv, txt, axfr
  ```

  ```text
  dig -x $website # reverse lookup
  ```

* Google **site:** to find information leakage
* Map their infrastructure: middleware, programming languages, backends, services. This can help **https://wappalyzer.com/**
* Find hidden folders, files. Nice list for fuzzing content discovery: **https://c.darenet.org/nitemare/SecLists/tree/master/Discovery/Web\_Content**

  ```text
  dirb http://target wordlists/dirb/common.txt
  ```

  ```text
  nikto -host http://target
  ```

* Spider/map all the functionalities of the application, discover hidden & default content, doing automated and manual crawling.
* Identify data entry points, technologies used. What does the application do? How does it do it? Map attack surface, dangerous functionalities, how they are implemented. Versions of the libraries, frameworks and known CVEs.
* Read the client code of the web app, what javascript libraries it use, code looks messy?, sinks, etc
* Check comments in source of all pages
* Generate an error page, sometimes vulnerable to XSS.
* Identify all parameters. Document which parameters are used for **GET** and **POST**.
* Identify where cookies are set, modified or added to.
* Note any strange headers

  ```text
  ./whatweb $website 
  ```

  ```text
  dirb https://$website /usr/share/wordlists/dirb/common.txt 
  ```

* Use Shodan for finding similar apps and endpoints, SSH hash keys
* Find previous vulnerabilities of the web site. Recon-ng is a useful tool; **use recon/domains-vulnerabilities/xssposed; set source example.org; run**
* RTFM - Read the manual for the application you are testing, does it have a dev mode? is there a **DEBUG=TRUE** flag that can be flipped to see more?
* Look for where you can put data, is it an API? Is there a paywall or sign up? Is it purely unauthenticated?
* Look at the application from a bad guy perspective, what does it do? what is the most valuable part? Some applications will value things more than others, for example a premium website might be more concerned about users being able to bypass the pay wall than they are of say cross-site scripting.
* Look at the application logic too, how is business conducted?
* If testing a bug bounty, look for new acquisition, code from new team, new mobile apps versions, new UI in web, new features.

## Testing input validation

* Append **.old** or **.bak** to files
* Run automated scanning against web app, Burp, nikto and dirb.
* Use wpscan to assess wordpress plugins
* Use cmsmap for durpal and joomla known bugs
* Flashbang to decode swf files, online tool
* Find parameters being **reflected** and test for: **XSS**, **HPP**, **link manipulation**, **template injection**.
* Test server side issues \(error-based, blind, outband, stored, different context\(numeric, single and double quoted\)\) such as: **SQL injection**, **Server-side include**, **OS command injection**, **path traversal**, **file inclusion**both local and remote, **SMTP injection**, **SOAP injection** get the application to respond to SOAP, this ties into XXE attacks too. **LDAP injection**, **XPath injection**, **code injection**, **deserialization** attacks, **overflow** attacks.
* A parameter looks like a file? Test **path traversal**, **RFI**, **LFI**
* A parameter looks like a URL? Test **open redirection** **SSRF**
* Parsing of XML, JSON, or any other markup language that the application processes. Test for **injection attacks**, **SSRF**, **xpath**, **XXE**, **insecure object de-references**.
* Look for parameters encoded in base64 or others, test again for injection attacks and insecure object de-references.
* Check for DOM-based attacks - open redirection, cross site scripting, client side validation.
* File uploads. SVG can have embedded XML that triggers SSRF, XXE.
* If user has profile and avatar, upload a malicious SVG with script.

## Server issues

* Script to request every https request to http
* Test **header injection**
* Test HTTP Options, use arbitrary method names to attempt to bypass authentication pages
* Test any client side applet such as flash, activex and silverlight.
* For file uploads functionalities, look for reflected file download. Uploading files with double extensions \(.php5.jpeg\) and using a null byte \(.php5%00.jpeg\)
* Ensure anti-CSRF mitigations are in place for main functionalities and clickjacking mitigations.
* If there is a binary, and runs as root, it should use https only and verify checksum or singed check with public key
* Captcha bypassing
* Check for frame injection, frame busting\(can still be an issue\)
* Caching poisoning issues
* Sensitive data in URL parameters
* Follow up any information leakage
* Look at numeric IDs, they can tell you much many orders, users etc. Looked for hashed numeric ids
* Look swf they are always vulnerable
* Check for weak SSL ciphers
* Test **CORS policy**. if CORS or crosssdomain.xml allow subdomain, you can trick a user into doing XSS to that page by injecting an iframe to all web pages he visits to a subdomain i.e. sub.vulnerable.com, intercepting all requests for that host and returning html that will issue a cross domain request to vulnerable.es and display it to the UI.
* Verify Content Security Policy \(CSP\). Look for bypasses
* Verify HTTP Strict Transport Security \(HSTS\)
* Verify X-XSS-Protection
* Verify X-Content-Type-Options
* Verify HTTP Public Key Pinning

## Testing authentication

* Burp extension to see what users can see \(authorization\)
* basic auth brute force:

  ```text
  nmap -d -vv -p 80 --script http-brute --script-args http-brute.path=/ $website
  ```

* Password quality rules, length, character set allowed \(alphanumeric, upper/lower case and special characters\). Empty Password? Empty username? 123456?
* Test username enumeration
* Test account recovery functionality, look for SMTP header injection.
* Does remember me expires?
* Test removing your email address from your account, add a new one, make sure that the old one can not be used to recover password/log in.
* Delete an account without entering password or other sensitive operations, in case you forgot your computer logged in.
* Password bruteforcing resilience. Application locks after some attempts?
* Rate limiting in change password functionality, forgot to log out in a cyber cafe, brute force the actual password using this feature. Does the application lock out an account after x number of login attempts?
* Email verification links through http
* Cookies: scope, httponly, secure flag.
* Broken OAuth authentication, make sure ID tokens generated by google or third party are properly validated on the backend. **https://developers.google.com/identity/sign-in/web/backend-auth\#verify-the-integrity-of-the-id-token**
* Other strange access control methods such as referral validation \(which can be bypassed **https://t.co/z84ajd7bmO**\)
* Does the remember me function ever expire? Is there room for exploit-ability in cookies combined with other attacks?
* Test username uniqueness
* How are logins processed, are they sent over http? Are details sent in a POST request or are they included in the URL\(this is bad if they are, especially passwords\)?
* Test NULL **%00** characters in the username and password fields.
* Test for fail-open conditions. Fail-open authentication is the situation when the user authentication fails but results in providing open access to authenticated and secure sections of the web application to the end user.
* Cookie poisoning. Try requesting the cookie names in the query string and body, some servers might read the parameters and set them as cookies. This can allow cookie poisoning.
* Set new password with old password

## Testing session management

* How well are sessions handled, is there a randomness to the session cookie? Are sessions killed in a reasonable time or do they last forever? Does the app allow multiple logins from the same user\(is this significant to the app?\).
* Test tokens for meaning
* Are tokens generated predictable or do they provide a sufficiently random value, tools to help with this are Burp Suite's sequencer tool.
* Check for insecure transmission of tokens Can they be accessed by JavaScript? Is this an Issue?
* Check for disclosure of tokens in logs. Are they cached server side? Can you view this? Can you pollute logs by setting custom tokens?
* Check mapping of tokens to sessions. Is a token tied to a session, or can it be re-used across sessions?
* Check session termination
* Check for session fixation
* Can an attacker hijack a user's session using the session token/cookie?
* Check for XSRF
* Can authenticated actions be performed within the context of the application from other websites?
* Check cookie scope. Is the cookie scoped to the current domain or can it be stolen, what are the flags set? is it missing secure or http-only? This can be tested by trapping the request in burp and looking at the cookie.
* Understand the access control requirements. How do you authenticate to the application, could there be any flaws here?
* Test effectiveness of controls, using multiple accounts if possible
* Test for insecure access control methods \(request parameters, Referrer header, etc\)
* Persistent cookies
* Session tokens strength
* Authorization properly enforced

## Testing business logic

* I do this step last, as it is when I am more familiar with the application and more likely to identify these issues.
* Identify the logic attack surface
* What does the application do, what is the most value, what would an attacker want to access?
* Test transmission of data via the client
* Is there a desktop application or mobile application, does the protocols used vary between this and the web application
* Test for reliance on client-side input validation
* Does the application attempt to base it's logic on the client side, for example do forms have a maximum length client side that can be edited with the browser that are simply accepted as true?
* Test any thick-client components \(Java, ActiveX, Flash\)
* Does the application utilize something like Java, Flash, ActiveX or silverlight? can you download the applet and reverse engineer it?
* Test multi-stage processes for logic flaws. Can you go from placing an order straight to delivery thus bypassing payment? or a similar process?

## Cache attacks

* Poisoning \(if only path is validated you can submit malicious queries/headers\) and cache bad results
* Race conditions: buy twice, get someone else's data
* If header injection: Inject a new response, the cache might store the attacker-controllable one
* Using multiple host headers or X-Forwarded-Host might cause the cache to load the attacker's site and serve it. Or the links to be written relative to the attackers host.
* **Dns cache poisoning**: The attacker creates a fake response to the DNS server that is cached, all users will get the wrong response until TTL.
* **Side-channel attacks** They exploit timing/energy consumption/noises/electromagnetic leaks rather than a direct weakness in the system.
* **Offline Web Application Cache Poisoning** The attacker loads an iframe of victim who uses wifi. The iframe points to facebook.com but caches the phishing site for a few days. When the user logs in at home it opens the cached poisoned site.

## Others

### Testing wordpress sites

```text
wpscan --url http://$ip/ --enumerate ap,at,tt,cb,dbe,u,m
```

### Quick wordpress bruteforce:

```text
python patator.py http_fuzz url=http://$ip/wp-login.php  raw_request=rawlogin 0=/usr/share/rockyou.txt -l /tmp/login &; tail -f /tmp/login | grep 302
```

### Fingerprint application

```text
clusterd --fingerprint  -i $ip
BlindElephant.py $ip
```

### Request site with specific cipher

```text
curl --ciphers ECDHE-RSA-AES256-SHA https://$ip
```

### Deobfuscate JS

```text
JStillery, JSNice
https://beautifier.io/
```

## OAuth2

* Allows a server to authenticate a user without any password. The app uses a service provider to authenticate users.
* Authentication flow:
  * User clicks login with Facebook.
  * User gets redirected to Facebook **facebook.com/oauth?redirect\_uri=target.com%2fcallback&state=xyz**
  * If the login is successful, he will be redirected to target.com
  * browser makes a request to including the state value
  * Client should validate the state value to prevent csrf.
* Pitfalls:
  * CSRF: use the redirect URL that contains the authorisation code and make a victim visit it
  * Open redir: attacker constructs and authorisation request URL for provider site with redirect\_uri set to attacker.com, when user gets redirected attacker can read authorisation code
  * Access token reuse: evil consumer site can authorise victim in provider site using access token, attacker uses the token to impersonate

## DNS Rebinding

* Attacker controls the authoritative nameserver for attacker.com
* User visits attacker.com
* NS responds with A record with the attacker.com IP, and short TTL
* The browser fetches a resource, i.e. **/secrets**
* The DNS response has expired, now the DNS server responds with victim.com IP.
* The browser will fetch **victim.com/secrets** in the attacker.com origin.
* Attacker can return a CNAME entry to brute force internal host names

## Cross domain requests

* Browser will do GET requests and POST that has standard content-type
* Otherwise, the browser will do an OPTIONS request and check the CORS headers

## PHPINFO\(\)

{% hint style="info" %}
to be updated - please get in contact to help complete
{% endhint %}

Useful information can be extracted from this page. Settings have been set in the loaded php.ini file. 

Look for `register_globals` and `allow_url`  - can allow LFI and RFI attacks. 

{% hint style="success" %}
Remember you may have to add a null byte %00   
e.g. $website/../../etc/passwd%00
{% endhint %}

## php.ini

RFI works only when the following is set in the php.ini file

`allow_url_fopen = On   
allow_url_include = On`

Commands such as`=exec,passthru,shell_exec,system,proc_open,popen,curl_exec,curl_multi_exec,parse_ ini_file,show_source`

can be used if this is found on the phpinfo\(\) page

`disable_functions    no value`



