---
layout: post
title: Build an Azure Hashcat Password Cracking Rig
subtitle: A quick prep guide
tags: [azure, security]
comments: true
---

I enjoy doing capture the flag boxes on sites like HackTheBox.eu, hoever I do come across the need for cracking passwords from time to time. My own graphics card is about ten years old and isn't suit at all for cracking. 

I decided to check out the Azure N Series VMs as they have GPUs allocated, and had success. I managed to take my time to crack SHA256 hashes with rockyou.txt from approx 8 hours down to 16 minutes. :)

I had some issues installing the NVidia drivers, but managed to sort it out in step.

**Step 1.** Create a Resource Group. Pick East US to get the option of choosing the GPU VMs.

![screenshot](/img/crack_01.jpg)

**Step 2.** Add a VM in the Resource Group

Choose Ubuntu 18 LTS, and the Size “Standard NC6_Promo”.

![screenshot](/img/crack_02.jpg)

**Step 3.** Choose the password options and set up your details.

**Step 4.** Disks – Choose Standard SSD.

**Step 5.** Networking – Leave the defaults, but enure “Public Inbound Ports” is set to SSH (22).

**Step 6.** Management – Unset the Boot Diagnostics.

**Step 7.** Review and Create – Select Create to create your VM.

**Step 8.** When your VM has been created, open the VM from the Azure Portal and take note of the “Public IP Address”.

![screenshot](/img/crack_08.jpg)

**Step 9.** SSH to your VM

![screenshot](/img/crack_09.jpg)

**Step 10.** `lspci` to confirm the Tesla 10 driver is installed

![screenshot](/img/crack_10.jpg)

**Step 11.** Install the Nvidia Drivers following this guide: [N Series Driver Setup](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/n-series-driver-setup)

If you get the below problem, you can fix it using the following commands:

`sudo dpkg -i --force-overwrite /var/cache/apt/archives/nvidia-440_440.64.00-0ubuntu1_amd64.deb`

`sudo apt-get --fix-broken install`

`sudo apt-get upgrade`

![screenshot](/img/crack_12.jpg)

**Step 12.** Reboot the VM.

**Step 13.** Verify driver install with `nvidia-smi`

![screenshot](/img/crack_14.jpg)

**Step 14.** `sudo apt install hashcat`

**Step 15.** `wget https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt`

![screenshot](/img/crack_16.jpg)

**Step 16.** `hashcat -m 1800 <your hashfile> -o cracked.txt wordlists/rockyou.txt -w 3 -O`

 ![screenshot](/img/crack_17_01.jpg)

 ![screenshot](/img/crack_17_02.jpg)


Happy Cracking!
