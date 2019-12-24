---
layout: post
title: 'Building an OS: Part 1 - Setup'
categories:
- tutorial
- os-dev
chapter: 1
tags:
- setup
- dockercross
- qemu
- virtual-box
date: 2019-12-24 00:00 +0000
---
Before we can start with the coding part you would need some of the tools to build and run our OS.
Generally, this setting up cross compiler setup with other tools for OS development is very much platform-dependent and required you to know a lot of things even before you start. 
In this series, I try to make OS development platform-independent using docker for the compiler setup.


#### Prerequistes 
- [Docker](https://www.docker.com/)
- [Virtual Box](https://www.virtualbox.org/)
- [Vagrant](https://www.vagrantup.com/)
- [QEMU](https://www.qemu.org/)
- [Dockercross with NASM](#dockercross-with-nasm)
- Any text editor (I will suggest `Sublime Text`)
- [Vagrant Manager](http://vagrantmanager.com/) (Optional)

Apart from these, this series expect that you know programming and curious enough to learn more.
<!--more-->


### Explanation
#### Docker
Setting up a cross-compiler can be a nightmare and special for people who just started and don't know what the hell they are installing. So we would be using a docker image that already contains all the compiler and tools we would need pre-installed.
We would be using [dockercross](https://github.com/dockcross/dockcross) images.

#### Virtual Box
Later on this series we would be using Virtual box to run our ISO images.

#### QEMU
At the start of this series, we would be working with basic bootload and kernel and we would frequently run our code. We want something fast to quickly validate our code.
More of all we can run our kernel without even having a bootloader with `-kernel`  flag.
It normal for you to be confused with the difference between `QEMU` and `Virtual Box`. Have a look at the following links
- [Quora Answer](https://www.quora.com/What-are-the-pros-and-cons-of-VirtualBox-versus-QEMU)
- [Stackoverflow Answer](https://stackoverflow.com/questions/43704856/what-are-the-differences-between-qemu-and-virtualbox)


### MISC

#### Dockercross with nasm
Create a docker file with the following content

```plain
# Dockerfile
FROM dockcross/linux-x86

ENV DEFAULT_DOCKCROSS_IMAGE dockercross-with-nasm

RUN apt-get update; apt-get install nasm
```

Run the following commands
```bash
$ docker build -t dockercross-with-nasm .
$ docker run --rm dockercross-with-nasm > dockercross
$ chmod +x dockercross
```

You should get an `dockercross` file after this.
Verify its working by running the following command
```bash
$ ./dockercross uname -a
Linux be0ff6624d17 4.9.184-linuxkit #1 SMP Tue Jul 2 22:58:16 UTC 2019 i686 GNU/Linux

```

You should get some string starting with `Linux`.