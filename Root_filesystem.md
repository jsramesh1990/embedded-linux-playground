# Root Filesystem in Embedded Linux

## Overview

The **Root Filesystem (RootFS)** is the main filesystem mounted by the Linux kernel during boot.

It contains:
- system binaries
- libraries
- configuration files
- device nodes
- applications
- startup scripts

Without a valid RootFS, Linux cannot boot into userspace.

---

# 1. Linux Boot Flow with RootFS

```text
Power ON
    ↓
Bootloader (U-Boot/GRUB)
    ↓
Linux Kernel
    ↓
Mount Root Filesystem
    ↓
Start Init System
    ↓
Userspace Applications
``` id="boot1"

---

# 2. What is RootFS?

The root filesystem is mounted at:

```text
/
```

All directories originate from this root.

Example:

```text
/bin
/etc
/dev
/proc
/sys
/usr
``` id="root1"

---

# 3. RootFS Responsibilities

RootFS provides:

- init system
- shell
- libraries
- startup scripts
- drivers interface
- user applications

---

# 4. Essential RootFS Directories

| Directory | Purpose |
|-----------|---------|
| /bin | Basic commands |
| /sbin | System binaries |
| /etc | Configuration |
| /dev | Device files |
| /proc | Process information |
| /sys | SysFS |
| /lib | Shared libraries |
| /usr | User applications |
| /tmp | Temporary files |
| /var | Logs/runtime data |

---

# 5. Minimal Embedded RootFS

```text
rootfs/
 ├── bin/
 ├── sbin/
 ├── etc/
 ├── proc/
 ├── sys/
 ├── dev/
 ├── lib/
 └── tmp/
``` id="min1"

---

# 6. Important RootFS Components

---

## /bin

Contains essential commands:

```text
sh
ls
cp
mv
cat
```

Usually provided by BusyBox.

---

## /sbin

Contains system binaries:

```text
init
mount
reboot
ifconfig
```

---

## /etc

Configuration files.

Examples:

```text
/etc/inittab
/etc/fstab
/etc/passwd
```

---

## /dev

Device files.

Examples:

```text
/dev/console
/dev/null
/dev/ttyS0
```

---

## /proc

Virtual filesystem for kernel information.

Example:

```bash
cat /proc/cpuinfo
```

---

## /sys

SysFS interface to kernel subsystems.

Example:

```bash
/sys/class/gpio/
```

---

## /lib

Shared libraries:

```text
libc.so
ld-linux.so
```

---

# 7. RootFS Types

| Type | Description |
|------|-------------|
| initramfs | RAM-based temporary FS |
| NFS root | Network filesystem |
| ext4 rootfs | Flash/SD card filesystem |
| squashfs | Compressed read-only |
| cramfs | Small compressed FS |
| jffs2 | Flash filesystem |

---

# 8. initramfs

Temporary root filesystem loaded into RAM.

---

## Boot Flow

```text
Kernel
   ↓
initramfs
   ↓
/init
   ↓
real rootfs
``` id="ram1"

---

# 9. RootFS Storage Locations

| Storage | Usage |
|---------|------|
| SD card | Raspberry Pi |
| NAND flash | Embedded boards |
| NOR flash | Boot storage |
| eMMC | Modern embedded |
| NFS | Development/debugging |

---

# 10. RootFS Mounting

Kernel mounts RootFS using:

```text
root=
```

kernel boot argument.

---

## Example

```bash
root=/dev/mmcblk0p2
``` id="mount1"

---

# 11. RootFS Boot Arguments

```bash
console=ttyS0 root=/dev/mmcblk0p2 rw
``` id="bootargs1"

---

# 12. Read-Only vs Read-Write RootFS

---

## Read-Only RootFS

Advantages:
- safer
- corruption resistant
- ideal for production

Common with:
```text
squashfs
```

---

## Read-Write RootFS

Allows:
- package installation
- logging
- updates

Common with:
```text
ext4
```

---

# 13. BusyBox-Based RootFS

BusyBox commonly forms the base.

---

## Example Layout

```text
/bin/busybox
/bin/sh
/sbin/init
/etc/inittab
``` id="bb1"

---

# 14. Creating a Minimal RootFS

---

## Step 1: Create directories

```bash
mkdir -p rootfs/{bin,sbin,etc,proc,sys,dev,lib,tmp}
``` id="mk1"

---

## Step 2: Install BusyBox

```bash
make install CONFIG_PREFIX=./rootfs
``` id="mk2"

---

## Step 3: Create device nodes

```bash
sudo mknod rootfs/dev/console c 5 1
``` id="mk3"

---

## Step 4: Add init script

```bash
#!/bin/sh

mount -t proc proc /proc
mount -t sysfs sysfs /sys

exec /bin/sh
``` id="mk4"

---

# 15. RootFS and Init System

Kernel starts:

```text
/sbin/init
```

inside RootFS.

If missing:

```text
Kernel panic - no init found
```

---

# 16. RootFS Compression Formats

| Format | Feature |
|--------|---------|
| squashfs | compressed RO |
| ext4 | general RW |
| jffs2 | flash aware |
| ubifs | NAND optimized |

---

# 17. RootFS Generation Tools

| Tool | Purpose |
|------|---------|
| Buildroot | Embedded rootfs |
| Yocto | Full Linux distro |
| debootstrap | Debian rootfs |
| BusyBox | Minimal userspace |

---

# 18. Buildroot RootFS Flow

```text
Buildroot
    ↓
BusyBox
    ↓
Libraries
    ↓
Applications
    ↓
RootFS image
``` id="br1"

---

# 19. Yocto RootFS Flow

```text
Recipes
   ↓
BitBake
   ↓
Packages
   ↓
Root Filesystem
``` id="yocto1"

---

# 20. RootFS Packaging Formats

| Format | Usage |
|--------|------|
| ext4 image | SD/eMMC |
| cpio | initramfs |
| tar.gz | archive |
| squashfs | compressed |

---

# 21. RootFS and Shared Libraries

Applications depend on:

```text
/lib/libc.so
```

Missing libraries cause:

```text
No such file or directory
```

---

# 22. Dynamic Linker

Important file:

```text
/lib/ld-linux.so
```

Responsible for:
- loading shared libraries
- runtime linking

---

# 23. RootFS Internal Boot Flow

```text
Kernel mounts rootfs
        ↓
execve("/sbin/init")
        ↓
Init starts services
        ↓
Applications run
``` id="flow2"

---

# 24. Device Nodes in RootFS

Examples:

```text
/dev/null
/dev/tty
/dev/console
```

Created using:

```bash
mknod
```

---

# 25. proc and sys Mounting

Usually mounted during boot:

```bash
mount -t proc proc /proc
mount -t sysfs sysfs /sys
``` id="proc1"

---

# 26. RootFS in Embedded Linux

Common embedded setup:

```text
U-Boot
  ↓
Kernel
  ↓
RootFS (ext4/squashfs)
  ↓
BusyBox init
  ↓
Application
``` id="emb1"

---

# 27. Common RootFS Problems

---

## Kernel Panic

```text
No init found
```

Cause:
- missing `/sbin/init`

---

## Cannot Mount RootFS

```text
VFS: unable to mount root fs
```

Cause:
- wrong root= argument
- missing filesystem driver

---

## Missing Libraries

```text
libc.so not found
```

---

# 28. Debugging RootFS

---

## Check mounts

```bash
mount
``` id="dbg1"

---

## Check rootfs type

```bash
cat /proc/mounts
``` id="dbg2"

---

## Verify init

```bash
ls -l /sbin/init
``` id="dbg3"

---

# 29. RootFS Memory View

```text
Flash / SD Card
      ↓
Kernel mounts RootFS
      ↓
Virtual File System (VFS)
      ↓
Applications access files
``` id="mem1"

---

# 30. Read-Only Production RootFS Design

Typical production design:

```text
squashfs (RO)
overlayfs (RW temp layer)
```

Advantages:
- safe updates
- corruption resistance

---

# 31. RootFS Security Considerations

- minimize binaries
- remove unnecessary shells
- read-only filesystem
- secure permissions

---

# 32. Embedded RootFS Example

```text
rootfs/
 ├── bin/busybox
 ├── bin/sh
 ├── sbin/init
 ├── etc/inittab
 ├── dev/console
 ├── proc/
 └── sys/
``` id="emb2"

---

# 33. RootFS vs Bootloader vs Kernel

| Component | Purpose |
|----------|---------|
| Bootloader | Loads kernel |
| Kernel | Core OS |
| RootFS | Userspace environment |

---

# 34. Complete Embedded Linux Boot Flow

```text
Power ON
   ↓
ROM Bootloader
   ↓
U-Boot
   ↓
Linux Kernel
   ↓
Mount RootFS
   ↓
/sbin/init
   ↓
BusyBox/Systemd
   ↓
Application
``` id="boot2"

---

````
