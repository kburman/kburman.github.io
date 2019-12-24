---
layout: post
title: 'Building an OS: Part 2 - Let''s write a bootloader'
categories:
- tutorial
- os-dev
chapter: 2
tags:
- dockercross
- qemu
- nasm
- bootloader
date: 2019-12-25 00:00 +0000
---
### When you press power button
It all start with you pressing the power button. A signal is passed to your motherboard and it's forward signal to PSU (Power Supply Unit) and it start powering the rest of the system if PSU is able to supply power without any major problem it sends a `power_good` signal to mother board BIOS. BIOS then runs BIOS POST(Power on self test) and a lot of other things. 
If you want to get an in-depth idea about this goto [link 1](http://www.brokenthorn.com/Resources/OSDev3.html) and [link 2](https://www.howtogeek.com/398493/what-exactly-happens-when-you-turn-on-your-computer/)
<!--more-->
![Laptop power button](/assets/img/laptop-power-button.jpg)
Credit: https://www.flickr.com/photos/liewcf/3968289500/in/photostream/
### After BIOS POST
- BIOS will iterate though each device mentioned in device boot order.
- How does BIOS know if a device is bootable?
- It check bytes at position `511` and `512` is `0xAA55` of first sector. If yes then its a bootable device.

![BIOS boot order screen](/assets/img/boot-order.jpg)
Credit: https://www.flickr.com/photos/liewcf/3968289500/in/photostream/

### When a bootable device is found
When BIOS found a bootable device then it would copy data from __first sector__ of bootable device to RAM starting from phsyical address `0x7c00`.

Size of the sector can vary for different device. For example 

|Device   |Sector Size             |
|-------|--------------------|
|Floppy Disk|512 Bytes|
|CD and DVD| 2 KB|
| Older HDD| 512 Bytes|
| Newer HDD| 4 KB |

As you have noticed that BIOS only load first sector into RAM and even with mordern hardware you can't get an actual OS in a single sector.
For reference, Linux Kernel with basically everything striped away will be in some *MBs* if not *GB* [[ref](https://superuser.com/questions/370586/how-can-a-linux-kernel-be-so-small#comment413299_370586)]

So this is clear that BIOS could not load our OS for us. We would need to write a __loader program__ which load the kernel into RAM for us.
Also we need to make sure that our *loader program* fits into the first sector.

Since smallest sector size is _512 Bytes_ we will write our loader within 512 bytes.

This program is called __bootloader__.


### Bootloader
We will write our bootloader in `assembly` languange because this is the closed we can get to low-level language.

Let's start with the minimum assembly that you would need to start. You can call it the **boiler plate code**.
```nasm
bits 16

cli
hlt

times 510 - ($-$$) db 0
dw 0xAA55
```

Let me explain what each line means one by one.

```nasm
bits 16
```
[learn more](https://nasm.us/doc/nasmdoc6.html#section-6.1)

Every computer has 3 modes in which it can work.
- 16 Bit (Also called as Real Mode) [learn more](https://wiki.osdev.org/Real_Mode)
- 32 Bit (Also called Protected Mode) [learn more](https://wiki.osdev.org/Protected_Mode)
- 64 Bit

To keep things simple just think that each mode defines the size of register you can use.

When the computer start it's in `16 bit` mode.
You can switch to different mode by doing some specific set of actions. Will come to this, later on.

This line explicitly tells the assember that we want to produce the code designed to run on a 16 bit mode.

Good thing about this mode is that we have access to BIOS.


```nasm
cli
``` 
[learn more](http://www.mathemainzel.info/files/x86asmref.html#cli)

This instruction stands for *Clear all the interrupt*. 
In 16 bit mode. 

CPU is programed to execute some block of code when an [interrupt](https://www.wikiwand.com/en/Interrupt) is triggered.
Interrupt can be triggered from various source like when press a keyboard button or timer is triggered.

For now we are not concerend about any of this. We will just disable all of them.

```nasm
hlt
``` 
[learn more](http://www.mathemainzel.info/files/x86asmref.html#hlt)

It stands for *Halt*. You guessed it right.
It does stop any further code execution but it doesn't  mean you can't run any code anymore. Interrupts can still be fired and CPU would execute the code. Unless you disabled the interrupts using `cli`.

One more myth you would come around is that it would put CPU in sleep mode or some low power mode. No it doesn't. [Stackoverflow Answer](https://retrocomputing.stackexchange.com/a/6733)


```nasm
times 510 - ($-$$) db 0
``` 
[learn more](https://nasm.us/doc/nasmdoc3.html#section-3.2.5)

This might look like the most complex line of all of them and to be honest it is.
But what it does is very simple and once we breakdown you would also see.

Remember when told that for the BIOS to identify if a device is bootable bytes at _511_ and _512_ should be `0xAA55`.

The assembly code written above this line may or may not be of exactly 510 bytes. So we add some padding by adding zero to make it *exactly* 510 bytes.

`$` is the address of current line and `$$` is the start of this section(In this case it would start of this file since we would be using flat binary).

So `($-$$)` returns the size of the program before this line. Subtracting it from `510 - ($-$$)` we get the number of bytes left to make it a `510 bytes` file.


```nasm
dw 0xAA55
```
[learn more](https://nasm.us/doc/nasmdoc3.html#section-3.2)

By now you must have gueessed it. It writes the *boot signature* after the 510 bytes.
`dw` stands for *double word*. Where a *word* means


#### Compiling and running the bootloader

For converting *assembly language* to *machine code* we would need an *assembler*. 
There are many assembler out ther but we are going to use `nasm` for this. Because that's what I know. 

Second thing that you should know is that output file format. We would be using `binary` file. In this file is not divided into section or any other complex logic as in *ELF* format.

First of all create a new file `boot.asm` and wite the below code in it.
```bash
$ cat boot.asm
bits 16

cli
hlt

times 510 - ($-$$) db 0
dw 0xAA55
```

Next we compile the above code using *nasm*
```bash
$ ./dockercross nasm -fbin boot.asm -o boot
```

You can check the bootloader size. It should be exactly *512 bytes* only.

```bash
$ ls -la boot
-rw-r--r--  1 kshitijburman  staff  512 Dec 25 01:14 boot

```


To run this bootloader we would be using `qemu`
```bash
$ qemu-system-i386 -fda boot
```

You should get a window as this
![QEMU running our bootloader](/assets/img/qemu-with-bootloader.png)

Congratulations!! You got you first bootloader up and running.

Will continue bootloader in next post.