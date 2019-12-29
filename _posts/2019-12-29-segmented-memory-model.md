---
layout: series
project_slug: os-dev
title: Segmented Memory Model
chapter: 4
author: Kshitij Burman
categories:
- tutorial
- os-dev
tags:
- segmented memory model
- bootloader
date: 2019-12-29 16:26 +0530
---
Remember we are in *16 bit* mode. That means the size of every register is *16 bit* only. So how much memory can we access using a 16-bit address?
`2^16 = 65,536 Bytes` that's only __64 KB__.


But *64 KB* was not sufficient as the complexity of the software grows the demand for memory also increased with it.

Intel solution to this was using a __20 bit address line__ instead of *16 bit*.
This would increase the memory accessible from _64 KB_ to __1 MB__.

`2^20 = 10,48,576` that would be `1 MB`.
<!--more-->

### How did Intel did that?
They introduced a new category of a register called _segment register_. 
- DS (Data Segment)
- CS (Code Segment)
- SS (Stack Segment)
- ES
- FS
- GS

Side note: Among these _CS_ always contains the segment currently code is executing and you can't set it using _MOV_ instruction.
This register contains the segment address.

Now all the addresses are represented using a segment address pair like __DS:DI__ where _DS_ defines the start of the _segment_ and the final address is calculated by adding both the address. Just one trick here.
_segement register_(_DS_  in this case)is *left shifted by 4 bit* before adding it to *DI*

{% include kapwing.html video_id="5e0511b48e681f00145f1b73" %}


- Orange area is the _current segement_.
- The number on the top and bottom of the segment is the start and end address of the segment.
- *DS:DI* arrow point the address within the segment.
- Two or more segments can overlap with each other.
- __Images are not to scale__


### Calculate physical address
Let's take the example of __DS:DI__. We would be using the below formula to calculate the physical address.
```
physical address = (DS * 0xF) + DI 
```


### Few more things about segment register
You can't use any register with any segment register.

|Segement   |Offset registers             | Description
|-------|--------------------|--------------------|
| CS| IP | Code pointer
| DS | BX, DI, SI | Data pointer
| SS | SP, BP | Stack pointer
| ES | BX, DI, SI | Used as destination address

[Table source](https://www.geeksforgeeks.org/memory-segmentation-8086-microprocessor/)



### Time to revisit the hello world program!
Let's now clear the segment address that I skipped in the previous post.
[Link to the post]({%post_url 2019-12-26-hello-world-from-bootloader%})

![Bootloader memory layout](/assets/img/bootloader-memory-layout.png)

(Image not to scale)

Our message `Hello world` is present in the section highlighted using *blue*.
To access it we can follow two-approach either set segment register at __0__ or set the segment register at __0x7C00__


We're going to cover both the approach.

### When segment register is set to __0x0__
First, we need to tell our *assembler* to calculate all the address from _0x7C00_
using _ORG_ command and set the offset register to start of our string address.

```nasm
BITS 16
ORG 0x7C00

main:
  MOV AX, 0
  MOV DS, AX
  MOV SI, msg
  CALL print_string

msg DB "Hello World", 0
```
(Removed print_string and other bootstrap code for simplicity)

Can you guess, What is the address in the `SI` register?
1. `MOV SI, 0x7c16`
2. `MOV SI, 0x16`


### When segment register is set to __0x7C00__


```nasm
BITS 16
ORG 0

main:
  MOV AX, 0x7C0
  MOV DS, AX
  MOV SI, msg
  CALL print_string

msg DB "Hello World", 0
```
(Removed print_string and other bootstrap code for simplicity)

__DS__ register is __0x7C0__, not __0x7C00__ because the segment register is left sift by 4 bit before added.

### References
- [Stackoverflow Answer Must Read](https://stackoverflow.com/a/36450373/1824021)
- [https://www.geeksforgeeks.org/memory-segmentation-8086-microprocessor/](https://www.geeksforgeeks.org/memory-segmentation-8086-microprocessor/)
- [https://en.wikipedia.org/wiki/Real_mode](https://en.wikipedia.org/wiki/Real_mode)
- [http://www.brokenthorn.com/Resources/OSDev4.html](http://www.brokenthorn.com/Resources/OSDev4.html)
