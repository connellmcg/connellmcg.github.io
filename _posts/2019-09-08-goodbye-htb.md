---
layout: post
title: Goodbye HTB, Hello PentesterLab
subtitle: Checking out Pentesterlab.com
tags: [cyber security]
comments: true
---
A recent challenge on HackTheBox had me banging my head off a wall for a full weekend. Without going into too much detail, or which challenge it was, I figured out that there was a third party service that ran an scheduled task every minute. I could use this job to run a reverse shell for me to connect to, and then upgrade the shell to a stable one. From this service account, the privilege escalation began, with a further reverse shell. It was probably realistic, but I felt like the movie Inception was playing out in my head.

I’ve learned an awful lot from #HackTheBox.eu but I can’t help but feel I am taking the long route. I need to go back to basics and make sure I am not missing some obvious techniques. I am preparing to pay for a three month subscription to VirtualHackingLabs. While I was researching this, I came across a Reddit post recommending PentesterLab.com. The Pro account is $20 a month, which is a lot less than VHL, so I figured I’d purchase this before committing to VHL.

So far it’s been a worthwhile endeavour. I have completed my UNIX badge and am working on the Essentials Badge. The Essentials badge goes over the basics of Web Penetration techniques, and it looks as if it is based off the OWASP Top Ten and some other attack vectors. I am working my way through SQL Injection now and this method is really useful to me for learning, because in HTB, I was using a tool called SQLmap which automated SQL Injection. (SQLMap and other such automated tools are not allowed in the OSCP Exam.)

I found with HtB, it was difficult to take notes due to the massive amounts of trial and error, but with this sequential process on PentesterLab, I am finding the process of note taking a lot easier. The concepts are introduced gradually, so I am finding that my note taking is a lot more organised in OneNote.

By doing the UNIX badge, I have found out some more methods I can use for UNIX filesystem hacking and escalation. I was unaware that the MySQL program could load the contents of a text file into a database table using the LOAD_FILE method. This is probably basic stuff, but I am unsure if I would have had the chance to cover this in HtB. Maybe eventually. I feel like I am making real progress here, and I have only spent a weekend at this!

Compiling a c file, then creating a binary of the file to set the owner as the victim, and running it to print the contents of the key.txt file under the victims home directory. A common scenario in CTF Challenges.

The other badges cover recent vulnerabilities (CVEs); one such example is the Shellshock exploit. The labs also cover a whole section in packet capture and Man in the Middle attacks, which I am super impressed with as I thought it would be really hard to simulate these conditions in a virtual lab, so kudos to the creators for figuring this out.

There are fifteen different badge categories, with plenty of sub challenges within them. I think they are meant to be done in order due the increasing difficulty and the fact that you use what you have learned in the previous challenges to build your way up, but I can’t really comment just yet as I haven’t done these yet.

I’d recommend PentesterLab — I am enjoying the learning process and labs. Each exercise has a corresponding video that walks you through to a solution, however I am trying not to use these. The videos do not have captioning, for those who rely on these — it’s not a dealbreaker as it should be evident on the videos anyway of what is being done.

I’ll come back to HackTheBox in a few months :)
