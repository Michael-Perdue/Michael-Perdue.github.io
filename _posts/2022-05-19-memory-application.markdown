---
title: Memory Application
date: 2021-11-21
categories: [University Projects, 2nd year]
tags: [java]     # TAG names should always be lowercase
---

## Creating my own malloc and free

I created my own implementation of memory allocation in C to replace the already existing [malloc and free](https://man7.org/linux/man-pages/man3/malloc.3.html). It contains multiple doubly linked free lists of memory blocks of varying sizes starting at the smallest being 64 and under and the biggest being 4k and over. It is fully functional at allocating and freeing memory but it currently does not coalesce freed memory leading to possibly lots of fragmentation depending on its use. It uses [sbrk](https://linux.die.net/man/2/sbrk) to get more memory to be used for allocation and each request sbrk request is 8k. An important thing to note with the implementation is that it cannot deal with memory requests of larger then 8k and this is due to the existing malloc using [mmap](https://man7.org/linux/man-pages/man2/mmap.2.html) to deal with requests larger then 8k so I decided not to implement it for my program.

As the base for the project, I created my own doubly linked list which had the length and a flag to tell you if the memory block was in use - this wasn’t the most efficient possible struct as it was 16 bytes and if I was trying to be really efficient then it could have be 12 bytes as not all of the int would be needed so the last bit could be for the flag. There is then an array which deals with tracking the pointers to the start of each free list of memory block and each time the user requests or frees memory then that bit of memory will be checked to see if it needs to be switched to a different list depending on its size and then the relevant actions are made so that it is swapped onto the new free list. When i comes to choosing what block to allocate when the user wants memory it allocates the smallest block which is helped by the different size free lists. Upon trying to free a memory location which is already free or doesn’t exist then you are greeted with an error message stating that you are not giving a valid address.

I created a header file to allow for the program to be used by other programs. I also created a method to print the free lists that a program is using so that I could  easily test that everything works, so following this I made a program in which you type `A<size>` and to free memory you type `F<address>` then after every command the free lists would be printed.

This program can be run throught the commands `gcc *.c *.h -o main` and `./main` - here is an series of frees and allocations:

![](https://michael-perdue.github.io/assets/MallocInAction.png)
