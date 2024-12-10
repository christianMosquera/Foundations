# ELF Basic Information
ELF stands for Executable Linkable Format. ELF is used for executables, shared libraries, object files, core dumps, and more.

## Structure Of ELF
Object files participate in program linking (building a program) and program execution (running a program). For convenience and efficiency, the object file format provides parallel views of a file's contents, reflecting the differing needs of those activities

```
                            +--------------------+                   +--------------------+  
                            |     ELF Header     |                   |     ELF Header     |
                            +--------------------+                   +--------------------+
                            |   Program Header   |                   |   Program Header   |
                            |  Table (optional)  |                   |  Table (required)  | 
                            +--------------------+                   +--------------------+ 
                            |     Section 1      |                   |     Section 1      |
                            +--------------------+                   +--------------------+
                            |        ...         |                   |        ...         |
                            +--------------------+                   +--------------------+
                            |     Section n      |                   |     Section n      |  
                            +--------------------+                   +--------------------+
                            |        ...         |                   |        ...         |
                            +--------------------+                   +--------------------+
                            |   Section Header   |                   |   Section Header   |
                            |  Table (required)  |                   |  Table (required)  |
                            +--------------------+                   +--------------------+
                                 Linking View                            Execution View
```

## ELF Header
An ELF header resides at the beginning and holds a ``road map'' describing the file's organization. View the ELF header with ```readelf -h ./binary```
```
$ readelf -h ./program
ELF Header:
 Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
 Class:                             ELF64
 Data:                              2's complement, little endian
 Version:                           1 (current)
 OS/ABI:                            UNIX - System V
 ABI Version:                       0
 Type:                              DYN (Shared object file)
 Machine:                           Advanced Micro Devices X86-64
 Version:                           0x1
 Entry point address:               0x1100
 Start of program headers:          64 (bytes into file)
 Start of section headers:          14928 (bytes into file)
 Flags:                             0x0
 Size of this header:               64 (bytes)
 Size of program headers:           56 (bytes)
 Number of program headers          13
 Size of section headers:           64 (bytes)
 Number of section headers:         31
 Section header string table index: 30

$ hexdump -C -n 64 ./program
00000000  7f 45 4c 46 02 01 01 00  00 00 00 00 00 00 00 00  |.ELF............|
00000010  03 00 3e 00 01 00 00 00  00 11 00 00 00 00 00 00  |..>.............|
00000020  40 00 00 00 00 00 00 00  50 3a 00 00 00 00 00 00  |@.......P:......|
00000030  00 00 00 00 40 00 38 00  0d 00 40 00 1f 00 1e 00  |....@.8...@.....|
00000040
```
Here the readelf command shows us the ELF header. And below that the hexdump shows the ELF header layed out in memory. 

### <ins>ELF Header Structs</ins>
This is how the ELF header is defined in both 32 and 64-bit:
```
#define EI_NIDENT 16

typedef struct {
        unsigned char   e_ident[EI_NIDENT];
        Elf32_Half      e_type;
        Elf32_Half      e_machine;
        Elf32_Word      e_version;
        Elf32_Addr      e_entry;
        Elf32_Off       e_phoff;
        Elf32_Off       e_shoff;
        Elf32_Word      e_flags;
        Elf32_Half      e_ehsize;
        Elf32_Half      e_phentsize;
        Elf32_Half      e_phnum;
        Elf32_Half      e_shentsize;
        Elf32_Half      e_shnum;
        Elf32_Half      e_shstrndx;
} Elf32_Ehdr;

typedef struct {
        unsigned char   e_ident[EI_NIDENT];
        Elf64_Half      e_type;
        Elf64_Half      e_machine;
        Elf64_Word      e_version;
        Elf64_Addr      e_entry;
        Elf64_Off       e_phoff;
        Elf64_Off       e_shoff;
        Elf64_Word      e_flags;
        Elf64_Half      e_ehsize;
        Elf64_Half      e_phentsize;
        Elf64_Half      e_phnum;
        Elf64_Half      e_shentsize;
        Elf64_Half      e_shnum;
        Elf64_Half      e_shstrndx;
} Elf64_Ehdr;
```
1. e_ident is an unsigned char array of size EI_NIDENT which is defined as 16. The e_ident array contains the following:
    - **Magic Number**: First 4 bytes of the array.
      - Hex 0x7f follwed by ASCII Hex for the letters E L F (0x45, 0x4c, 0x46)
    - **EI_CLASS (Class)**: 5th byte, determines the class of the file (32-bit or 64-bit)
    - **EI_DATA (Data)**: 6th byte, tells us how it processes negative numbers and determines the endianess
    - **EI_VERSION (Version)**: 7th byte,
    - **EI_OSABI (OS/ABI)**: 8th byte,
    - **EI_ABIVERSION (ABI Version)**: 9th byte,
    - **EI_PAD (padding)**: 10-16 bytes,
2. e_type (Type)
3. e_machine (Machine)
4. e_version (Version)
5. e_entry (Entry point address)
6. e_phoff (Start of program headers)
7. e_shoff (Start of section headers)
8. e_flags (Flags)
9. e_ehsize (Size of this header)
10. e_phentsize (Size of program headers)
11. e_phnum (Number of program headers)
12. e_shentsize (Size of section headers)
13. e_shnum (Number of section headers)
14. e_shstrndx (Section header string table index)


## Program Headers
Program Headers are stored in the Program Header Table (In the case of the ELF Header, it starts 64 bytes into the file). In the Program Header Table contains entries to Program Headers (Hence the name), but they are also called segments. The Program Headers (segments) define where and how to load segments into memory. Program Headers are only meaningful for executable or shared object files. The size is determined by the ELF headers e_phentsize and e_phnum fields.

```
Program Headers:
 Type           Offset   VirtAddr           PhysAddr           FileSiz  MemSiz   Flg Align
 PHDR           0x000040 0x0000000000000040 0x0000000000000040 0x0001f8 0x0001f8 R   0x8
 INTERP         0x000238 0x0000000000000238 0x0000000000000238 0x00001b 0x00001b R   0x1
     [Requesting program interpreter: /lib/ld-linux-aarch64.so.1]
 LOAD           0x000000 0x0000000000000000 0x0000000000000000 0x003f7c 0x003f7c R E 0x10000
 LOAD           0x00fc48 0x000000000001fc48 0x000000000001fc48 0x000528 0x001190 RW  0x10000
 DYNAMIC        0x00fc58 0x000000000001fc58 0x000000000001fc58 0x000200 0x000200 RW  0x8
 NOTE           0x000254 0x0000000000000254 0x0000000000000254 0x0000e0 0x0000e0 R   0x4
 GNU_EH_FRAME   0x003610 0x0000000000003610 0x0000000000003610 0x0001b4 0x0001b4 R   0x4
 GNU_STACK      0x000000 0x0000000000000000 0x0000000000000000 0x000000 0x000000 RW  0x10
 GNU_RELRO      0x00fc48 0x000000000001fc48 0x000000000001fc48 0x0003b8 0x0003b8 R   0x1
```
### <ins>Type</ins>
- **PT_LOAD**: The array element specifies a loadable segment, described by p_filesz and p_memsz. The bytes from the file are mapped to the beginning of the memory segment. If the segment's memory size (p_memsz) is larger than the file size (p_filesz), the ``extra'' bytes are defined to hold the value 0 and to follow the segment's initialized area. The file size may not be larger than the memory size. Loadable segment entries in the program header table appear in ascending order, sorted on the p_vaddr member.
- **PT_DYNAMIC**: The array element specifies dynamic linking information
- **PT_INTERP**: The array element specifies the location and size of a null-terminated path name to invoke as an interpreter. This segment type is meaningful only for executable files (though it may occur for shared objects); it may not occur more than once in a file. If it is present, it must precede any loadable segment entry
- **PT_PHDR**: The array element, if present, specifies the location and size of the program header table itself, both in the file and in the memory image of the program. This segment type may not occur more than once in a file. Moreover, it may occur only if the program header table is part of the memory image of the program. If it is present, it must precede any loadable segment entry

### <ins>Program Header Structs</ins>
Here are the 32 and 64-bit representations of a Program Header:
```
typedef struct {
	Elf32_Word	p_type;
	Elf32_Off	p_offset;
	Elf32_Addr	p_vaddr;
	Elf32_Addr	p_paddr;
	Elf32_Word	p_filesz;
	Elf32_Word	p_memsz;
	Elf32_Word	p_flags;
	Elf32_Word	p_align;
} Elf32_Phdr;

typedef struct {
	Elf64_Word	p_type;
	Elf64_Word	p_flags;
	Elf64_Off	p_offset;
	Elf64_Addr	p_vaddr;
	Elf64_Addr	p_paddr;
	Elf64_Xword	p_filesz;
	Elf64_Xword	p_memsz;
	Elf64_Xword	p_align;
} Elf64_Phdr;
```
You can see how they are mapped to the above visual of the Program Headers.

## Segments and Sections
### <ins>Segments</ins>
Segments are almost exclusively used during runtime. Segments point to a place in memory and will specify how much memory the segment contains. The segments also specify where and how they should be loaded into memory. Segments tell the operating system how to map parts of the ELF file into memory.

```
Program Headers:
 Type           Offset   VirtAddr           PhysAddr           FileSiz  MemSiz   Flg Align
 PHDR           0x000040 0x0000000000000040 0x0000000000000040 0x0001f8 0x0001f8 R   0x8
 INTERP         0x000238 0x0000000000000238 0x0000000000000238 0x00001b 0x00001b R   0x1
     [Requesting program interpreter: /lib/ld-linux-aarch64.so.1]
 LOAD           0x000000 0x0000000000000000 0x0000000000000000 0x003f7c 0x003f7c R E 0x10000
 LOAD           0x00fc48 0x000000000001fc48 0x000000000001fc48 0x000528 0x001190 RW  0x10000
 DYNAMIC        0x00fc58 0x000000000001fc58 0x000000000001fc58 0x000200 0x000200 RW  0x8
 NOTE           0x000254 0x0000000000000254 0x0000000000000254 0x0000e0 0x0000e0 R   0x4
 GNU_EH_FRAME   0x003610 0x0000000000003610 0x0000000000003610 0x0001b4 0x0001b4 R   0x4
 GNU_STACK      0x000000 0x0000000000000000 0x0000000000000000 0x000000 0x000000 RW  0x10
 GNU_RELRO      0x00fc48 0x000000000001fc48 0x000000000001fc48 0x0003b8 0x0003b8 R   0x1
```
