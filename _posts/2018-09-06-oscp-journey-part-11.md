---
id: 393
title: OSCP Journey Part 11
date: 2018-09-06T10:00:16+01:00
author: rowbot
layout: post
guid: http://offsecnewbie.com/?p=393
permalink: /2018/09/06/oscp-journey-part-11/
image: /wp-content/uploads/2018/06/perfexion_logo.png
categories:
  - Progress Bar
  - Useful things
tags:
  - oscp
  - PE
---
<pre class="wp-block-preformatted">Day: -6
PDF: 100
Videos: 100%
Boxes: 10
Networks:1</pre>

Well less that a week until my exam and I&#8217;m not feeling confident. I was hoping for at least 20 boxes owned but the rabbit holes owned me.

I&#8217;ve signed up for the proctored exam, meaning someone will be watching me via a webcam for the duration of the exam. Bad news for them, I can only hack when naked. 

I just haven&#8217;t put the time required to pass the exam in so failure is inevitable. This is okay I&#8217;ve still learned a lot. I&#8217;ve signed up for another 3 months labs which I&#8217;ll start in a few months + I&#8217;ll use the credits for being on the pilot to pay for more exam resits. I&#8217;ll take a break from OSCP for a while and concentrate on HTB actually looking forward to that!

Privilege escalation is my weak area. I&#8217;ve decided to list all the ways I know of to PE. Let me know if you know any more.

<div class="wp-block-image">
  <figure class="alignleft is-resized"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/09/noun_Penguin_619279.png?resize=183%2C183" alt="" class="wp-image-394" width="183" height="183" data-recalc-dims="1" /></figure>
</div>

LINUX  
Kernel exploits  
Badly configured cron jobs  
Exploiting services which are running as root  
Exploiting suid executables  
Exploiting sudo rights/user exploiting users with ‘.’ in their path.  


<div class="wp-block-image">
  <figure class="alignright is-resized"><img src="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/09/noun_bill-gates_369842-1.png?resize=201%2C201" alt="" class="wp-image-396" width="201" height="201" srcset="https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/09/noun_bill-gates_369842-1.png?w=512 512w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/09/noun_bill-gates_369842-1.png?resize=150%2C150 150w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/09/noun_bill-gates_369842-1.png?resize=300%2C300 300w, https://i0.wp.com/offsecnewbie.com/wp-content/uploads/2018/09/noun_bill-gates_369842-1.png?resize=500%2C500 500w" sizes="(max-width: 201px) 100vw, 201px" data-recalc-dims="1" /></figure>
</div>

WINDOWS  
Kernel Exploits  
Unquoted Service Paths  
Insecure Service Permissions  
Insecure File/Folder Permissions  
AlwaysInstallElevated  
Privilege Escalation using Task Scheduler  
DLL Hijacking  
Stored Credentials  


So the final push to the finish line is underway wish me luck.