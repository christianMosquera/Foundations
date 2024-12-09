# System Calls

## What Is A System Call?
A system call is a mechanism that allows user-space applications to request services from the kernel. The kernel has higher privileges (runs in kernel mode) and controls access to hardware, memory, and other resources that user-space processes cannot access directly.

```
int fd = open("file.txt", O_RDONLY);
```
When you run open(), it triggers a system call to the kernel in order to open the file that is being requested.

## Why Do We Need System Calls?
- **Security**: Direct hardware access is unsafe, so the kernel controls access to files, devices, and memory.
- **Abstraction**: System calls provide a simple API to use in order to do low level things (open(), close(), write(), read(), etc...)
- **Portability**: Applications do not need to know the details of the hardware or system internals

## How Do System Calls Work?
1. User-Mode Request:
    - The application calls a C library function like open(), read(), or write().
2. Library Wrapper (Glibc):
    - The function in the C standard library (glibc) calls a system call instruction (like syscall in x86-64).
3. Trap into Kernel Mode:
    - A special instruction (like int 0x80 on x86 or syscall on x86-64) switches the CPU from user mode to kernel mode.
4. Kernel Handler:
    - The kernel performs the requested action (like opening a file or reading from it) using the provided arguments.
5. Return to User Mode:
    - The kernel switches back to user mode and returns a result (like a file descriptor or error code) to the user process.
  
## How to Invoke a System Call?
There are two primary ways to invoke system calls in Linux:

1. Library Call (Preferred):

    - Use C functions like open(), read(), write(), etc.
    - Simpler, portable, and easier to debug.
2. Direct Syscall (Advanced):

    - Use the syscall function directly.
    - More control, but less portable and harder to read.
  
## How to Trace System Calls?
Use strace to see system calls made by a program. strace stands for "system trace". It intercepts and logs system calls made by a program and displays their arguments, return values, and error codes. This provides a real-time view of how a program behaves internally.
```
strace ls
```
Example of system calls from ls:
```
open("/etc/ld.so.cache", O_RDONLY) = 3
open("/lib/libc.so.6", O_RDONLY) = 3
write(1, "file1 file2\n", 12) = 12
```
## Key Concepts to Remember
1. User Space vs Kernel Space:
    - User applications run in user space (low-privilege).
    - The kernel runs in kernel space (high-privilege).
2. Switching Modes:
    - A system call switches from user mode to kernel mode.
3. System Call Numbers:
    - Each system call has a unique ID in the system call table.
