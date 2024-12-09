# Operating Systems
This folder will talk about different aspects of an Operating System. In this file we start off with an introduction to operating systems. Then we will go into more detail about operating systems, such as processes, threads, memory, file systems, and more.

## What Is An Operating System?
An operating system is a body of software that is responsible for making it easy to run programs, allowing programs to communicate, allowing programs to share memory, enabling programs to interact with devices, and other things. The primary way an OS does this is through a technique called **virtualization**. The OS takes physical resources and transforms it into a more general, powerful, and easy to use virtual form of itself.

## Virtualizing The CPU
The OS and hardware are in charge of making it look like that there are multiple processers available on the system, even though there might be one or few of them. This allows users to run multiple programs at the same time. This is called virtualizing the CPU.

## Virtualizing Memory
The OS and hardware make it seem that each process has its own private physical address space, but in reality the processes have a virtual address space. Each process thinks that their memory starts at address 0 and goes to some max number, but in reality, the OS and hardware are performing address translation from virtual addresses to physical addresses. 

## Concurrency

## Persistence
