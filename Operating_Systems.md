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
