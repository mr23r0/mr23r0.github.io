---
layout: post
category: posts
title: "The Database Handover | A Dumb Mistake | Critical BUG"
---

![banner](https://miro.medium.com/v2/resize:fit:720/format:webp/1*xCrUUPjQj7flq5ReQUfcXA.png){:.ioda}

### The Wakeup Call
Have you ever used these type of tools ?
-ShareIt<br/>
-Xender<br/>
-Inshare<br/>
-ShareMe<br/>
If yes, then you’re gonna enjoy it, if you never used it than I don’t believe that you ever lived your 13 age, Go and try using one now before reading it further.

*These tools are used to share Data from one device to another, and sometimes also helps to switch devices (move data from one to another)*

### The Beginning of the Story
I had a Pentesting Contract from a big German Company, and I earned 1000 Euros from it….. :) For :

-Email Verification Bypass (2 Methods)<br/>
-Stored Cross Site Scripting<br/>
-Insecure Direct Object reference (IDOR) leaking Employee Data<br/>
-Broken Link Hijacking<br/>

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*n3DAfWFDWbRSHKtRE86ZVQ.png){:.ioda}

So I again Started to look for more bugs and called it #2 Round. I had mapped the whole infrastructure in the #1st Round.

So this time I had small and well-mapped scope for testing, So I started to revisit my old issues (to try bypassing these) but this “Broken link hijacking” returned as a weird result,…. For fixing it The Company changed the whole child company name & also the domain.

### The Ritual of Comparing
After visiting the new domain “IAMNEW.COM” I saw something really terrible form of “moving on”. All subdomains were totally Identical…. and I started to get demotivated that I’m not gonna find anything new here, but by mistake I viewed my wappalyzer and saw “Wordpress 4.8” banner.

![image](https://miro.medium.com/v2/resize:fit:440/format:webp/1*jQ_rudboUxgB-QixF0mEZQ.gif)

### The Ritual of Background Enumeration
I quickly started my WPSCAN but I didn’t get any amazing results, Obviously I was sad & frustrated… so I gave myself 30 minutes and started thinking what can I do now, but after a couple of thoughts I got a spark I recalled A Line of my Mentor/ Inspiration “If you wanna do something to make sure you do your best”

Without waiting a minute I made a WordPress API token and ran it in the background, meanwhile I started my manual recon and testing but No luck :(
So I came to look at the WpScan results, And saw `debug.log` is publicly accessible.

### The Ritual of Dumb Mistakes
I started looking at the log, and I found list of Users and some Auth keys and reported it as an “Information disclosure”

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*L1zj18S9eLUl28FR8RZ1aQ.png)

But I was still feeling dumb, my Gut feeling was telling me that I’m missing something, So I again started to look at the log and I found the Database Name so I again added it in my previous “Information disclosure”

But still, something was missing, “my mind was keeping telling me to look deeper you dumb idiot” so took a cup of tea and viewed the log on my TV (for better preview) after reading 580 lines of logs I found the treasure:

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*qxBl4V0C131ob-EswiVMfQ.png)

But I again did the dumb mistake, I saw that the backup size is >500MB so reported it as Possible Denial of Service (DOS) because I was unable to extract it, I never encountered a file like this backup before. :(

### The Miracle
The Next Day I was still thinking about this file `.Daf` questing myself why? Why are you so dumb?

So I started again looking for the answers, after reading some threads on stackoverflow.com I moved myself to YouTube and after trying multiple search queries I finally found a video and really great tutorial :

<iframe width="98%" height="300" class="ioda" style="filter:invert(100%)"
src="https://www.youtube.com/embed/d7JrZYoiRuE"  
frameborder="0"  
allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"></iframe> 

After navigating nested folders, I’ve found the Database backup SQL file under: `dup-installer/database.sql`
It was containing All users, password hashes and much more juicy stuff :)

### The BUG
As we have used the file transfer applications, similar thing is available in Wordpress which is called [Duplicator](https://wordpress.org/plugins/duplicator/){:target="_blank"} which allows the admin to make a copy of the existing Wordpress Application and it’s too easy to use.

But sometimes developer forgets to remove the Backup files to remove from the new server (domain)

Simple isn’t it?

### Discovery
There is a list of files that you may find in the backup:

wp-config.bak (wordpress config backup)<br/>
All users Info<br/>
full database backup (containing Users with password hashes & articles)<br/>
all articles (including unlisted & drafts)<br/>
all wordpress files (for static analysis)<br/>
You may have to dig between folders and unique files, but I can promise you one thing at the end of the I’ll be worth it.<br/>

Thanks!