# DMA (Direct Memory Access) in Embedded Linux

## Overview

DMA (Direct Memory Access) is a hardware mechanism that allows:
- peripherals
- hardware devices

to transfer data directly to/from memory:
```text
WITHOUT continuous CPU involvement
```

DMA is extremely important in:
- embedded Linux
- high-speed peripherals
- multimedia systems
- networking
- storage systems

DMA improves:
- performance
- throughput
- CPU efficiency

---

# 1. What is DMA?

DMA =
```text
Direct Memory Access
```

It enables:
```text
hardware devices to access RAM directly
```

without CPU copying every byte.

---

# 2. Why DMA is Needed

Without DMA:
```text
CPU must move every byte manually
```

This wastes:
- CPU cycles
- power
- performance

---

# 3. DMA High-Level Flow

```text
Peripheral Device
        ↓
DMA Controller
        ↓
System RAM
```

CPU only:
- configures DMA
- starts transfer
- handles completion interrupt

---

# 4. Traditional CPU-based Transfer

```text
Peripheral
    ↓
CPU Reads Byte
    ↓
CPU Writes RAM
``` id="cpu1"

CPU heavily occupied.

---

# 5. DMA-based Transfer

```text
Peripheral
    ↓
DMA Controller
    ↓
RAM
``` id="dma1"

CPU free for other work.

---

# 6. DMA Architecture

```text
+----------------------+
| CPU                  |
+----------------------+
        ↓
+----------------------+
| DMA Controller       |
+----------------------+
   ↓              ↓
Peripheral      RAM
``` id="arch1"

---

# 7. DMA Controller

Special hardware managing:
- memory transfers
- addressing
- transfer size
- interrupts

---

# 8. DMA Controller Responsibilities

DMA controller handles:
- source address
- destination address
- transfer count
- transfer mode

---

# 9. DMA Transfer Types

| Type | Description |
|------|-------------|
| Peripheral → Memory | RX transfer |
| Memory → Peripheral | TX transfer |
| Memory → Memory | RAM copy |

---

# 10. Peripheral-to-Memory Example

UART RX:
```text
UART → RAM
```

---

# 11. Memory-to-Peripheral Example

Audio playback:
```text
RAM → DAC/I2S
```

---

# 12. Memory-to-Memory Example

Fast buffer copy:
```text
RAM → RAM
```

---

# 13. DMA Workflow

```text
CPU Configures DMA
        ↓
DMA Starts Transfer
        ↓
DMA Transfers Data
        ↓
Transfer Complete
        ↓
DMA Interrupt Generated
``` id="flow1"

---

# 14. DMA Channels

DMA controller usually has:
```text
multiple channels
```

Each channel may serve:
- UART
- SPI
- I2C
- ADC

---

# 15. DMA Request (DMAREQ)

Peripheral signals:
```text
DMA request
```

when ready for transfer.

---

# 16. DMA Acknowledge (DMAACK)

DMA controller acknowledges:
```text
transfer request
```

---

# 17. DMA Transfer Modes

| Mode | Description |
|------|-------------|
| Single Transfer | One byte/word |
| Burst Transfer | Multiple bytes |
| Cycle Stealing | DMA steals bus cycles |
| Block Transfer | Entire block |

---

# 18. Burst Transfer

Transfers:
```text
multiple data words at once
```

Improves:
- throughput
- bus efficiency

---

# 19. Scatter-Gather DMA

DMA processes:
```text
multiple memory buffers automatically
```

using descriptor chains.

---

# 20. Scatter-Gather Flow

```text
Descriptor 1
     ↓
Descriptor 2
     ↓
Descriptor 3
``` id="sg1"

---

# 21. DMA and Interrupts

DMA usually generates:
```text
interrupt on completion
```

---

# 22. DMA Interrupt Flow

```text
DMA Transfer Complete
        ↓
DMA IRQ Generated
        ↓
ISR Executes
        ↓
Driver Processes Data
``` id="irq1"

---

# 23. DMA in Embedded Linux

Linux uses:
```text
DMA Engine Framework
```

for DMA management.

---

# 24. Linux DMA Architecture

```text
Application
     ↓
Device Driver
     ↓
DMA Engine API
     ↓
DMA Controller Driver
     ↓
Hardware DMA
``` id="linux1"

---

# 25. DMA Engine Framework

Linux abstraction layer for:
- DMA controllers
- device drivers

---

# 26. DMA-capable Devices

Common devices using DMA:
- UART
- SPI
- I2S
- Ethernet
- USB
- Camera

---

# 27. UART DMA Example

```text
UART RX
   ↓
DMA
   ↓
RAM Buffer
``` id="uart1"

---

# 28. Audio DMA Example

```text
Audio Buffer
      ↓
DMA
      ↓
I2S Controller
      ↓
Codec/Speaker
``` id="audio1"

---

# 29. Ethernet DMA Example

```text
Ethernet Packet
       ↓
DMA
       ↓
RAM Buffer
``` id="eth1"

---

# 30. DMA Buffer

Memory region used for:
```text
DMA transfers
```

---

# 31. DMA-safe Memory

DMA requires:
```text
physically accessible memory
```

---

# 32. Cache Coherency Problem

CPU cache may differ from:
```text
RAM content
```

during DMA.

---

# 33. Cache Coherency Flow

```text
CPU Cache Updated
        ↓
RAM Not Updated Yet
        ↓
DMA Reads Old Data
``` id="cache1"

---

# 34. DMA Cache Management

Linux uses:
- cache flush
- cache invalidate

operations.

---

# 35. DMA Mapping API

Linux APIs:
```c
dma_map_single()
dma_unmap_single()
``` id="map1"

---

# 36. DMA Allocation API

```c
dma_alloc_coherent()
``` id="alloc1"

Allocates:
```text
DMA-safe coherent memory
```

---

# 37. DMA Coherent Memory

CPU and DMA see:
```text
same memory contents
```

without manual cache operations.

---

# 38. DMA Descriptor

Structure describing:
- source
- destination
- size
- flags

---

# 39. DMA Descriptor Flow

```text
Driver Creates Descriptor
         ↓
DMA Controller Reads Descriptor
         ↓
Transfer Executes
``` id="desc1"

---

# 40. DMA and Bus Arbitration

DMA shares system bus with:
- CPU
- GPU
- peripherals

Bus arbiter manages access.

---

# 41. DMA and Performance

DMA improves:
- throughput
- latency
- CPU availability

---

# 42. DMA Advantages

| Advantage | Description |
|-----------|-------------|
| Lower CPU usage | CPU free |
| Faster transfer | Hardware optimized |
| Better multitasking | CPU available |

---

# 43. DMA Disadvantages

| Issue | Description |
|------|-------------|
| Complexity | Harder debugging |
| Cache coherency | Must manage |
| Hardware dependency | Platform-specific |

---

# 44. DMA in Real-Time Systems

DMA essential for:
- deterministic transfer
- multimedia streaming
- low-latency communication

---

# 45. DMA Device Tree Example

```dts
dma-controller@40400000 {
    compatible = "vendor,dma";
};
``` id="dt1"

---

# 46. Linux DMA Driver Flow

```text
Driver Requests DMA Channel
          ↓
Allocates DMA Buffer
          ↓
Configures Transfer
          ↓
Starts DMA
          ↓
Waits for Completion IRQ
``` id="drv1"

---

# 47. DMA Transfer Completion

Completion methods:
- interrupt
- polling

Interrupt preferred.

---

# 48. DMA Polling

CPU continuously checks:
```text
DMA status register
```

Less efficient.

---

# 49. DMA Burst Size

Configurable transfer chunk size.

Affects:
- bus efficiency
- latency

---

# 50. Circular DMA

DMA continuously cycles through:
```text
ring buffer
```

Used in:
- audio
- ADC sampling

---

# 51. Circular DMA Flow

```text
Buffer End
    ↓
DMA Returns to Buffer Start
``` id="circ1"

---

# 52. DMA and MMU

DMA hardware uses:
```text
physical addresses
```

NOT virtual addresses.

---

# 53. IOMMU

IOMMU maps:
```text
device virtual addresses
```

to physical memory.

---

# 54. DMA Synchronization

Drivers synchronize:
- CPU access
- DMA access

to avoid corruption.

---

# 55. DMA Debugging

---

## Kernel Logs

```bash
dmesg
``` id="dbg1"

---

## DMA Debugging Support

```text
CONFIG_DMA_API_DEBUG
```

---

## Trace DMA Events

```bash
ftrace
``` id="dbg2"

---

# 56. Common DMA Problems

| Problem | Description |
|---------|-------------|
| Cache corruption | Coherency issue |
| Invalid buffer | Wrong memory |
| Missed interrupt | Transfer incomplete |
| Alignment issue | Hardware restriction |

---

# 57. DMA vs CPU Transfer

| Feature | CPU Transfer | DMA |
|---------|--------------|-----|
| CPU Usage | High | Low |
| Speed | Lower | Higher |
| Complexity | Simple | Higher |

---

# 58. Embedded Linux DMA Boot Flow

```text
Kernel Boots
      ↓
DMA Controller Driver Loaded
      ↓
Peripheral Drivers Request DMA
      ↓
DMA Transfers Begin
``` id="boot1"

---

# 59. Complete DMA Workflow

```text
Driver Configures DMA
         ↓
DMA Controller Starts Transfer
         ↓
Peripheral Exchanges Data with RAM
         ↓
DMA Completion Interrupt
         ↓
Driver Processes Data
``` id="final1"

---

# 60. Summary

- DMA enables direct device-memory transfers
- Reduces CPU overhead significantly
- Essential for high-speed peripherals
- Linux uses DMA Engine framework
- DMA requires careful cache management
- Critical in embedded Linux and real-time systems

---


````
