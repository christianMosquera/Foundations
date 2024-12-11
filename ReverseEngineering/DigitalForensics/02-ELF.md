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
An ELF header resides at the beginning and holds a "road map" describing the file's organization. View the ELF header with ```readelf -h ./binary```
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

typedef struct {                                                    typedef struct {
        unsigned char   e_ident[EI_NIDENT];                                 unsigned char   e_ident[EI_NIDENT];
        Elf32_Half      e_type;                                     	    Elf64_Half      e_type;                        
        Elf32_Half      e_machine;                                          Elf64_Half      e_machine;
        Elf32_Word      e_version;                                          Elf64_Word      e_version;
        Elf32_Addr      e_entry;                                            Elf64_Addr      e_entry;
        Elf32_Off       e_phoff;                                            Elf64_Off       e_phoff;
        Elf32_Off       e_shoff;                                            Elf64_Off       e_shoff;
        Elf32_Word      e_flags;                                            Elf64_Word      e_flags;
        Elf32_Half      e_ehsize;                                           Elf64_Half      e_ehsize;
        Elf32_Half      e_phentsize;                                        Elf64_Half      e_phentsize;
        Elf32_Half      e_phnum;                                            Elf64_Half      e_phnum;
        Elf32_Half      e_shentsize;                                        Elf64_Half      e_shentsize;        
        Elf32_Half      e_shnum;                                            Elf64_Half      e_shnum;
        Elf32_Half      e_shstrndx;                                         Elf64_Half      e_shstrndx;
} Elf32_Ehdr;                                                       } Elf64_Ehdr;
```
1. **e_ident** is an unsigned char array of size EI_NIDENT which is defined as 16. The e_ident array contains the following:
    - **Magic Number**: First 4 bytes of the array.
      - Hex 0x7f follwed by ASCII Hex for the letters E L F (0x45, 0x4c, 0x46)
    - **EI_CLASS (Class)**: 5th byte, determines the class of the file (32-bit or 64-bit)
    - **EI_DATA (Data)**: 6th byte, tells us how it processes negative numbers and determines the endianess
    - **EI_VERSION (Version)**: 7th byte, only valid value is 0x1
    - **EI_OSABI (OS/ABI)**: 8th byte, value is expected to be 0x0 unless using a type specifc to ARM
    - **EI_ABIVERSION (ABI Version)**: 9th byte, usually just 0x0
    - **EI_PAD (padding)**: 10-16 bytes, currently unused and reserved for future use
2. **e_type** (Type) corresponds to which type of binary file it is (executables, shared libraries, relocatables, core dump)
3. **e_machine** (Machine) tells us what type of processor architecture that the binary is compiled for
4. **e_version** (Version) tells us the object file version, typically 0x1
5. **e_entry** (Entry point address) it tells us the address where the code execution will start, it represents an address when the program is loaded into memory
6. **e_phoff** (Start of program headers) it tells us how many bytes into the file the start of the program headers can be found
7. **e_shoff** (Start of section headers) it tells us how many bytes into the file the start of the section headers can be found
8. **e_flags** (Flags) it contains processor specific flags
9. **e_ehsize** (Size of this header) contains the size of the ELF header
10. **e_phentsize** (Size of program headers) defines the size of each entry (program header) in the program header table
11. **e_phnum** (Number of program headers) tells us how many program headers are in the program header table
12. **e_shentsize** (Size of section headers) defines the size of each entry (section header) in the section header table
13. **e_shnum** (Number of section headers) tells us total number of section headers in the section header table
14. **e_shstrndx** (Section header string table index) tells us where to look in the section header to find the entry where it defines the strings for the sections

## Section Headers
Section headers define sections within the binary. An object file's section header table lets one locate all the file's sections. The section header table is an array of Elf32_Shdr or Elf64_Shdr structure. Some important information about the section headers in the ELF header are ```e_shoff```, ```e_shentsize```, ```e_shnum```, and ```e_shstrndx```

### <ins>Sections</ins>
This is a list of a few common sections that can be found
1. .text: This section holds the 'text' or the executable instructions of the program
3. .bss: Holds the programs uninitalized data, usually it is filled with 0's when the program runs
4. .data: Holds initialized data for the program
5. .rodata: Holds read only data
6. .shstrtab: Holds the strings for the sections names
7. .got: Global Offset Table, used when calling functions from shared libraries
8. .plt: Procedure Linkage Table, used when calling functions from shared libraries
9. .interp: Holds the path name for the program interpreter
10. .dynamic: Holds important information about dynamic linking


### <ins>Section Header Structs</ins>
```
	typedef struct {                                          typedef struct {
		Elf32_Word	sh_name;                          	Elf64_Word	sh_name;
		Elf32_Word	sh_type;                          	Elf64_Word	sh_type;
		Elf32_Word	sh_flags;                         	Elf64_Xword	sh_flags;
		Elf32_Addr	sh_addr;                          	Elf64_Addr	sh_addr;
		Elf32_Off	sh_offset;                         	Elf64_Off	sh_offset;
		Elf32_Word	sh_size;                           	Elf64_Xword	sh_size;
		Elf32_Word	sh_link;                          	Elf64_Word	sh_link;
		Elf32_Word	sh_info;                           	Elf64_Word	sh_info;
		Elf32_Word	sh_addralign;                     	Elf64_Xword	sh_addralign;
		Elf32_Word	sh_entsize;                       	Elf64_Xword	sh_entsize;
	} Elf32_Shdr;                                             } Elf64_Shdr;
```
1. **sh_name** contains the offset into the section header string table that contains the string name for this section
2. **sh_type** tells us what type the section is and determines the sections contents
3. **sh_flags** are bit masked flags that describe various attributes
4. **sh_addr** is the location in memory this section gets loaded to (if it gets loaded, not all sections get loaded into memory)
5. **sh_offset** is the file offset where this section is located
6. **sh_size** is the size of this section in bytes
7. **sh_link** is the section header table index link
8. **sh_info** holds additional information depending on the section
9. **sh_addralign** specifes an address alignment constraint for the section (sh_addr % sh_addralign == 0)
10. **sh_entsize** specifies the size of each entry if this section is a table


## Program Headers
Program headers are stored in the program header table. Program headers are structures that define segments. The segments that are defined, provide information that relates to the program during execution. They describe information needed by the linker/loader to load the binary into memory and execute the program. Therefore, program headers are only relevant during run time.

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
```
	typedef struct {                                   	typedef struct {
		Elf32_Word	p_type;                    		Elf64_Word	p_type;
		Elf32_Off	p_offset;                  		Elf64_Word	p_flags;
		Elf32_Addr	p_vaddr;                   		Elf64_Off	p_offset;
		Elf32_Addr	p_paddr;                   		Elf64_Addr	p_vaddr;
		Elf32_Word	p_filesz;                  		Elf64_Addr	p_paddr;
		Elf32_Word	p_memsz;                   		Elf64_Xword	p_filesz;
		Elf32_Word	p_flags;                   		Elf64_Xword	p_memsz;
		Elf32_Word	p_align;                    		Elf64_Xword	p_align;
	} Elf32_Phdr;                                      	} Elf64_Phdr;
```
1. **p_type** defines what type of segment it is (more detail in Type section above)
2. **p_offset** indicates the file offset where this segment starts (within the binary)
3. **p_vaddr** indicates the location in memory where this segment is expected to be loaded into, due to OS protections such as ASLR, this may not be accurate
4. **p_paddr** indicates the physical address, but most times it is same as p_vaddr
5. **p_filesz** indicates the number of bytes this segment takes up in the file
6. **p_memsz** indicates the size in bytes of the segment when it is loaded into process memory. It may be bigger than p_filesz due to uninitialized data
7. **p_flags** is the value of bit-masked flags that are relevant to the segment (readable, writeable, executable)
8. **p_align** is the value that the segments are aligned to in the file and memory
