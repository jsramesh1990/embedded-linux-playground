# Difference Between Kernel Space and User Space 

## Overview

Linux operating systems divide memory and execution into two major regions:

1. User Space
2. Kernel Space

This separation provides:
- security
- stability
- process isolation
- hardware protection

---

# 1. High-Level Architecture

```text
+--------------------------------------+
|             User Space               |
|--------------------------------------|
| Applications                         |
| Shell                                |
| GUI                                  |
| Libraries                            |
+--------------------------------------+
|         System Call Interface        |
+--------------------------------------+
|             Kernel Space             |
|--------------------------------------|
| Process Management                   |
| Memory Management                    |
| Device Drivers                       |
| Networking Stack                     |
| Filesystems                          |
+--------------------------------------+
|               Hardware               |
+--------------------------------------+
``` id="arch1"

---

# 2. What is User Space?

User space is where:
- applications run
- user programs execute
- restricted operations occur

Examples:
- bash
- firefox
- embedded applications
- system utilities

---

# 3. Characteristics of User Space

| Feature | Description |
|---------|-------------|
| Restricted access | Cannot directly access hardware |
| Isolated | Processes separated |
| Protected memory | Cannot access kernel memory |
| Less privilege | User mode execution |

---

# 4. What is Kernel Space?

Kernel space is reserved for:
- Linux kernel
- device drivers
- low-level hardware control

Kernel has:
- full memory access
- privileged CPU instructions
- hardware control

---

# 5. Characteristics of Kernel Space

| Feature | Description |
|---------|-------------|
| Full hardware access | Yes |
| Privileged execution | Yes |
| Shared memory | Global kernel memory |
| High risk | Bugs can crash system |

---

# 6. CPU Modes

Modern CPUs provide modes:

| Mode | Purpose |
|------|---------|
| User Mode | Restricted execution |
| Kernel Mode | Privileged execution |

---

# 7. Execution Flow

```text
Application
     ↓
System Call
     ↓
Switch to Kernel Mode
     ↓
Kernel Executes
     ↓
Return to User Mode
``` id="flow1"

---

# 8. Why Separation is Needed

Without separation:
- applications could crash OS
- malicious code could access hardware
- memory corruption would be common

Separation improves:
- reliability
- security
- stability

---

# 9. Memory Layout

Typical Linux memory layout:

```text
+-----------------------------+
| User Space                  |
|-----------------------------|
| Stack                       |
| Heap                        |
| Libraries                   |
| Application Code            |
+-----------------------------+
| Kernel Space                |
|-----------------------------|
| Kernel Code                 |
| Drivers                     |
| Kernel Heap                 |
+-----------------------------+
``` id="mem1"

---

# 10. User Space Restrictions

User applications CANNOT:
- access physical memory directly
- execute privileged instructions
- access I/O ports directly
- disable interrupts

---

# 11. Kernel Space Capabilities

Kernel CAN:
- control hardware
- manage interrupts
- allocate physical memory
- schedule processes
- access devices directly

---

# 12. System Calls

User programs request kernel services using:
```text
System Calls
```

---

# 13. Common System Calls

| System Call | Purpose |
|-------------|---------|
| open() | Open file |
| read() | Read file |
| write() | Write file |
| fork() | Create process |
| exec() | Execute program |
| mmap() | Memory mapping |

---

# 14. System Call Flow

```text
User Application
        ↓
glibc Wrapper
        ↓
syscall instruction
        ↓
Kernel Handler
        ↓
Hardware / Kernel Service
``` id="sys1"

---

# 15. Context Switch

Switch between user and kernel mode.

---

## Flow

```text
User Mode
    ↓
Interrupt/System Call
    ↓
Save CPU State
    ↓
Kernel Mode
    ↓
Execute Kernel Code
    ↓
Restore State
    ↓
User Mode
``` id="ctx1"

---

# 16. Kernel Stack vs User Stack

Each process has:
- user stack
- kernel stack

---

## User Stack

Used by:
- application functions
- local variables

---

## Kernel Stack

Used during:
- syscalls
- interrupts
- exceptions

---

# 17. User Space Example

```c
#include <stdio.h>

int main() {
    printf("Hello User Space\n");
    return 0;
}
``` id="usr1"

---

# 18. Kernel Space Example

```c
static int __init my_init(void)
{
    printk("Hello Kernel Space\n");
    return 0;
}
``` id="ker1"

---

# 19. Device Access Example

## User Space

```text
Application
   ↓
read("/dev/ttyS0")
```

---

## Kernel Space

```text
UART Driver
   ↓
Hardware Registers
``` id="dev1"

---

# 20. Kernel Modules

Kernel functionality can be extended using:

```text
Loadable Kernel Modules (.ko)
```

These run in kernel space.

---

# 21. Communication Between Spaces

Methods:

| Method | Purpose |
|--------|---------|
| System Calls | Standard interface |
| ioctl() | Device control |
| procfs | Kernel info |
| sysfs | Device interface |
| netlink | Kernel-user communication |

---

# 22. procfs Example

```bash
cat /proc/cpuinfo
``` id="proc1"

---

# 23. sysfs Example

```bash
/sys/class/gpio/
``` id="sysfs1"

---

# 24. Memory Protection

MMU protects:
- user memory
- kernel memory

Applications cannot:
```text
directly access kernel addresses
```

---

# 25. Address Space Separation

Typical 32-bit Linux split:

```text
0x00000000 - 0xBFFFFFFF → User Space
0xC0000000 - 0xFFFFFFFF → Kernel Space
``` id="addr1"

---

# 26. User Space Crash

If application crashes:

```text
Segmentation Fault
```

Usually:
- application terminates
- OS survives

---

# 27. Kernel Space Crash

Kernel crash causes:

```text
Kernel Panic
```

May freeze or reboot system.

---

# 28. Segmentation Fault

Example:

```c
int *p = NULL;
*p = 10;
``` id="seg1"

---

## Result

```text
Segmentation Fault
```

Protected by OS memory isolation.

---

# 29. Kernel Panic Example

```text
NULL pointer dereference in kernel
```

Can crash entire system.

---

# 30. Interrupt Handling

Interrupts handled in kernel space.

---

## Flow

```text
Hardware IRQ
     ↓
CPU switches to kernel mode
     ↓
ISR executes
``` id="irq1"

---

# 31. Privileged Instructions

Only kernel mode can execute:

Examples:
- disable interrupts
- configure MMU
- access I/O ports

---

# 32. User-Kernel Transition Cost

Switching modes costs CPU cycles due to:
- context saving
- MMU changes
- privilege switching

---

# 33. Embedded Linux Perspective

In embedded systems:
- applications run in user space
- drivers run in kernel space
- hardware controlled via kernel

---

# 34. Embedded Driver Flow

```text
User App
   ↓
System Call
   ↓
Kernel Driver
   ↓
GPIO/UART Hardware
``` id="emb1"

---

# 35. Advantages of Separation

| Benefit | Description |
|---------|-------------|
| Security | Apps isolated |
| Stability | App crash doesn't crash OS |
| Hardware protection | Controlled access |
| Memory safety | Protected address spaces |

---

# 36. Disadvantages

| Issue | Description |
|------|-------------|
| Context switch overhead | Performance cost |
| Complex driver development | Kernel programming harder |

---

# 37. User Space Driver Approach

Some drivers use:
- UIO
- FUSE
- Userspace networking

Advantages:
- safer debugging
- less kernel crashes

---

# 38. Kernel APIs

Kernel-space code uses:

```c
kmalloc()
printk()
copy_to_user()
copy_from_user()
``` id="api1"

---

# 39. copy_to_user Example

```c
copy_to_user(user_buf, kernel_buf, size);
``` id="copy1"

---

Purpose:
- safe memory transfer

---

# 40. User APIs

User-space programs use:

```c
malloc()
printf()
fopen()
``` id="uapi1"

---

# 41. Complete Linux Execution Model

```text
User Application
      ↓
glibc
      ↓
System Call Interface
      ↓
Linux Kernel
      ↓
Device Drivers
      ↓
Hardware
``` id="final1"

---

# 42. Security Implications

Kernel vulnerabilities:
- root privilege escalation
- complete system compromise

User-space vulnerabilities:
- usually process limited

---

# 43. Debugging Tools

## User Space

```bash
gdb
strace
valgrind
```

---

## Kernel Space

```bash
dmesg
kgdb
ftrace
```

---

# 44. Embedded Linux Example

```text
Smart TV Application
        ↓
Framebuffer Driver
        ↓
Display Hardware
``` id="tv1"

---

# 45. Summary Table

| Feature | User Space | Kernel Space |
|---------|-------------|--------------|
| Privilege | Low | High |
| Hardware Access | No | Yes |
| Memory Access | Restricted | Full |
| Crash Impact | Process only | Entire system |
| Execution Mode | User mode | Kernel mode |

---

# 46. Summary

- Linux separates execution into user and kernel space
- User space runs applications safely
- Kernel space controls hardware and system resources
- Communication occurs through system calls
- Separation improves security and stability

---

````
