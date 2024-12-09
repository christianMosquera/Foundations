# Radare2 (r2)
Radare2 (r2) is one of the most powerful tools for reverse engineering. Unlike Ghidra, it is a command-line based tool, which gives you more control but also has a steeper learning curve. Radare2 is useful for analyzing ELF, PE, and binary files, and is heavily used in binary exploitation and vulnerability research.

## What Is Radare2?
Radare2 (r2) is an open-source reverse engineering framework with support for:

- Static Analysis: Disassemble binaries without running them.
- Dynamic Analysis: Attach to running processes, like GDB.
- Patching: Modify instructions directly in the binary.
- Emulation: Run instructions without full execution.

## Radare2 Interface Overview
Radare2 is command-line only, so learning its commands and workflow is crucial.
Here are the key modes and views you’ll encounter.

| Mode                     | Command | Purpose                                       | 
| ------------------------ | ------- | --------------------------------------------- |
| Visual Mode              | V       | Interactive view of disassembly.              | 
| Hexdump View             | px      | Shows raw hex bytes of the binary.            | 
| Disassembler View        | pdf     | Shows disassembled instructions.              |
| Pseudo-code View         | pdc     | Displays C-like decompiled code.              |
| CFG (Control Flow Graph) | VV      | Shows a graphical control flow of a function. |
| Scriptable Commands      | r2pipe  | Allows scripting via Python.                  |

## Key Commands for Radare2

| Command	          | Action                                               |
| ----------------- | ---------------------------------------------------- |
| r2                | filename	Open a binary (like ELF or PE)             |
| aa                | Analyze functions, symbols, and calls.               |
| afl	              | List all discovered functions.                       |
| pdf @ main        |	Disassemble function main.                           |
| s main            | Seek to the start of main.                           |
| V	                | Enter visual mode. Use q to exit.                    |
| VV	              | Show a graphical control flow graph (CFG).           |
| px	              | View hexdump of the binary.                          | 
| pdc	              | View decompiled pseudo-code.                         |
| pdf	              | Print disassembled function.                         | 
| aaaa	            | Auto-analyze everything (functions, symbols, calls). |
| e anal.bb=true    | Enable basic block analysis.                         |
| wa jmp 0x08048400	| Patch the binary to jump to an address.              |
| wop	              | Show previous patches.                               |
| q	                | Quit Radare2.                                        |
| i	                | Display binary info.                                 |
| is                | Show strings in the binary.                          |
