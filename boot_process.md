# Embedded Linux Boot Process

## Overview

The Embedded Linux boot process is a staged sequence that initializes hardware, loads the operating system, and finally starts user applications. Each stage hands control to the next stage after completing its responsibilities.

---

## Boot Flow Summary

```

Power ON
↓
BootROM (SoC Internal ROM)
↓
SPL / FSBL (First Stage Bootloader)
↓
U-Boot / Barebox (Second Stage Bootloader)
↓
Linux Kernel
↓
init System (systemd / sysvinit)
↓
User Space Applications
↓
Fully Running Embedded Linux System

```

---

# 1. BootROM (SoC Internal ROM)

## Start Point
- Hardware reset / Power ON

## End Point
- Loads SPL into SRAM and jumps to it

## Responsibilities
- Minimal hardware initialization
- Detect boot device (SD / eMMC / NAND / SPI)
- Initialize CPU to basic state
- Load first-stage bootloader into internal SRAM

## Characteristics
- Stored inside SoC (immutable)
- Vendor-specific
- Very small memory footprint

---

# 2. First Stage Bootloader (SPL / FSBL)

## Start Point
- Execution begins from BootROM

## End Point
- Loads second stage bootloader (U-Boot)

## Responsibilities
- Initialize DRAM (critical step)
- Setup clocks and PLLs
- Basic board initialization
- Load next bootloader into RAM

## Examples
- U-Boot SPL
- ARM Trusted Firmware BL2

---

# 3. Second Stage Bootloader (U-Boot / Barebox)

## Start Point
- Control from SPL

## End Point
- Transfers control to Linux Kernel

## Responsibilities
- Full hardware initialization
- Load kernel image
- Load Device Tree Blob (DTB)
- Load initramfs (optional)
- Pass kernel boot arguments

## Features
- Interactive CLI
- Boot scripts (`bootcmd`)
- Environment variables
- Fallback boot support

---

# 4. Linux Kernel

## Start Point
- Entry at `start_kernel()`

## End Point
- Starts init process (PID 1)

## Responsibilities
- Memory management initialization
- Process scheduler setup
- Device driver initialization
- Interrupt system setup
- Mount root filesystem

## Final Action
- Executes `/sbin/init`

---

# 5. Init System (systemd / sysvinit / busybox init)

## Start Point
- Kernel launches PID 1

## End Point
- System reaches operational state

## Responsibilities
- Mount `/proc`, `/sys`, `/dev`
- Start system services
- Initialize networking
- Start device management (udev/systemd-udevd)

## Result
- System services running

---

# 6. User Space Applications

## Start Point
- Services are up

## End Point
- Fully operational system

## Responsibilities
- Run embedded applications
- UI frameworks (Qt / Wayland / framebuffer)
- IoT services
- Control logic

---

# Memory Transition Flow

```

BootROM  → SRAM
SPL      → DRAM initialized
U-Boot   → Full RAM access
Kernel   → Virtual memory enabled
Userland → Complete OS environment

````

---

# Boot Artifacts

## Kernel Images
- zImage (compressed kernel)
- Image (raw kernel)
- vmlinuz (x86 kernel)

## Device Tree Blob (DTB)
- Hardware description passed to kernel

## Initramfs
- Temporary root filesystem for early boot

---

# Boot Flow Diagram (Mermaid)

```mermaid
flowchart TD
    A[Power ON] --> B[BootROM]
    B --> C[SPL / FSBL]
    C --> D[U-Boot / Barebox]
    D --> E[Linux Kernel]
    E --> F[Init System]
    F --> G[System Services]
    G --> H[User Applications]
    H --> I[Embedded Linux Running System]
````

---

# Kernel Boot Internal Flow

```
start_kernel()
  ↓
setup_arch()
  ↓
mm_init()
  ↓
sched_init()
  ↓
driver_init()
  ↓
vfs_init()
  ↓
mount_root()
  ↓
start init (PID 1)
```

---

# Secure Boot (Optional)

```
BootROM (Root of Trust)
   ↓
Signed SPL
   ↓
Signed U-Boot
   ↓
Signed Kernel
   ↓
Verified Root Filesystem
```

---

# Common Boot Failures

| Stage   | Problem   | Cause                       |
| ------- | --------- | --------------------------- |
| BootROM | No boot   | Wrong boot mode             |
| SPL     | Hang      | DRAM initialization failure |
| U-Boot  | No output | Bad image or config         |
| Kernel  | Panic     | Missing root filesystem     |
| Init    | Failure   | Missing init binary         |

---

# Final System State

After successful boot:

* Kernel running
* PID 1 active
* Drivers loaded
* Network initialized
* Applications running

---

