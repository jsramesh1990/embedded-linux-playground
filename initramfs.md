# initramfs in Embedded Linux

## Overview

`initramfs` stands for:

```text
Initial RAM Filesystem
```

It is a temporary root filesystem loaded into RAM during the Linux boot process before the real root filesystem is mounted.

It helps the kernel:
- initialize hardware
- load drivers
- locate the real root filesystem
- perform recovery or rescue tasks

---

# 1. Linux Boot Flow with initramfs

```text
Power ON
    ↓
ROM Code
    ↓
SPL
    ↓
U-Boot
    ↓
Load Kernel
    ↓
Load initramfs
    ↓
Kernel Boot
    ↓
Temporary RootFS (RAM)
    ↓
Mount Real RootFS
    ↓
Start init System
``` id="boot1"

---

# 2. What is initramfs?

initramfs is:
- a compressed archive
- loaded into RAM
- unpacked by kernel
- used as temporary root filesystem

Usually:
```text
cpio.gz archive
```

---

# 3. Why initramfs is Needed

The kernel may not initially know:
- where the real root filesystem exists
- how to access storage hardware

initramfs provides:
- early userspace environment
- drivers/modules
- boot scripts

---

# 4. initramfs Responsibilities

| Responsibility | Description |
|----------------|-------------|
| Load drivers | Storage/network drivers |
| Mount rootfs | Real filesystem |
| Recovery shell | Emergency mode |
| Firmware loading | Hardware firmware |
| Early boot tasks | Initialization |

---

# 5. initramfs Architecture

```text
Compressed initramfs
        ↓
Loaded by Bootloader
        ↓
Kernel extracts into RAM
        ↓
Temporary RootFS
        ↓
Execute /init
``` id="flow1"

---

# 6. initramfs vs initrd

| Feature | initrd | initramfs |
|---------|---------|-----------|
| Older method | Yes | No |
| Uses block device | Yes | No |
| Uses cpio archive | No | Yes |
| Modern Linux | Rare | Standard |

---

# 7. initramfs File Format

Usually:
```text
cpio archive + gzip compression
```

Example:
```text
initramfs.cpio.gz
```

---

# 8. Kernel and initramfs Relationship

Kernel:
- extracts initramfs into RAM
- mounts it as temporary root filesystem
- runs `/init`

---

# 9. initramfs Boot Flow

```text
Kernel Start
     ↓
Extract initramfs
     ↓
Mount tmpfs/ramfs
     ↓
Run /init
     ↓
Mount Real RootFS
     ↓
switch_root
``` id="boot2"

---

# 10. initramfs Memory Layout

```text
+----------------------+
| Kernel Image         |
+----------------------+
| Device Tree Blob     |
+----------------------+
| initramfs            |
+----------------------+
| Free RAM             |
+----------------------+
``` id="mem1"

---

# 11. Loading initramfs in U-Boot

Example:

```bash
fatload mmc 0:1 0x83000000 initramfs.cpio.gz
``` id="load1"

---

# 12. Booting Kernel with initramfs

Example:

```bash
bootz 0x80000000 0x83000000 0x82000000
``` id="bootz1"

---

## Parameters

| Parameter | Meaning |
|-----------|---------|
| kernel addr | Kernel image |
| ramdisk addr | initramfs |
| dtb addr | Device Tree |

---

# 13. initramfs Contents

Typical contents:

```text
/init
/bin
/sbin
/dev
/proc
/sys
/lib
```

---

# 14. Essential initramfs Components

| Component | Purpose |
|----------|---------|
| /init | First userspace program |
| BusyBox | Minimal utilities |
| Kernel modules | Driver loading |
| Mount tools | Mount rootfs |

---

# 15. /init Script

Kernel executes:
```text
/init
```

after mounting initramfs.

---

# 16. Example /init Script

```sh
#!/bin/sh

mount -t proc none /proc
mount -t sysfs none /sys

echo "Booting Linux..."

mount /dev/mmcblk0p2 /newroot

exec switch_root /newroot /sbin/init
``` id="init1"

---

# 17. BusyBox in initramfs

BusyBox provides:
- shell
- mount
- ls
- cp
- networking tools

in a single binary.

---

# 18. initramfs Filesystem Types

Kernel uses:
- ramfs
- tmpfs

for initramfs storage.

---

# 19. Root Filesystem Switching

After real rootfs mounted:

```text
switch_root
```

or

```text
pivot_root
```

used.

---

# 20. switch_root Flow

```text
Temporary RootFS
      ↓
Mount Real RootFS
      ↓
switch_root
      ↓
Run Real /sbin/init
``` id="switch1"

---

# 21. Building initramfs Manually

Create directory:

```bash
mkdir initramfs
``` id="mk1"

---

# 22. Add BusyBox

```bash
cp busybox initramfs/bin/
``` id="bb1"

---

# 23. Create init Script

```bash
chmod +x init
``` id="chmod1"

---

# 24. Create cpio Archive

```bash
find . | cpio -o -H newc | gzip > initramfs.cpio.gz
``` id="cpio1"

---

# 25. Embedding initramfs into Kernel

Kernel can include initramfs internally.

---

## Kernel Config

```text
CONFIG_INITRAMFS_SOURCE
```

---

# 26. Embedded initramfs Flow

```text
Kernel Image
      ↓
Embedded initramfs
      ↓
Single boot image
``` id="embed1"

---

# 27. External initramfs

More common approach:

```text
Kernel + external initramfs
```

loaded separately.

---

# 28. initramfs and Kernel Modules

Can load:
- storage drivers
- filesystem modules
- network modules

during early boot.

---

# 29. Module Loading Example

```bash
modprobe mmc_block
``` id="mod1"

---

# 30. initramfs and Recovery Mode

Used for:
- rescue shell
- firmware repair
- diagnostics

---

# 31. Emergency Shell Example

```bash
/bin/sh
``` id="shell1"

Provides debugging environment.

---

# 32. initramfs and Encrypted RootFS

Used to:
- unlock encrypted partitions
- request passwords
- decrypt storage

before mounting rootfs.

---

# 33. initramfs and Network Boot

Can:
- configure networking
- mount NFS rootfs

---

# 34. NFS Boot Flow

```text
Kernel
   ↓
initramfs
   ↓
Configure Ethernet
   ↓
Mount NFS RootFS
``` id="nfs1"

---

# 35. initramfs Compression Formats

Supported formats:

| Format | Extension |
|--------|-----------|
| gzip | .gz |
| xz | .xz |
| lz4 | .lz4 |
| bzip2 | .bz2 |

---

# 36. initramfs in Embedded Linux

Commonly used because:
- modular boot process
- easier recovery
- dynamic hardware support

---

# 37. initramfs and Memory Usage

Disadvantage:
- consumes RAM
- temporary duplication

---

# 38. initramfs Debugging

---

## Kernel Boot Logs

```bash
dmesg
``` id="dbg1"

---

## Break into Shell

Add to bootargs:

```text
rdinit=/bin/sh
``` id="dbg2"

---

# 39. Common initramfs Errors

---

## No init found

```text
Kernel panic - not syncing
```

---

## Cannot mount rootfs

```text
VFS: unable to mount root fs
```

---

## Missing BusyBox

```text
/bin/sh not found
```

---

# 40. initramfs vs RootFS

| Feature | initramfs | RootFS |
|---------|------------|--------|
| Temporary | Yes | No |
| RAM-based | Yes | Usually storage |
| Early boot | Yes | Main system |
| Small | Usually | Larger |

---

# 41. initramfs Source Tree Example

```text
initramfs/
 ├── init
 ├── bin/
 ├── sbin/
 ├── lib/
 ├── dev/
 ├── proc/
 └── sys/
```

---

# 42. Linux Kernel initramfs Support

Kernel options:

```text
CONFIG_BLK_DEV_INITRD
CONFIG_INITRAMFS_SOURCE
```

---

# 43. Complete initramfs Boot Sequence

```text
Bootloader
    ↓
Load Kernel
    ↓
Load initramfs
    ↓
Kernel Extracts Archive
    ↓
Execute /init
    ↓
Mount Real RootFS
    ↓
switch_root
    ↓
Run /sbin/init
``` id="final1"

---

# 44. Advantages of initramfs

- flexible early boot
- modular driver loading
- recovery support
- encrypted rootfs support
- network boot support

---

# 45. Disadvantages

- increased RAM usage
- additional boot complexity
- larger boot image

---

# 46. Embedded Linux Example

```text
U-Boot
   ↓
Load zImage
   ↓
Load board.dtb
   ↓
Load initramfs.cpio.gz
   ↓
bootz
   ↓
Kernel mounts temporary rootfs
``` id="emb1"

---

# 47. Summary

- initramfs is a temporary RAM-based root filesystem
- Used during early Linux boot
- Contains `/init`, BusyBox, modules, scripts
- Helps mount the real root filesystem
- Essential for modern Linux boot systems

---

````
