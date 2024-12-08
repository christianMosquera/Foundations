# CPU and Memory
A very basic overview of computer architecture. By no means am I an expert on this subject, but I try my best to explain this subject at a shallow level.

## CPU (Control Processing Unit)
The CPU can be defined as the "brains" of the computer. In this section we will talk about the components of the CPU and the Fetch-Decode-Execute cycle

### <ins>Control Unit (CU)</ins>
**Role**: Orchestrates operations by directing data flow between CPU, memory, and peripherals.

**Key tasks**:
* Fetching instructions from memory (based on the program counter or instruction pointer)
* Decoding instructions into operations the CPU can perform
* Managing the sequence of operations (branch prediction)

### <ins>Arithmetic Logic Unit (ALU): The Calculator</ins>
**Role**: Performs arithmetic operations (+, -, /, *) and logical expressions (AND, OR, XOR)

**Operations**:
* Integer arithmetic: Fundamental for almost all programs
* Logical operations: Essential for decision making (if branches)
* Bitwise operations: Often used in low level programming, like setting or clearing specific bits in a register

### <ins>Registers: The Immediate Storage</ins>
**Role**: Temporary fast storage for immediate computations

**Purpose**: Accessing a register is orders of magnitude faster than accessing RAM

### <ins>Cache: Bridging The Speed Gap</ins>
Hierarchy:
* L1 Cache: Closest to CPU, smallest, fastest
* L2 Cache: Larger, but slower
* L3 Cache: Shared among cores, even larger, but even slower

Purpose: Reduces latency by storing frequently used instructions/data close to the CPU

### <ins>The Fetch-Decode-Execute Cycle</ins>
Every operation your computer does, is broken down into this fundamental cycle:


1. **Fetch**: CPU fetches the instruction from memory (using the address in the Instruction Pointer, RIP/EIP)
    - The Instruction Pointer (PC, RIP/EIP) holds the address of the next instruction to be executed. Therefore after the CPU fetches it from memory, it is updated to point to the next instruction
    - The address in then loaded into the Instruction Register, which is what holds the address of the instruction that is currently being decoded and executed

2. **Decode**: The control unit interprets the instruction
    - MOV AX, 5 translates into copying the value 5 into the AX register

3. **Execute**: The ALU performs the operation or the CU directs data transfer
    - The CPU writes 5 into the memory location for AX
  
## Memory: The CPUs Playground
Memory is a hierarchy, ranging from fast but small (registers, cache) to large but slow (main memory, disk)
### <ins>Memory Hierarchy</ins>
1. **Registers**: Fastest and smallest, directly accessible to the CPU
2. **Cache**: Stores frequently accessed data to reduce latency
3. **Main Memory (RAM)**: Volatile, meaning that when the power is turned off it loses its data. Stores active programs and data
4. **Secondary Storage (Disk)**: Non-volatile, does not lose data when turned off. Much larger than RAM, but slower.
### <ins>Stack</ins>
- **Purpose**: The purpose of the stack is to store temporary data for function execution.
- **Structure**:
  - Organized as a LIFO (Last in First out) data structure
  - Each function pushes a stack frame onto the stack
  - Contains: 
    - **Return address**: Where to resume execution after the function ends
    - **Local variables**: These are defined inside of the function
    - **Saved registers**: This preserves the CPU state
- **Growth**: The stack grows downward towards lower memory. The stack starts at the top of the stack with high memory addresses.
- **Pros**:
  - Fast allocation and deallocation via push and pop
- **Cons**:
  - Limited size, the stack can overflow

### <ins>Heap</ins>
- **Purpose**: The heap is used for dynamic allocation, the heap can grow and shrink. This is done via the malloc call in C.
- **Structure**:
    - Managed by the OS and runtime libraries 
    - Memory is explicitly allocated and freed by the programmer
- **Growth**: The heap grows upward in memory
- **Pros**:
  - The heap is flexible, it allows allocation at runtime
- **Cons**:
  - The heap is slower than stack memory and requires manual management which has a risk of memory leaks

## Registers: The CPU’s Workbench
Registers are essential for executing instructions as they hold the data for the CPU to process
### <ins>General Purpose Registers</ins>
Used to store integers, addresses, or intermediate values. Example on a x86 architecture:
- EAX: Accumulator 
- EBX: Base register
- ECX: Counter for loops
- EDX: Data register for I/O operations
### <ins>Special Purpose Registers</ins>
- EIP/RIP: Holds the address of the next instruction to execute
- ESP/RSP: Stack pointer, which points to the top of the stack
- EBP/RBP: Base pointer, which points to the base of the current stack frame
