---
layout: post
title: Compiling lillit OS in macos
categories:
- journal
tags:
- lilit-os
- ''
date: 2019-12-23 21:17 +0530
---
So the first thing I tried to do was to compile the OS and enjoy it.
Well no so fast. I just forgot that I'm now on macos. 

When I ran the first make command `make toolchain/crystal/.build/crystal` it failed.
I haven't opened make file in years so I tried to avoid opening it. Googled the error so if it's some generic error or not. Couldn't find anything significant so have to get to make file. After little tinkering figured out that it was patching the crystal compile and it was failing in that step.
In short, I got down to the crystal wrapped and found that path for crystal was incorrect for my installation.

After fixing the crystal path another roadblock was just waiting for me. It was using gcc assembler. So I tried installing gcc assembler. Had no luck. 

Finally, I decided to use ubuntu for it. So I could install ubuntu, use docker or just a few days back read about ubuntu vm using multipass. I thought this is the perfect time to try this.
Booted a VM installed bunch of tools things were going good when I hit another error. Which was strange. So I decided to run each command one by one for installing gcc. Then I learned VM was out of disk space. 
I did google a bit but can't find much to increase the disk size.

One thing is clear it would take a lot of effort to build this thing in mac. 

Anyways I would now be starting my project taking help from the very first commit of lillt OS.
Learning from this I would be automating the build process using docker.
