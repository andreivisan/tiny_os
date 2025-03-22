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