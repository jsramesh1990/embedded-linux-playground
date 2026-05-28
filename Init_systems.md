# Init Systems in Linux & Embedded Linux

## Overview

An **Init System** is the first userspace process started by the Linux kernel.

It is responsible for:
- system initialization
- starting services
- mounting filesystems
- managing processes
- handling shutdown/reboot

The init process always runs as:

```text
PID = 1
```

---

# 1. Linux Boot Flow Overview

```text
Power ON
    ↓
Bootloader (U-Boot/GRUB)
    ↓
Linux Kernel
    ↓
Mount Root Filesystem
    ↓
Start Init System (PID 1)
    ↓
System Services
    ↓
Login Shell / Application
``` id="boot1"

---

# 2. What is PID 1?

The first process started by the kernel:

```text
/sbin/init
```

Responsibilities:
- orphan process handling
- zombie cleanup
- starting all userspace services

---

# 3. Main Init Systems

| Init System | Usage |
|-------------|------|
| SysVinit | Traditional Linux |
| BusyBox init | Embedded Linux |
| systemd | Modern Linux |
| OpenRC | Lightweight systems |
| Upstart | Older Ubuntu systems |
| runit | Minimal systems |

---

# 4. Traditional SysVinit

## Overview

Classic UNIX-style init system.

Uses:
- runlevels
- shell scripts
- sequential startup

---

# 5. SysVinit Boot Flow

```text
Kernel
   ↓
/sbin/init
   ↓
/etc/inittab
   ↓
Runlevel scripts
   ↓
Services start
``` id="sysv1"

---

# 6. SysV Runlevels

| Runlevel | Meaning |
|----------|--------|
| 0 | Halt |
| 1 | Single-user mode |
| 2 | Multi-user |
| 3 | Multi-user with networking |
| 5 | GUI mode |
| 6 | Reboot |

---

# 7. SysVinit Directory Structure

```text
/etc/init.d/
/etc/rc.d/
/etc/rc3.d/
```

---

# 8. SysVinit Startup Script Example

```bash
#!/bin/sh

case "$1" in
start)
    echo "Starting service"
    ;;
stop)
    echo "Stopping service"
    ;;
restart)
    echo "Restarting service"
    ;;
esac
``` id="sysv2"

---

# 9. BusyBox init

## Overview

Lightweight init system for embedded Linux.

Designed for:
- small memory usage
- fast boot
- minimal systems

---

# 10. BusyBox init Flow

```text
Kernel
   ↓
BusyBox init
   ↓
/etc/inittab
   ↓
rcS startup script
   ↓
Shell / Application
``` id="bb1"

---

# 11. BusyBox inittab Example

```text
::sysinit:/etc/init.d/rcS
ttyS0::askfirst:/bin/sh
::ctrlaltdel:/sbin/reboot
``` id="bb2"

---

# 12. BusyBox rcS Script

```bash
#!/bin/sh

mount -t proc proc /proc
mount -t sysfs sysfs /sys

ifconfig lo up
``` id="bb3"

---

# 13. BusyBox Advantages

- Extremely small
- Fast startup
- Simple configuration
- Ideal for embedded systems

---

# 14. systemd

## Overview

Modern Linux init and service manager.

Features:
- parallel startup
- dependency management
- service monitoring
- logging integration

---

# 15. systemd Boot Flow

```text
Kernel
   ↓
systemd (PID 1)
   ↓
Targets & Units
   ↓
Parallel service startup
``` id="sd1"

---

# 16. systemd Components

| Component | Purpose |
|----------|--------|
| systemd | Init manager |
| journald | Logging |
| udevd | Device manager |
| logind | User sessions |

---

# 17. systemd Units

Systemd uses unit files:

| Unit Type | Purpose |
|-----------|--------|
| .service | Service |
| .target | Group of services |
| .mount | Mount points |
| .socket | IPC sockets |

---

# 18. Example systemd Service

```ini
[Unit]
Description=My Service

[Service]
ExecStart=/usr/bin/myapp

[Install]
WantedBy=multi-user.target
``` id="sd2"

---

# 19. systemctl Commands

## Start service

```bash
systemctl start nginx
``` id="ctl1"

---

## Stop service

```bash
systemctl stop nginx
``` id="ctl2"

---

## Enable service

```bash
systemctl enable nginx
``` id="ctl3"

---

# 20. systemd Targets

Replaces runlevels.

| Target | Equivalent |
|--------|------------|
| poweroff.target | Runlevel 0 |
| multi-user.target | Runlevel 3 |
| graphical.target | Runlevel 5 |

---

# 21. OpenRC

Lightweight alternative to systemd.

Used in:
- Alpine Linux
- Gentoo

Advantages:
- simple
- dependency aware
- lightweight

---

# 22. runit

Minimal init system.

Features:
- tiny
- fast
- service supervision

Common in embedded systems.

---

# 23. Init Responsibilities

Init systems handle:

---

## Filesystem Mounting

```bash
mount -t proc proc /proc
mount -t sysfs sysfs /sys
```

---

## Device Initialization

```text
/dev creation
udev handling
```

---

## Networking Startup

```text
ifconfig / DHCP
```

---

## Service Management

```text
ssh
network
logging
applications
```

---

# 24. Embedded Linux Init Flow

```text
Kernel Boot
     ↓
RootFS Mount
     ↓
BusyBox init
     ↓
rcS Script
     ↓
Drivers & Network
     ↓
Application Start
``` id="emb1"

---

# 25. Init and Root Filesystem

Important directories:

```text
/sbin/init
/etc/inittab
/etc/init.d/
/etc/systemd/
```

---

# 26. initramfs and Init

Kernel may boot into:

```text
/init
```

inside initramfs.

---

## Flow

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

# 27. Process Supervision

Modern init systems:
- restart crashed services
- monitor daemons
- log failures

---

# 28. Service Startup Types

| Type | Description |
|------|-------------|
| Sequential | One after another |
| Parallel | Simultaneous startup |
| Dependency-based | Ordered by dependencies |

---

# 29. Common Embedded Init Choice

| System Size | Init System |
|------------|-------------|
| Tiny IoT | BusyBox init |
| Mid-size embedded | OpenRC |
| Full Linux distro | systemd |

---

# 30. Common Problems

---

## Kernel Panic

```text
No init found
```

Cause:
- missing `/sbin/init`

---

## Service Failure

```text
Dependency not met
```

---

## Slow Boot

Cause:
- sequential startup
- blocking scripts

---

# 31. Debugging Init Issues

---

## Check PID 1

```bash
ps -p 1
``` id="dbg1"

---

## View logs

```bash
dmesg
journalctl
``` id="dbg2"

---

## Verify init executable

```bash
ls -l /sbin/init
``` id="dbg3"

---

# 32. systemd vs BusyBox init

| Feature | BusyBox init | systemd |
|---------|--------------|----------|
| Size | Very small | Large |
| Boot speed | Fast | Moderate |
| Complexity | Simple | High |
| Embedded usage | Excellent | Heavy |
| Logging | Minimal | Advanced |

---

# 33. Embedded Boot Example

```text
Power ON
   ↓
U-Boot
   ↓
Kernel
   ↓
BusyBox init
   ↓
rcS
   ↓
Mount filesystems
   ↓
Start application
``` id="boot2"

---

# 34. Init Failure Scenarios

---

## Missing init

Kernel tries:
```text
/sbin/init
/etc/init
/bin/init
/bin/sh
```

---

## Wrong permissions

```bash
chmod +x /sbin/init
```

---

# 35. Internal Linux Init Execution

Kernel internally executes:

```c
kernel_execve("/sbin/init");
``` id="kern1"

---

````
