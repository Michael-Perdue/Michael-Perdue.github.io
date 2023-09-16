---
title: My Own Malloc And Free In C
date: 2021-11-21
categories: [University Projects, 2nd year]
tags: [c, memory management]     # TAG names should always be lowercase
---

**Project Overview:** 

I was tasked with developing a memory allocation implementation in C, aiming to be used as a working replacement for the existing [malloc and free](https://man7.org/linux/man-pages/man3/malloc.3.html). The implementation includes multiple doubly linked free lists of memory blocks, varying in size from 64 bytes to 4 kilobytes and beyond. It's fully functional for memory allocation and deallocation, but it currently lacks memory coalescing, which may lead to fragmentation issues depending on usage. To acquire additional memory for allocation, it uses the [sbrk](https://linux.die.net/man/2/sbrk) system call, fetching 8 kilobytes of memory per request.


## Implementation Details

### Doubly Linked List   

I created a custom doubly linked list structure with each block in the list having a length indicator, a flag to determine if the memory block is in use and two pointers one to the previous block and one to the next block. Although the struct consumes 16 bytes, optimization could reduce it to 12 bytes, with the last bit reserved for the flag.

### Memory Block Tracking 

An array tracks pointers to the start of each free list of memory blocks. When a user requests or frees memory, the program checks the starting memory block of each list to decide if it should move to a different list based on its size being requested or freed. It then returns the address of the memory block and creates a new memory block with the address being the end of what was allocated and the size being updated, it then places the new now smaller memory block onto the correct free list. Allocating memory prioritizes the smallest available block, thanks to different-sized free lists. It does not perform memory coalescing on freed memory blocks.

The sizes for each list start at 64 and increase in multiple of 2 till reaches 8,192.

### Error Handling   

Attempting to free memory already marked as free or nonexistent results in an error message indicating an invalid address.

### Header File     

I created a header file for easy integration with other programs.

### Limitiations

1. **Memory requests larger than 8 kilobytes:** it cannot handle memory requests larger than 8 kilobytes due to it ideally using [mmap](https://man7.org/linux/man-pages/man2/mmap.2.html) for those cases like the  existing malloc function and I did not have time to get round to adding mmap for my implementation.   
2. **Potential error upon freeing data inbetween sbrks:** my program knows the starting address of the allocated memory and the end address of allocated memory but if inbetween the program getting more memory allocated from sbrk there could be memory allocated to other programs inbetween it. Meaning im given 0x1 - 0x2 then program x is given 0x2-0x3 and i then request more and get 0x3-0x4 my system will break if someone says i want to free inbetween 0x2-0x3 as the program sees the valid range for free as 0x1-0x4.
3. **Fragmentation issues:** it currently lacks memory coalescing, which may lead to fragmentation issues depending on usage.

## Testing Utility:

A method allows for printing the free lists used by a program, aiding in testing and verification. I wrote a test program that accepts commands such as `A<size>` for memory allocation and `F<address>` for memory deallocation. The free lists are automatically printed after each command.

### Usage 

This program can be compiled and run using the following commands: `gcc *.c *.h -o main` and `./main`. Below is a series of memory allocation and deallocation actions:

![](https://michael-perdue.github.io/assets/MallocInAction.png)

**Closing Thoughts:** 

Developing the Memory Application was a very intellectually stimulating project that I completed during my second year at university. It allowed me to apply my C programming skills to create a practical memory allocation tool and taught me invaluable knowledge on how memory and pointers work.
