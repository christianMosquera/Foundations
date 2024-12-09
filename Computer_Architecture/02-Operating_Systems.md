# Operating Systems

## Processes
### <ins>What is a Process</ins>
A process is a running instance of a program. When you execute a program, the operating system: loads the program’s code into memory, allocates resources (CPU, memory, file descriptors), and creates a process to manage the execution of the program.

### <ins>Key Components of a Process</ins>
1. Code: The instructions of the program
2. Data: Static variables, constants, and global variables
3. Heap: Dynamically allocated memory
4. Stack: Stores function calls, local variables, and control flow data
5. Registers: The CPU registers used during execution
6. Program Counter (PC): Stores the next instruction to execute

### <ins>Process States</ins>
A process goes through multiple states during its lifecycle. The most common are:
1. New: The process is being created
2. Ready: The process is loaded into memory and waiting to be executed
3. Running: The process is actively executing instructions on the CPU
4. Blocked/Waiting: The process is waiting for an event
5. Terminated: The process has finished execution

### <ins>Process Context Switching</ins>
Since modern computers execute multiple processes, the CPU must switch between these processes to ensure multitasking. This is called context switching.

Steps in a context switch:
1. Save the current processes state (registers, PC, etc) in the Process Control Block (PCB)
2. Load the new process’s state from the PCB
3. Update the CPU’s Program Counter to the new process’s instruction

This operations is managed by the operating systems kernel and is essential for multitasking

### <ins>Process Control Block (PCB)</ins>
The Process Control Block is a data structure in the OS that stores information about a process. It contains:
- Process ID (PID): An unique identifier for the process
- Process State: The state of the process, running, ready, etc
- CPU Registers: A snapshot of the process’s register values
- Memory Info: Details about memory allocation (stack, heap, etc)
- I/O Status: Open files and devices being used

### <ins>Practical Example</ins>
Running the ls command in linux

1. Execution:
    - You type the ls command in the terminal. The shell creates a new process calling fork()
    - The process executes the ls command by calling execve()
2. Lifecycle:
    - New: Process is created
    - Ready: Process is waiting to be scheduled by CPU
    - Running: CPU is executing the ls command
    - Terminated: The process completes execution and resources are freed

## Threads
A **thread** is the smallest unit of execution in a program. Threads exist within a program, and every program has at least one thread. This thread is called the main thread. Threads enable parallelism within a program, which allow it to perform multiple tasks simultaneously

### <ins>Threads vs. Processes</ins>
- **Definition**: A thread is a subdivision of a process, while a process is an independent execution unit
- **Memory**: A thread shares memory within a process, while a process gets its own memory space for each process
- **Communication**: A thread can easily communicate through shared resources, while a process has to communicate through inter-process communication
- **Crash Impact**: A thread that crashes can impact the process, while a process crash is isolated to just the process

### <ins>Why Threads?</ins>
1. **Improved Performance**:
    - Threads can run in parallel on multiple CPU cores.
    - It reduces idle CPU time
2. **Shared Resources**
    - Threads within the same process share resources, making communication faster
3. Efficient Context Switching
    - Switching between threads is faster than switching between processes because they share the same memory space
  
### <ins>Thread Lifecycle</ins>
1. **New**: Created but not yet started.
2. **Runnable**: Ready to run but waiting for the CPU.
3. **Running**: Actively executing on a CPU core.
4. **Blocked/Waiting**: Waiting for an event (e.g., I/O).
5. **Terminated**: Completed execution.

### <ins>Thread Operations</ins>
1. Creating threads
    - In C:
       ```
       pthread_t thread_id;
       pthread_create(&thread_id, NULL, thread_function, NULL);
       ```
    - In Python:
       ```
       import threading
       t = threading.Thread(target=some_function)
       t.start()
       ```
2. Synchronization
   
   To prevent race conditions(Threads accessing shared data at the same time), they use a locking mechanism
    - In C use mutex:
       ```
       pthread_mutex_lock(&mutex);
       // critical section
       pthread_mutex_unlock(&mutex);
       ```
    - In Python use threading.Lock:
       ```
       lock = threading.Lock()
       with lock:
       # critical section
       ```
4. Termination
   
    Threads will terminate when their associated function is done executing or explicitly stopped (rare)

## Memory Management

Memory management is the process of controlling and coordinating computer memory (RAM) between different processes, threads, and the operating system (OS). It involves allocation, access, and release of memory efficiently while ensuring safety and performance.

### <ins>Key Concepts</ins>
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
  
### <ins>Virtual Memory In Depth</ins>
1. Goals of Virtual Memory:
    - **Transparency**: How can the OS implement virtual memory so that it is invisible to the running program. The running program should not be aware that memory is being virtualized. It should act as if it has its own private physical memory. From the running programs perspective, it looks like its address space starts at memory address 0, but in reality it is being translated into a physical address.
    - **Efficiency**: The OS should strive to make virtual memory efficient in both time and space. In order to implement this the OS has to rely on hardware support, for example TLBs
    - **Protection**: The OS should make sure to protect processes from one another as well as the OS itself. When a process performs a write to a memory address, it needs to make sure that it is not accessing memory from another process. No process should be able to interact with another processes memory space, therefore protection gives us the property of **isolation**
2. Translation:
    - **The Problem**: From the programs perspective, its address space starts at address 0 and grows until its max size (in this case use 16kb as an example). Therefore, any references to an address should be within the bounds of 0-16kb. Thus the problem is: How can we relocate this process in memory in a way that is **transparent** to the process. How can we provide the illusion that memory starts at 0, but in reality it starts somewhere else in physical memory.
    - **Dynamic (Hardware-based) Relocation**: The idea is referred to **base and bounds** or **dynamic relocation**. Both terms can be used interchangeably. The idea is that we use two registers within each CPU. One is called the base register and the other is called the bounds register (sometimes called the limit register). These registers allow us to place the address space anywhere in physical memory while ensuring that each process can only access its own address space. When the program is running and it references a memory address, translation occurs ```physical address = virtual address + base``` The base and bounds registers are hardware structures kept on the chip (one pair per CPU), this part of the processer is sometimes called the memory management unit (MMU).
  
### <ins>Memory Allocation Techniques</ins>
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
     
### <ins>Common Memory Issues</ins>
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

## File Systems

### <ins>Purpose</ins>
1. 
