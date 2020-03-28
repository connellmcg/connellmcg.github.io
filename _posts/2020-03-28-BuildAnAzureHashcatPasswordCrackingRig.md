---
layout: post
title: Build an Azure Hashcat Password Cracking Rig
subtitle: A quick prep guide
tags: [azure, security]
comments: true
---

I enjoy doing capture the flag boxes on sites like HackTheBox.eu, hoever I do come across the need for cracking passwords from time to time. My own graphics card is about ten years old and isn't suit at all for cracking. 

I decided to check out the Azure N Series VMs as they have GPUs allocated, and had success. I managed to take my time to crack SHA256 hashes with rockyou.txt from approx 8 hours down to 16 minutes. :)

I had some issues installing the NVidia drivers, but managed to sort it out with the following steps.

1. Create a Resource Group. Pick East US to get the option of choosing the GPU VMs.
![screenshot](/img/crack_01.jpg)

2.
![screenshot](/img/crack_02.jpg)
3.
4.
5.
6.
7.
8.
![screenshot](/img/crack_08.jpg)
9.
![screenshot](/img/crack_09.jpg)
10.
11.
12.
![screenshot](/img/crack_12.jpg)
13.
14.
![screenshot](/img/crack_14.jpg)
15.
16.
![screenshot](/img/crack_16.jpg)
17
![screenshot](/img/crack_17_01.jpg)

![screenshot](/img/crack_17_02.jpg)


    
Happy Cracking!
