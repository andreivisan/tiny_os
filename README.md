# Tiny OS

Building a tiny OS for learning purposes using [The Little Book about OS development](https://littleosbook.github.io/)

## Prerequesites 

### Hexadecimal system

**Basics:**

    - Digits: Uses 0–9 and A–F (where A=10, B=11, … F=15).
    - Relation to Binary: Each hex digit represents exactly 4 bits. For example, the binary 1101 translates to D in hexadecimal.
    - Why It’s Useful in OS Dev:
        - Memory Addresses: Memory locations and registers are typically represented in hex (e.g., 0x7FFFDDE0).
        - Debugging: Hexadecimal simplifies reading raw binary dumps and understanding bit patterns in low-level programming.
        - Compact Representation: It is much more compact than binary and aligns well with the underlying 8-, 16-, 32-, or 64-bit architectures.

**Conversions:**

    - Binary to Hex: Group the binary number into nibbles (4 bits) and convert each group.
        - Example: Binary 1010 1111 → Hex: A F → Written as 0xAF.
    - Decimal to Hex: Convert the decimal number to binary first or use repeated division by 16.

### Bitwise Operations & Algorithms

**Bitwise Operations Overview**

1. AND (&):
    - Compares each bit of two numbers; result bit is 1 only if both bits are 1.
    - Usage: Masking out bits (e.g., x & 0x0F retrieves the lower nibble).

2. OR (|):
    - Result bit is 1 if at least one bit is 1.
    - Usage: Setting specific bits (e.g., x | 0x80 sets the highest bit in a byte).

3. XOR (^):
    - Result bit is 1 only if the bits are different.
    - Usage: Toggling bits or swapping values without a temporary variable.

4. NOT (~):
    - Flips every bit in the number.
    - Usage: Creating bit masks for clearing or inverting bits.

5. Bit Shifts (<<, >>):
    - Left Shift (<<): Moves bits to the left, filling with zeros (equivalent to multiplying by 2 for each shift).
    - Right Shift (>>): Moves bits to the right (dividing by 2 for each shift), though the exact behavior can depend on whether the number is signed or unsigned.

**Common Bitwise Algorithms & Tricks**

- Setting a Bit:

```c
x |= (1 << n);  // Sets the nth bit of x.
```

- Clearing a Bit:

```c
x &= ~(1 << n);  // Clears the nth bit of x.
```

- Toggling a Bit:

```c
x ^= (1 << n);  // Toggles the nth bit of x.
```

- Checking a Bit:

```c
if (x & (1 << n)) { /* bit is 1 */ }
```

- Swapping Two Numbers using XOR (without a temporary variable):

```c
a ^= b;
b ^= a;
a ^= b;
```

- Counting Bits (Brian Kernighan's Algorithm):

Repeatedly clear the least significant set bit until the number becomes zero.

```c
int count = 0;
while (x) {
    x &= (x - 1);
    count++;
}
```

**Relevance in OS Development**

- Hardware Registers & Flags:

    - OS dev often involves reading from or writing to hardware registers where each bit can represent a flag or control a specific feature.
    - Example: A status register might have specific bits for error flags or device states.

- Memory Addressing:

    - Addresses and offsets are commonly represented in hexadecimal, which makes it easier to align with memory boundaries and perform calculations.
    
- Performance:

    - Bitwise operations are low-level, fast operations that are ideal for performance-critical code in an operating system.

## Random bits I need to learning

An ELF (Executable and Linkable Format) executable is the standard file format for executables, object code, shared libraries, and core dumps on Linux and Unix-like systems[1][6]. Originally designed by Unix System Laboratories in collaboration with Sun Microsystems for SVR4, it first appeared in Solaris 2.0 and has since become the standard format on Unix-like systems[4][6].

### Structure of ELF Files

An ELF file consists of several key components:

1. **ELF Header**: Located at the start of the file, it contains metadata about the file including:
   - A magic number (0x7F followed by "ELF") that identifies it as an ELF file[3][5]
   - Information about whether it's 32-bit or 64-bit
   - Endianness (little or big)
   - Target architecture
   - Entry point address[3][5]

2. **Program Header Table**: Describes segments used at runtime[1]

3. **Section Header Table**: Contains information about sections used during linking[1][2]

4. **Sections and Segments**: The actual data of the executable[1]

### Key Sections

ELF executables typically contain four main sections:

- **.text**: Contains the executable code with read and execute permissions[7]
- **.data**: Holds initialized data
- **.rodata**: Contains read-only data
- **.bss**: Stores uninitialized data[7]

### Characteristics

ELF is designed to be:

- **Portable**: Used across various Unix-like systems[1]
- **Extensible**: Supports dynamic linking, allowing code sharing between programs[1]
- **Efficient**: Optimized for quick loading and execution[1]
- **Versatile**: Can be used for different types of files (executables, object files, shared libraries)[4][6]

Unlike Windows which uses the PE (Portable Executable) format, Linux and Unix systems rely on ELF for their executable files.

### Booting

Booting an operating system consists of transferring control along a chain of small programs, each one more “powerful” than the previous one, where the operating system is the last “program”. See the following figure for an example of the boot process:

![boot chain](/docs/images/boot_chain.png)

### What is a linker?

A linker is a crucial tool in the software build process that combines multiple object files (compiled code) into a single executable or binary image. In the context of OS development, the linker (often guided by a linker script) ensures that every piece of code and data is placed at the correct memory addresses, which is critical for bootstrapping and running the OS.

Below, we break down the key points and explain the provided linker script in detail.

---

### 1. What Is a Linker?

- **Definition:**  
  A linker is a program that takes object files—outputs from the compiler—and merges them into a single executable, library, or binary image.

- **Primary Functions:**  
  - **Symbol Resolution:** It connects function calls and variable references to their definitions across multiple object files.
  - **Address Assignment:** It assigns final memory addresses to all symbols (functions, global variables, etc.).
  - **Section Placement:** It organizes code and data into sections (like `.text`, `.data`, `.bss`) according to a defined layout, which is especially important in low-level programming.

---

#### 2. Role of a Linker in OS Building

When building an operating system, especially one that must run directly on hardware (or in an emulator), you need precise control over where code and data reside in memory. Here’s why the linker is essential in this process:

- **Custom Memory Layout:**  
  OS kernels must often reside at specific addresses in memory (for example, the first megabyte might be reserved for boot code or hardware services). The linker script allows you to specify these addresses explicitly.

- **Combining Code from Multiple Sources:**  
  OS projects typically consist of code written in different languages (like C and assembly) and spread across multiple files. The linker ensures that these disparate pieces are correctly assembled into a coherent whole.

- **Ensuring Alignment:**  
  Many hardware architectures require that code and data be aligned on certain boundaries (e.g., 4 KB boundaries for page alignment). Proper alignment can enhance performance and meet the expectations of the hardware.

- **Creating a Bootable Image:**  
  The OS must be structured in such a way that the bootloader (or firmware) knows exactly where to jump to start executing the kernel. The linker script’s `ENTRY` directive and section placement are key to creating a bootable image.

---

#### 3. Detailed Explanation of the Provided Linker Script

```ld
ENTRY(loader)                /* the name of the entry label */

SECTIONS {
    . = 0x00100000;          /* the code should be loaded at 1 MB */

    .text ALIGN (0x1000) :   /* align at 4 KB */
    {
        *(.text)             /* all text sections from all files */
    }

    .rodata ALIGN (0x1000) : /* align at 4 KB */
    {
        *(.rodata*)          /* all read-only data sections from all files */
    }

    .data ALIGN (0x1000) :   /* align at 4 KB */
    {
        *(.data)             /* all data sections from all files */
    }

    .bss ALIGN (0x1000) :    /* align at 4 KB */
    {
        *(COMMON)            /* all COMMON sections from all files */
        *(.bss)              /* all bss sections from all files */
    }
}
```

#### ENTRY(loader)

- **What It Does:**  
  The `ENTRY(loader)` directive tells the linker that the starting point of the executable is the symbol `loader`.  
- **Context in OS Development:**  
  This is critical because the bootloader (or early kernel initialization code) must begin execution at a known point. By specifying `loader`, you ensure that when the OS is loaded into memory, the processor jumps to the correct starting location.

#### Setting the Load Address: `. = 0x00100000;`

- **What It Does:**  
  The line `. = 0x00100000;` sets the location counter to `0x00100000` (which is 1 MB). This tells the linker that the following sections should be placed starting at this address.
- **Context in OS Development:**  
  Many OS designs, especially those following legacy PC boot conventions, load the kernel at the 1 MB mark to avoid conflicts with the bootloader and to conform with hardware expectations.

#### Section Definitions

Each section definition in the script specifies a part of the binary and how it should be placed and aligned in memory.

#### .text Section

```ld
.text ALIGN (0x1000) :
{
    *(.text)
}
```

- **Purpose:**  
  The `.text` section contains the executable code.
- **Alignment:**  
  `ALIGN (0x1000)` ensures the section starts at a 4 KB boundary (0x1000 = 4096 bytes). This is often required for proper memory paging.
- **Inclusion of Files:**  
  The pattern `*(.text)` means “include all `.text` sections from every object file.” This aggregates all the executable code into one contiguous block.

#### .rodata Section

```ld
.rodata ALIGN (0x1000) :
{
    *(.rodata*)
}
```

- **Purpose:**  
  The `.rodata` section contains read-only data such as constant strings or immutable data.
- **Pattern:**  
  The wildcard `*(.rodata*)` includes any section that begins with `.rodata`. This is useful if there are variations (e.g., `.rodata.str1.1`).

#### .data Section

```ld
.data ALIGN (0x1000) :
{
    *(.data)
}
```

- **Purpose:**  
  The `.data` section holds initialized global and static variables.
- **Importance:**  
  Data that can change during execution but has a known starting value is placed here.

#### .bss Section

```ld
.bss ALIGN (0x1000) :
{
    *(COMMON)
    *(.bss)
}
```

- **Purpose:**  
  The `.bss` section is used for uninitialized global and static variables. The space is reserved in memory, but no actual data is stored in the binary (it is typically zero-initialized at runtime).
- **Inclusion of COMMON:**  
  `*(COMMON)` collects any common symbols that weren’t explicitly placed in another section.
- **Alignment:**  
  As with other sections, alignment is specified to ensure proper placement in memory.

---

#### 4. Summary

- **Linker Definition:**  
  A tool that combines object files into an executable, resolving symbols and assigning memory addresses.

- **Role in OS Development:**  
  - Controls the memory layout of the OS.
  - Resolves addresses and symbols from multiple source files.
  - Ensures the executable is bootable by defining the entry point and proper memory placement.
  - Aligns code and data sections according to hardware requirements.

- **Specific Script Breakdown:**  
  - **`ENTRY(loader)`:** Sets the starting point of the program.
  - **`. = 0x00100000;`:** Loads the code at the 1 MB mark.
  - **Section definitions (`.text`, `.rodata`, `.data`, `.bss`):** Organize and align the code and data into the binary according to their usage and memory requirements.
