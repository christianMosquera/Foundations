# Memory Management

Memory management is the process of controlling and coordinating computer memory (RAM) between different processes, threads, and the operating system (OS). It involves allocation, access, and release of memory efficiently while ensuring safety and performance.

## Key Concepts
1. Memory Layout of a Process: Each process has a virtual memory space divided into distinct sections:

    - Text Segment: Contains the executable code (read-only).
    - Data Segment:
        - Initialized Data: Global/static variables with initial values.
        - Uninitialized Data (BSS): Global/static variables without initial values.
    - Heap: Dynamically allocated memory (grows upward).
    - Stack: Stores function calls, local variables, and control flow (grows downward).
2. Virtual Memory:
    - The OS virtulizes memory, so every adderss from a user program is virtual. The OS will then translate the virtual address to a physical address in order to manipulate/read it. The OS does this to give the view of a large contiguous block of memory to each program.
    - Advantages:
        - Isolation between processes.
        - Efficient use of physical memory through paging/swapping.
     
3. Page Table:
    - Translates virtual addresses into physical addresses.
    - Maintains mappings for each process.
  
## Virtual Memory In Depth
### <ins>Address Spaces</ins>
TODO
### <ins>Address Translation</ins>
TODO
### <ins>Segmentation</ins>
TODO
### <ins>Free Space Management</ins>
TODO
### <ins>Paging</ins>
TODO
### <ins>TLBs</ins>
TODO
1. Goals of Virtual Memory:
    - **Transparency**: How can the OS implement virtual memory so that it is invisible to the running program. The running program should not be aware that memory is being virtualized. It should act as if it has its own private physical memory. From the running programs perspective, it looks like its address space starts at memory address 0, but in reality it is being translated into a physical address.
    - **Efficiency**: The OS should strive to make virtual memory efficient in both time and space. In order to implement this the OS has to rely on hardware support, for example TLBs
    - **Protection**: The OS should make sure to protect processes from one another as well as the OS itself. When a process performs a write to a memory address, it needs to make sure that it is not accessing memory from another process. No process should be able to interact with another processes memory space, therefore protection gives us the property of **isolation**
2. Translation:
    - **The Problem**: From the programs perspective, its address space starts at address 0 and grows until its max size (in this case use 16kb as an example). Therefore, any references to an address should be within the bounds of 0-16kb. Thus the problem is: How can we relocate this process in memory in a way that is **transparent** to the process. How can we provide the illusion that memory starts at 0, but in reality it starts somewhere else in physical memory.
    - **Dynamic (Hardware-based) Relocation**: The idea is referred to **base and bounds** or **dynamic relocation**. Both terms can be used interchangeably. The idea is that we use two registers within each CPU. One is called the base register and the other is called the bounds register (sometimes called the limit register). These registers allow us to place the address space anywhere in physical memory while ensuring that each process can only access its own address space. When the program is running and it references a memory address, translation occurs ```physical address = virtual address + base``` The base and bounds registers are hardware structures kept on the chip (one pair per CPU), this part of the processer is sometimes called the memory management unit (MMU).
  
## Memory Allocation Techniques
1. Static Allocation:
    - Memory allocated at compile-time.
    - Fixed size, cannot be resized during execution.
    - Example: Global and static variables.
2. Automatic Allocation:
    - Memory allocated on the stack during function calls.
    - Released automatically when the function exits.
3. Dynamic Allocation:
    - Memory allocated during runtime on the heap.
    - Flexible but requires manual management.
    - Functions:
        - malloc(): Allocates memory.
        - calloc(): Allocates and initializes memory.
        - realloc(): Resizes allocated memory.
        - free(): Releases allocated memory.
     
## Common Memory Issues
1. **Forgetting To Allocate Memory**:
    ```
    char *src = "hello";
    char *dst;
    strcpy(dst,src);
    ```
    - This causes a segmentation fault (basically means you did something wrong with memory). This causes a segmenation fault because strcpy is trying to copy the contents that the address at src is pointing to in memory. dst is declared as a pointer, but is not defined, therefore it does not have an address that points to a location in memory. The proper way to do this is:
    ```
    char *src = "hello";
    char *dst = (char*)malloc(strlen(src) + 1);
    strcpy(dst,src);
    ```
2. **Not Allocating Enough Memory**:
    ```
    char *src = "hello";
    char *dst = (char*)malloc(strlen(src));
    strcpy(dst,src);
    ```
    - In this case the dst is not given enough space in memory to hold all of the data at src. Therefore this can cause problems such as overwriting other things on the heap/stack.
3. **Forgetting To Free Memory**:
    - Forgetting to free memory is called a memory leak. When a program is running for long periods of time, and you forget to free memory, it can lead to the program eventually running out of memory and then a restart is required.

There are more issues that can be talked about, but will be skipped for now, some include: Double free, incorrectly calling free, forgetting to initialize allocated memory, and more.
