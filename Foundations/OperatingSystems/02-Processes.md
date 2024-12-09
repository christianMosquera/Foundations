

# Processes
## What is a Process
A process is a running instance of a program. When you execute a program, the operating system: loads the program’s code into memory, allocates resources (CPU, memory, file descriptors), and creates a process to manage the execution of the program.

## Key Components of a Process
1. Code: The instructions of the program
2. Data: Static variables, constants, and global variables
3. Heap: Dynamically allocated memory
4. Stack: Stores function calls, local variables, and control flow data
5. Registers: The CPU registers used during execution
6. Program Counter (PC): Stores the next instruction to execute

## Process States
A process goes through multiple states during its lifecycle. The most common are:
1. New: The process is being created
2. Ready: The process is loaded into memory and waiting to be executed
3. Running: The process is actively executing instructions on the CPU
4. Blocked/Waiting: The process is waiting for an event
5. Terminated: The process has finished execution

## Process Context Switching
Since modern computers execute multiple processes, the CPU must switch between these processes to ensure multitasking. This is called context switching.

Steps in a context switch:
1. Save the current processes state (registers, PC, etc) in the Process Control Block (PCB)
2. Load the new process’s state from the PCB
3. Update the CPU’s Program Counter to the new process’s instruction

This operations is managed by the operating systems kernel and is essential for multitasking

## Process Control Block (PCB)
The Process Control Block is a data structure in the OS that stores information about a process. It contains:
- Process ID (PID): An unique identifier for the process
- Process State: The state of the process, running, ready, etc
- CPU Registers: A snapshot of the process’s register values
- Memory Info: Details about memory allocation (stack, heap, etc)
- I/O Status: Open files and devices being used

## Practical Example
Running the ls command in linux

1. Execution:
    - You type the ls command in the terminal. The shell creates a new process calling fork()
    - The process executes the ls command by calling execve()
2. Lifecycle:
    - New: Process is created
    - Ready: Process is waiting to be scheduled by CPU
    - Running: CPU is executing the ls command
    - Terminated: The process completes execution and resources are freed
