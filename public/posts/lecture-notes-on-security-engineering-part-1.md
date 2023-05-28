Lecture notes on security engineering, part 1: Introduction
Michael Sjöberg
Aug 27, 2022
May 27, 2023

This is the first post in a series of lecture notes on security engineering, based on the postgraduate-level course with the same name at King's College London. These notes cover most of the topics, but not as deep and without assignments, and are primarily intended as a reference for myself.

## <a name="1" class="anchor"></a> [Computer programs and compilation](#1)

A computer program is a sequence of bits (0 or 1), and organized as 8-bit bytes, where one byte is 8-bits and each byte represent some text character (ASCII standard). Most programs are developed in a high-level programming language, then compiled, or translated, into an object file, which is executed by a process, and finally terminated.

An object file contains application and libraries, such as program code in binary format, relocation information, which are things that need to be fixed once loaded into memory, symbol information as defined by object (or imported), and optional debugging information (note that some interpreted languages are translated into an intermediate format).

#### Compilation system

```c
/* hello.c */
#include <stdio.h>

int main() {
    printf("hello c\n");
    return 0;
}
```

The compilation system typically includes a preprocessor, compiler, assembler, and linker, and is used to translate programs into a sequence of machine-language instructions, which is packed into an executable object file.

The compilation process (note that most program code in this post refer to programs written in the C programming language and using [gcc](https://linux.die.net/man/1/gcc) to compile):

- preprocessor, such as [cpp](https://linux.die.net/man/1/cpp), modifies the program according to directives that begin with `#`, in this case `#include <stdio.h>`, and are imported into the program text, resulting in an intermediate file with `.i` suffix, use flag `-E` to see intermediate file

- compiler translates the intermediate file into an assembly program with `.s` suffix, where each line describe one instruction, use flag `-S` to generate assembly program (note that below assembly is generated on 64-bit macOS, operand size suffix and special directives for assembler is omitted for clarity)

    ```x86asm
    .section __TEXT
        .globl  _main

    _main:
        push    %rbp
        mov     %rsp, %rbp
        sub     $16, %rsp
        mov     $0, -4(%rbp)
        lea     L_.str(%rip), %rdi
        mov     $0, %al
        call    _printf
        xor     %eax, %eax
        add     $16, %rsp
        pop     %rbp
        ret

    .section __TEXT
        L_.str: .asciz  "hello c\n"
    ```

- assembler, such as [as](https://linux.die.net/man/1/as), translates assembly program instructions into machine-level instructions, use flag `-c` to compile assembly program, resulting in a relocatable object file with `.o` suffix (this is a binary file)

- linker, such as [ld](https://linux.die.net/man/1/ld), merges one or more relocatable object files, which are separate and precompiled `.o`-files that the program is using, such as `printf.o`, resulting in an executable file (or simply executable) with no suffix, ready to be loaded into memory and executed by the system

Linking is the process to resolve references to external objects, such as variables and functions (for example `printf`), where static linking is performed at compile-time and dynamic linking is performed at run-time.

#### Tools

Useful tools for working with programs from a systems perspective:

- [gdp](https://www.sourceware.org/gdb/), GNU debugger
- `objdump` such as `objdump -d <filename>` to display information about object file (disassembler)
    -  `gcc hello.c && objdump -d hello` (alternative to `gcc -S` to see assembly)
- `readelf` to display information about ELF files
- `elfish` to manipulate ELF files
- `hexdump` to display content in binary file
- `/proc/pid/maps` to show memory layout for process

## <a name="2" class="anchor"></a> [Computer organisation](#2)

Most modern computers are organized as an assemble of buses, I/O devices, main memory, and processor:

- buses are collections of electrical conduits that carry bytes between components, usually fixed sized and referred to as words, where the word size is 4 bytes (32 bits) or 8 bytes (64 bits)
- I/O devices are connections to the external world, such as keyboard, mouse, and display, but also disk drives, which is where executable files are stored
    - controllers are chip sets in the devices themselves or main circuit board (motherboard)
    - adapters are cards plugged into the motherboard

- main memory is a temporary storage device that holds program and data when executed by the processor, physically, it is a collection of dynamic random-access memory (DRAM) chips, and logically, it is a linear array of bytes with its own unique address (array index)
- processor, or central processing unit (CPU), is the engine that interprets and executes the machine-level instructions stored in the main memory

#### CPU

The CPU has a word size storage device (register) called program counter (PC) that points at some instruction in main memory. Instructions are executed in a strict sequence, which is to read and interpret the instruction as pointed to by program counter, perform operation (as per the instruction), and then update the counter to point to next instruction (note that each instruction has its own unique address).

An operation, as performed by the CPU, use main memory, register file, which is a small storage device with collection of word-size registers, and the arithmetic logic unit (ALU) to compute new data and address values:

- load, copy byte (or word) from main memory into register, overwriting previous content in register
- store, copy byte (or word) from register to location in main memory, overwriting previous content in location
- operate, copy content of two registers to ALU, perform arithmetic operation on the two words and store result in register, overwriting previous content in register
- jump, extract word from instruction and copy that word into counter, overwriting previous value in counter

## <a name="3" class="anchor"></a> [Cache memory](#3)

A cache memory describe memory devices at different levels, or sizes (L1, L2, L3), where smaller sizes are faster (this locality can be exploited to make programs run faster).

|    |     |     |
| -- | :-- | :-- |
| L0 | Register | CPU register hold words copied from cache memory |
| L1 | SRAM     | L1 hold cache lines copied from L2 |
| L2 | SRAM     | L2 hold cache lines copied from L3 |
| L3 | SRAM     | L3 hold cache lines copied from main memory |
| L4 | DRAM (main memory) | Main memory hold disk blocks from local disks |
| L5 | Local secondary storage (local disks) | Local disks hold files copied from other disks or remote network storage |
| L6 | Remote secondary storage |  |

The different levels of memory is also referred to as [memory hierarchy](https://en.wikipedia.org/wiki/Memory_hierarchy), which is based on response time, and where each level act as cache for the level before and top levels are used to store information that the processor might need in the near future.

## <a name="4" class="anchor"></a> [Operating systems](#4)

The operating system provides services to programs, which are used via abstractions to make it easier to manipulate low-level devices and to protect the hardware. A shell provides an interface to the operating system via the command-line (or library functions):

- when opened, waiting for commands and read each character as it is typed into some register, which is then stored in memory
- load executable file, such as `./hello` (command to execute program `hello`), which basically copies the code and data in file from disk to main memory
- once loaded, the processor starts to execute the machine-language instructions in the `main` routine, which corresponds to the function `main` in program, copies the data from memory to register, and then from register to display device, which should output the string "hello assembly"

A system command is a command-line program (`system()` is a library function to execute shell commands in C programs, for more system commands, see: [List of Unix commands](https://en.wikipedia.org/wiki/List_of_Unix_commands)):

- sort files with `ls | sort`

    ```c
    int main() {
        system("ls | sort");
        return 0;
    }
    ```

- copy files with `cp`, such as `cp ~/file /dest` (note that `~` expands into `HOME` environment variable)

A system call is a function executed by the operating system, such as accessing hard drive and creating processes, whereas system command is a program implementing functions (system calls are used by programs to request services from the operating system). In C, and most other programming languages, system commands, such as `write`, is wrapped in some other function, such as `printf`.

#### Processes

A process is an abstraction for processor, main memory, and I/O devices, and represent a running program, where its context is the state information the process needs to run. The processor can switch between multiple running programs, such as shell and program, using context switching, which saves the state of current process and restores the state of some new process.

A thread is multiple execution units within a process with access to the same code and global data, and kernel is collection of code and data structures that is always in memory. A kernel is used to manage all processes and called using a system call instruction, or `syscall`, which transfers control to the kernel when the program need some action by the operating system, such as read or write to file.

#### Virtual memory

Virtual memory is an abstraction for main memory and local disks, which provides each program with a virtual address space to make it seem as if programs have exclusive use of memory:

- program code and data, code begin at the same fixed address for all processes at bottom of virtual address space, followed by global variables, and is fixed size once process is running
- heap expands and contracts its size dynamically at run-time using special routines, such as `malloc` and `free` (or using garbage collection)
- shared libraries, such as the C standard library, is near the middle of the virtual address space
- stack is at top of user virtual address space and used by compiler to implement function calls, it expands and contracts its size dynamically at run-time (note that stack grows with each function call and contracts on return)
- kernel virtual memory is at top of virtual memory space, which is reserved for kernel, programs must call kernel to read or write in this space

#### Files

A file is an abstraction for I/O devices, which provides a uniform view of devices, where most input and output in a system is reading and writing to files.

#### [Next part](lecture-notes-on-security-engineering-part-2)
