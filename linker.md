#  linker.md

---

#  Introduction

In embedded systems, after compilation and assembly, multiple object files are generated. These files cannot run directly.

A **linker** combines all object files and libraries into a single executable firmware file.

This step is critical for creating:

* `.elf` (Executable and Linkable Format)
* `.hex` (Flashable firmware)
* `.bin` (Binary image for MCU)

---

#  Definition

A linker is a system tool that **combines multiple object files and resolves external symbol references to produce a final executable program**.

---

#  Why Linker is Important in Embedded Systems

* combines multiple `.o` files
* resolves function and variable references
* links standard libraries (libc, math, etc.)
* assigns memory addresses
* generates final firmware image
* works with linker script (`.ld`)

---

#  Compilation vs Linking

```text id="flow1"
Compilation → Converts code into .o files
Linking     → Combines .o files into final firmware
```

---

#  Full Build Flow

```text id="flow2"
Source Code (.c/.cpp)
        ↓
Compiler
        ↓
Object Files (.o)
        ↓
Linker
        ↓
Executable (.elf)
        ↓
Binary (.hex/.bin)
        ↓
Flashed to MCU
```

---

#  Graphical Representation

```text id="diag1"
+----------------------+
| file1.o             |
| file2.o             |
| startup.o          |
+----------+----------+
           ↓
+----------------------+
|       LINKER         |
| resolve symbols      |
| assign memory addr   |
+----------+----------+
           ↓
+----------------------+
| final.elf / .hex     |
+----------------------+
```

---

#  What Linker Does Internally

---

## 1. Symbol Resolution

Finds function/variable definitions.

```text id="sym1"
main() in file1.o
printf() in libc
```

---

## 2. Address Binding

Assigns memory addresses:

```text id="addr1"
0x0000 → startup code
0x1000 → main()
0x2000 → variables
```

---

## 3. Section Merging

Combines:

* `.text` (code)
* `.data` (initialized variables)
* `.bss` (uninitialized variables)

---

#  Embedded Example (Multiple Files)

---

## file1.c

```c id="ex1"
extern void led_on();

int main() {
    led_on();
}
```

---

## file2.c

```c id="ex2"
void led_on() {
    // GPIO logic
}
```

---

## Linking Step

```text id="flow3"
file1.o + file2.o → linker → final firmware
```

---

#  Linker Script (.ld)

Used in embedded systems to define memory layout.

---

## Example

```ld id="ld1"
MEMORY
{
  FLASH (rx) : ORIGIN = 0x00000000, LENGTH = 512K
  RAM   (rwx): ORIGIN = 0x20000000, LENGTH = 128K
}

SECTIONS
{
  .text : { *(.text*) } > FLASH
  .data : { *(.data*) } > RAM
  .bss  : { *(.bss*) } > RAM
}
```

---

#  Memory Mapping Diagram

```text id="diag2"
FLASH MEMORY
+----------------------+
| .text (code)        |
| startup code        |
| main()              |
+----------------------+

RAM MEMORY
+----------------------+
| .data               |
| .bss                |
| stack               |
+----------------------+
```

---

#  Linking Process Flow

```text id="flow4"
Object Files
     ↓
Symbol Table Created
     ↓
Resolve External Calls
     ↓
Assign Memory Addresses
     ↓
Merge Sections
     ↓
Generate Final Binary
```

---

#  Real Embedded Example

## STM32 Firmware Build

```text id="flow5"
main.o
gpio.o
uart.o
startup.o
        ↓
        LINKER
        ↓
firmware.elf
        ↓
firmware.hex
        ↓
Flashed to STM32 MCU
```

---

#  Common Linker Errors

---

## 1. Undefined reference

```text id="err1"
function declared but not defined
```

---

## 2. Multiple definition

```text id="err2"
same function defined in multiple files
```

---

## 3. Missing library

```text id="err3"
-lm not linked
```

---

#  Static vs Dynamic Linking

| Feature      | Static       | Dynamic  |
| ------------ | ------------ | -------- |
| Linking time | Compile time | Run time |
| Size         | Larger       | Smaller  |
| Embedded use | Mostly used  | Rare     |
| Speed        | Fast         | Slower   |

---

#  Static Linking Flow

```text id="flow6"
All libraries → combined into final binary
```

---

#  Linker vs Compiler

| Feature | Compiler       | Linker            |
| ------- | -------------- | ----------------- |
| Input   | Source code    | Object files      |
| Output  | .o files       | .elf/.bin         |
| Role    | Translate code | Combine code      |
| Errors  | Syntax         | Undefined symbols |

---

#  Key Concepts

---

## 1. Symbol Table

Tracks all functions/variables.

---

## 2. Relocation

Adjusts memory addresses after linking.

---

## 3. Section Mapping

Organizes memory layout.

---

#  Advantages of Linker

* modular programming
* reuse of code
* memory optimization
* supports libraries
* generates final executable

---

#  Disadvantages

* complex error debugging
* dependency issues
* memory layout constraints

---


