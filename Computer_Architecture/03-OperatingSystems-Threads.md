# Threads
A **thread** is the smallest unit of execution in a program. Threads exist within a program, and every program has at least one thread. This thread is called the main thread. Threads enable parallelism within a program, which allow it to perform multiple tasks simultaneously

## Threads vs. Processes
- **Definition**: A thread is a subdivision of a process, while a process is an independent execution unit
- **Memory**: A thread shares memory within a process, while a process gets its own memory space for each process
- **Communication**: A thread can easily communicate through shared resources, while a process has to communicate through inter-process communication
- **Crash Impact**: A thread that crashes can impact the process, while a process crash is isolated to just the process

## Why Threads?
1. **Improved Performance**:
    - Threads can run in parallel on multiple CPU cores.
    - It reduces idle CPU time
2. **Shared Resources**
    - Threads within the same process share resources, making communication faster
3. Efficient Context Switching
    - Switching between threads is faster than switching between processes because they share the same memory space
  
## Thread Lifecycle
1. **New**: Created but not yet started.
2. **Runnable**: Ready to run but waiting for the CPU.
3. **Running**: Actively executing on a CPU core.
4. **Blocked/Waiting**: Waiting for an event (e.g., I/O).
5. **Terminated**: Completed execution.

## Thread Operations
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
