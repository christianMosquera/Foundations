# ELF Basic Information
ELF stands for Executable Linkable Format. ELF is used for executables, shared libraries, object files, core dumps, and more.

## Segments and Sections
- Each ELF file can contain 0 or more sections and 0 or more segments
- The main difference between sections and segments is that segments are used during runtime, while sections are used during link time.
- This means it is valid to have an ELF executable that only has segments, and to have an ELF object file that has only sections.
