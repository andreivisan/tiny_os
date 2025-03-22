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