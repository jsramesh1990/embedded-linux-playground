# Cross Compilation

## Overview

Cross compilation is the process of compiling source code on one machine (Host) to generate binaries for another machine (Target) with a different architecture.

It is one of the most important concepts in embedded Linux development.

---

# 1. Why Cross Compilation is Needed

Embedded boards usually:
- Have low CPU power
- Have limited RAM/storage
- Cannot compile large projects efficiently

So compilation is done on a powerful PC.

---

# 2. Host vs Target vs Build Machine

| Term | Meaning |
|------|--------|
| Host | Machine where compilation happens |
| Target | Machine where binary runs |
| Build | Machine building toolchain itself |

---

## Example

| System | Architecture |
|--------|--------------|
| Ubuntu PC | x86_64 |
| Raspberry Pi | ARM64 |

Here:
```

Host   = x86_64 PC
Target = ARM64 board

```id="cross1"

---

# 3. Cross Compilation Flow

```

Source Code
↓
Cross Compiler
↓
Target Binary (ARM)
↓
Copy to Embedded Board
↓
Execute on Target

```id="flow1"

---

# 4. Native Compilation vs Cross Compilation

## Native Compilation

```

x86 → x86 binary

```id="nat1"

---

## Cross Compilation

```

x86 → ARM binary

````id="cross2"

---

# 5. Cross Toolchain Components

A toolchain includes:

| Tool | Purpose |
|------|--------|
| gcc | Compiler |
| as | Assembler |
| ld | Linker |
| gdb | Debugger |
| libc | Standard C library |

---

# 6. Common Cross Compilers

| Architecture | Toolchain Prefix |
|-------------|----------------|
| ARM 32-bit | arm-linux-gnueabihf- |
| ARM64 | aarch64-linux-gnu- |
| RISC-V | riscv64-linux-gnu- |
| MIPS | mips-linux-gnu- |

---

# 7. Example Cross Compiler Commands

## ARM Compilation

```bash
arm-linux-gnueabihf-gcc main.c -o app
``` id="cmd1"

---

## ARM64 Compilation

```bash
aarch64-linux-gnu-gcc main.c -o app
``` id="cmd2"

---

# 8. Cross Compilation Stages

````

Source Code (.c)
↓
Preprocessor
↓
Compiler
↓
Assembler
↓
Object Files (.o)
↓
Linker
↓
Target ELF Binary

````id="flow2"

---

# 9. Architecture Difference

## x86 Binary

Cannot run on ARM.

Example error:
``` id="f0e8yx"
Exec format error
````

---

## Reason

Different:

* Instruction set
* ABI
* Register layout
* Endianness (sometimes)

---

# 10. Build Example

## Source File

````c
#include <stdio.h>

int main() {
    printf("Hello ARM\n");
    return 0;
}
``` id="code1"

---

## Compile

```bash
arm-linux-gnueabihf-gcc hello.c -o hello
``` id="build1"

---

## Verify Binary

```bash
file hello
``` id="file1"

### Output
``` id="mabdd4"
ELF 32-bit ARM executable
````

---

# 11. Sysroot Concept

## What is Sysroot?

Directory containing target libraries and headers.

Example:

````
/opt/arm-sysroot/
``` id="sys1"

---

## Used For

- Linking target libraries
- Accessing target headers

---

# 12. Cross Compilation with Makefile

```makefile
CC = arm-linux-gnueabihf-gcc

all:
	$(CC) main.c -o app
``` id="mk1"

---

# 13. Cross Compiling Linux Kernel

## Example

```bash
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-
``` id="kern1"

---

## Parameters

| Parameter | Meaning |
|----------|--------|
| ARCH | Target architecture |
| CROSS_COMPILE | Toolchain prefix |

---

# 14. Cross Compiling U-Boot

```bash
make CROSS_COMPILE=arm-linux-gnueabihf-
``` id="uboot1"

---

# 15. Common Embedded Build Systems

| System | Purpose |
|--------|--------|
| Buildroot | Embedded root filesystem |
| Yocto | Full Linux distribution |
| OpenWRT | Router firmware |
| CMake | Build automation |

---

# 16. Environment Variables

## PATH Setup

```bash
export PATH=$PATH:/opt/toolchain/bin
``` id="env1"

---

## Compiler Variables

```bash
export CC=arm-linux-gnueabihf-gcc
``` id="env2"

---

# 17. Debugging Cross-Compiled Binaries

## Remote GDB

### Target
```bash
gdbserver :1234 app
``` id="dbg1"

---

### Host
```bash
arm-linux-gnueabihf-gdb app
``` id="dbg2"

---

# 18. Common Problems

## 18.1 Wrong Architecture

Error:
``` id="vb0p1m"
cannot execute binary file
````

---

## 18.2 Missing Shared Libraries

Error:

```id="d4l46g"
No such file or directory
```

Cause:

* Missing target libc

---

## 18.3 ABI Mismatch

* Hard float vs soft float
* ARM version mismatch

---

# 19. Toolchain Directory Structure

````
toolchain/
 ├── bin/
 ├── lib/
 ├── include/
 ├── sysroot/
``` id="tool1"

---

# 20. Embedded Linux Cross Build Flow

````

Application Source
↓
Cross GCC
↓
ARM ELF Binary
↓
Root Filesystem
↓
SD Card / Flash
↓
Embedded Linux Boot

```id="flow3"

---


