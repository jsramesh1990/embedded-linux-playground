# Linux Kernel Architecture

## Overview

The Linux Kernel is the **core component** of the operating system.

It acts as an interface between:
- hardware
- applications
- system resources

The kernel manages:
- CPU
- memory
- devices
- filesystems
- networking
- processes

---

# 1. Linux System Architecture

```text
+----------------------------------+
|         User Applications        |
+----------------------------------+
|     System Libraries (glibc)     |
+----------------------------------+
|        System Call Interface     |
+----------------------------------+
|           Linux Kernel           |
|----------------------------------|
| Process Mgmt | Memory Mgmt       |
| Device Drv   | VFS               |
| Networking   | Scheduler         |
+----------------------------------+
|             Hardware             |
+----------------------------------+
``` id="arch1"

---

# 2. Kernel Space vs User Space

## User Space

Applications run here.

Examples:
- bash
- firefox
- embedded apps

Restrictions:
- cannot directly access hardware
- cannot execute privileged instructions

---

## Kernel Space

Kernel runs here.

Capabilities:
- full hardware access
- memory management
- interrupt handling

---

# 3. Linux Kernel Types

| Type | Description |
|------|-------------|
| Monolithic | Entire kernel in one image |
| Microkernel | Minimal kernel services |
| Hybrid | Combination |

Linux uses:
```text
Monolithic Modular Kernel
```

---

# 4. Monolithic Kernel Structure

```text
Linux Kernel
 ├── Scheduler
 ├── Memory Manager
 ├── VFS
 ├── Networking
 ├── Device Drivers
 ├── IPC
 └── Security
``` id="mono1"

---

# 5. Major Kernel Subsystems

| Subsystem | Purpose |
|-----------|---------|
| Process Management | Handles tasks/processes |
| Memory Management | RAM and virtual memory |
| VFS | Filesystem abstraction |
| Device Drivers | Hardware interaction |
| Networking Stack | TCP/IP implementation |
| IPC | Inter-process communication |
| Scheduler | CPU allocation |

---

# 6. Process Management

Responsible for:
- process creation
- scheduling
- termination
- context switching

---

## Important Structures

```c
struct task_struct
```

Represents a process/task.

---

# 7. Process Lifecycle

```text
fork()
   ↓
Ready Queue
   ↓
Running
   ↓
Waiting
   ↓
Exit
``` id="proc1"

---

# 8. Scheduler

Controls CPU time allocation.

Linux uses:
```text
CFS (Completely Fair Scheduler)
```

---

## Scheduler Responsibilities

- task switching
- priority handling
- load balancing
- SMP scheduling

---

# 9. Context Switching

```text
Process A
    ↓
Save CPU registers
    ↓
Load Process B registers
    ↓
Process B runs
``` id="ctx1"

---

# 10. Memory Management

Handles:
- RAM allocation
- virtual memory
- paging
- caching

---

# 11. Linux Memory Layout

```text
+------------------+
| User Space       |
+------------------+
| Kernel Space     |
+------------------+
``` id="mem1"

---

# 12. Kernel Memory Components

| Component | Purpose |
|----------|---------|
| Stack | Function calls |
| Heap | Dynamic allocation |
| Page Cache | File caching |
| Slab Allocator | Object allocation |

---

# 13. Virtual Memory

Each process gets virtual address space.

Benefits:
- isolation
- protection
- efficient memory usage

---

# 14. Paging

Memory divided into:
```text
Pages
```

Typical page size:
```text
4 KB
```

---

# 15. Slab Allocator

Optimized memory allocator.

Used for:
- kernel objects
- inode cache
- task structures

---

# 16. Device Drivers

Drivers provide hardware abstraction.

Examples:
- UART driver
- GPIO driver
- Ethernet driver

---

# 17. Driver Architecture

```text
Application
     ↓
System Call
     ↓
VFS
     ↓
Device Driver
     ↓
Hardware
``` id="drv1"

---

# 18. Character Drivers

Stream-based devices.

Examples:
- UART
- keyboard
- serial ports

---

# 19. Block Drivers

Block-based storage devices.

Examples:
- SD cards
- HDD
- eMMC

---

# 20. Network Drivers

Responsible for:
- packet TX/RX
- DMA
- interrupt handling

---

# 21. Virtual File System (VFS)

Provides unified filesystem interface.

Supports:
- ext4
- FAT
- NFS
- squashfs

---

# 22. VFS Architecture

```text
Application
    ↓
System Call
    ↓
VFS
    ↓
Filesystem Driver
``` id="vfs1"

---

# 23. Important Filesystem Structures

| Structure | Purpose |
|----------|---------|
| inode | File metadata |
| dentry | Directory cache |
| superblock | Filesystem info |

---

# 24. Networking Stack

Implements:
- TCP/IP
- UDP
- Ethernet
- sockets

---

# 25. Networking Flow

```text
Application
    ↓
Socket API
    ↓
TCP/IP Stack
    ↓
NIC Driver
    ↓
Hardware
``` id="net1"

---

# 26. Interrupt Handling

Interrupts notify CPU about hardware events.

Examples:
- keyboard press
- UART RX
- timer interrupt

---

# 27. Interrupt Flow

```text
Hardware IRQ
      ↓
Interrupt Controller
      ↓
ISR (Interrupt Handler)
      ↓
Bottom Half / Tasklet
``` id="irq1"

---

# 28. System Calls

User applications interact with kernel using syscalls.

Examples:
```text
open()
read()
write()
fork()
exec()
```

---

# 29. System Call Flow

```text
User App
    ↓
glibc wrapper
    ↓
syscall instruction
    ↓
Kernel handler
``` id="sys1"

---

# 30. Kernel Modules

Loadable kernel extensions.

Example:
```bash
insmod driver.ko
``` id="mod1"

---

## Advantages

- dynamic loading
- modular design
- reduced kernel size

---

# 31. Module Flow

```text
Kernel
   ↓
Load .ko module
   ↓
Initialize driver
   ↓
Register device
``` id="mod2"

---

# 32. IPC (Inter Process Communication)

Methods:
- pipes
- shared memory
- message queues
- sockets
- semaphores

---

# 33. Security Subsystem

Provides:
- permissions
- capabilities
- SELinux/AppArmor
- namespaces

---

# 34. Linux Kernel Layers

```text
User Space
    ↓
System Call Interface
    ↓
Kernel Subsystems
    ↓
Hardware Abstraction
    ↓
Hardware
``` id="layer1"

---

# 35. Embedded Linux Kernel Architecture

```text
Bootloader
    ↓
Kernel
    ↓
Drivers
    ↓
RootFS
    ↓
BusyBox/Systemd
    ↓
Embedded Application
``` id="emb1"

---

# 36. Kernel Build Artifacts

| File | Purpose |
|------|---------|
| zImage | compressed kernel |
| uImage | U-Boot kernel image |
| vmlinux | ELF kernel |
| dtb | device tree blob |
| .ko | kernel module |

---

# 37. Device Tree Integration

Device tree describes hardware.

```text
.dts → .dtb
```

Kernel parses DTB during boot.

---

# 38. SMP (Multi-Core Support)

Kernel supports:
- multiple CPUs
- load balancing
- synchronization

---

# 39. Synchronization Mechanisms

| Mechanism | Purpose |
|-----------|---------|
| spinlock | short critical section |
| mutex | sleeping lock |
| semaphore | resource counting |
| atomic ops | lock-free updates |

---

# 40. Kernel Threads

Kernel internal threads:

Examples:
```text
kworker
ksoftirqd
migration
```

---

# 41. Kernel Logging

Uses:
```c
printk()
```

Logs visible via:
```bash
dmesg
```

---

# 42. Kernel Boot Flow

```text
Power ON
    ↓
Bootloader
    ↓
Kernel Decompression
    ↓
Memory Initialization
    ↓
Driver Initialization
    ↓
Mount RootFS
    ↓
Start init process
``` id="boot1"

---

# 43. Kernel Source Tree

```text
linux/
 ├── arch/
 ├── drivers/
 ├── fs/
 ├── mm/
 ├── net/
 ├── kernel/
 ├── include/
 └── init/
```

---

# 44. Important Kernel APIs

| API | Purpose |
|-----|---------|
| kmalloc | kernel memory allocation |
| printk | logging |
| copy_to_user | user copy |
| request_irq | interrupt registration |

---

# 45. Embedded Linux Kernel Features

- lightweight configuration
- device drivers
- real-time support
- low memory footprint
- power management

---

# 46. Common Kernel Panics

---

## No init found

```text
Kernel panic - not syncing
```

---

## NULL pointer dereference

```text
Oops: NULL pointer
```

---

# 47. Debugging Kernel

---

## dmesg

```bash
dmesg
``` id="dbg1"

---

## GDB

```bash
gdb vmlinux
``` id="dbg2"

---

## Kernel Logs

```bash
cat /proc/kmsg
``` id="dbg3"

---

# 48. Linux Kernel Design Goals

- portability
- modularity
- performance
- scalability
- security

---

# 49. Complete Embedded Linux Architecture

```text
+----------------------------------+
| Embedded Application             |
+----------------------------------+
| BusyBox / systemd                |
+----------------------------------+
| Root Filesystem                  |
+----------------------------------+
| Linux Kernel                     |
|  ├── Scheduler                   |
|  ├── Drivers                     |
|  ├── Networking                  |
|  ├── VFS                         |
|  └── Memory Manager              |
+----------------------------------+
| Hardware                         |
+----------------------------------+
``` id="final1"

---

````
