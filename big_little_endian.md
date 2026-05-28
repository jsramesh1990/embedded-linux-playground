# Big Endian vs Little Endian

## Overview

**Endianness** defines how multi-byte data is stored in memory. The two main formats are:

- Big Endian
- Little Endian

This concept is critical in embedded systems, networking, and cross-platform development.

---

# 1. Core Idea

For a 32-bit value:

```

0x12345678

```id="e1a9k2"

It consists of 4 bytes:
```

12 | 34 | 56 | 78

```id="e1a9k3"

The question is: **which byte goes to the lowest memory address?**

---

# 2. Big Endian

## Definition

The **Most Significant Byte (MSB)** is stored at the lowest memory address.

---

## Memory Layout

### Value:
```

0x12345678

```

### Stored as:

```

Address → Value
1000 → 12
1001 → 34
1002 → 56
1003 → 78

```id="be1m01"

---

## Visual Flow

```

MSB → → → → → LSB
12   34   56   78

```id="be1m02"

---

## Characteristics

- Human-readable memory order
- Used in networking
- Easier for debugging byte streams

---

## Used In

- Network protocols (TCP/IP)
- Some RISC processors
- Older architectures (SPARC, PowerPC)

---

# 3. Little Endian

## Definition

The **Least Significant Byte (LSB)** is stored at the lowest memory address.

---

## Memory Layout

### Value:
```

0x12345678

```

### Stored as:

```

Address → Value
1000 → 78
1001 → 56
1002 → 34
1003 → 12

```id="le1m01"

---

## Visual Flow

```

LSB → → → → → MSB
78   56   34   12

```id="le1m02"

---

## Characteristics

- Efficient CPU arithmetic
- Most common in modern processors
- Default in ARM (Linux systems)
- Used in x86 architecture

---

## Used In

- x86 / x86_64
- ARM (default mode)
- Most embedded Linux systems

---

# 4. Side-by-Side Comparison

| Feature | Big Endian | Little Endian |
|--------|------------|---------------|
| Byte order | MSB → LSB | LSB → MSB |
| Readability | Easy | Hard |
| Performance | Neutral | Faster arithmetic |
| Network use | Yes | No |
| Common CPUs | PowerPC, SPARC | x86, ARM |

---

# 5. Visual Memory Comparison

## Value: 0xA1B2C3D4

### Big Endian
```

1000 → A1
1001 → B2
1002 → C3
1003 → D4

```id="vbe1"

---

### Little Endian
```

1000 → D4
1001 → C3
1002 → B2
1003 → A1

```id="vle1"

---

# 6. Why Two Formats Exist?

## Big Endian Advantage
- Matches human reading order
- Used in network protocols

## Little Endian Advantage
- Faster arithmetic on CPUs
- Easier increment operations
- Hardware efficiency

---

# 7. Endianness in Embedded Systems

## Typical Embedded Linux

```

ARM Cortex-A → Little Endian (default)
x86 systems  → Little Endian

```id="emb1"

---

## Exceptions

Some ARM CPUs can switch:

- Big Endian mode (rare)
- Little Endian mode (default)

---

# 8. Network Byte Order

Networking ALWAYS uses:

```

BIG ENDIAN

````id="netbe1"

---

## Conversion Functions

```c
htonl()  // Host to Network Long
htons()  // Host to Network Short
ntohl()  // Network to Host Long
ntohs()  // Network to Host Short
````

---

# 9. C Example (Detect Endianness)

````c
#include <stdio.h>

int main() {
    unsigned int x = 0x1;
    char *c = (char*)&x;

    if (*c == 1)
        printf("Little Endian\n");
    else
        printf("Big Endian\n");

    return 0;
}
``` id="cde1"

---

# 10. Byte-Level Inspection

```c
#include <stdio.h>

int main() {
    unsigned int x = 0x12345678;
    unsigned char *p = (unsigned char*)&x;

    printf("%02x %02x %02x %02x\n",
           p[0], p[1], p[2], p[3]);

    return 0;
}
``` id="cde2"

---

# 11. Byte Swapping

## Manual Swap

```c
unsigned int swap32(unsigned int x) {
    return ((x >> 24) & 0xFF) |
           ((x << 8)  & 0xFF0000) |
           ((x >> 8)  & 0xFF00) |
           ((x << 24) & 0xFF000000);
}
``` id="sw1"

---

## GCC Built-ins

```c
__builtin_bswap32(x);
__builtin_bswap64(x);
````

---

# 12. Common Problems

* Wrong sensor data interpretation
* Network packet corruption
* Image decoding errors
* File format mismatch
* MCU ↔ PC communication issues

---

# 13. Debugging Tips

* Always print raw bytes
* Use `htons / ntohs`
* Check architecture endianness
* Use debugger memory view

Example:

````
gdb: x/4xb address
``` id="dbg1"

---

# 14. Summary

- Big Endian: MSB stored first
- Little Endian: LSB stored first
- Embedded Linux mostly uses Little Endian
- Networking uses Big Endian
- Mismatch leads to subtle bugs

---


