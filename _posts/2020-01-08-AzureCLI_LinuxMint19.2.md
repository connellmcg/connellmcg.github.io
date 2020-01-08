---
layout: post
title: Configuring Azure CLI for Linux Mint 19.2 (Tina)
subtitle: A quick guide
tags: [azure]
comments: true
---

I have Linux Mint 19.2 (Tina) installed on my workstation. I was trying to get the Azure CLI installed on this machine using the Microsoft guide at [Install Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-apt?view=azure-cli-latest).

I kept running into issues - it comes down to the fact that the lsb module is not installed by default.

As you follow the guide, on step 3make sure you subtitute for `AZ_REPO=$(lsb_release -cs)` for `AZ_REPO=$(bionic)` so that you pull the Azure CLI from the bionic repo.

`AZ_REPO=$(bionic)
echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | 
    sudo tee /etc/apt/sources.list.d/azure-cli.list`
   
Run apt-get update, then sudo apt-get install azure-cli

![azurecli_screenshot](/img/azurecli.jpg)
    
Happy Azuring!
