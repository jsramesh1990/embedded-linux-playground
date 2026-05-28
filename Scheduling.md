# Linux Scheduling in Embedded Linux

## Overview

Scheduling is the mechanism by which the Linux kernel decides:

```text
Which task runs?
When does it run?
How long does it run?
```

The Linux scheduler is one of the most important kernel subsystems.

It manages:
- processes
- threads
- CPU time
- priorities
- multitasking

---

# 1. What is a Scheduler?

A scheduler is a kernel component responsible for:

```text
CPU resource management
```

It selects:
- next runnable task
- execution duration
- CPU assignment

---

# 2. Why Scheduling is Needed

Modern systems run multiple tasks simultaneously:
- applications
- drivers
- kernel threads
- interrupts

Scheduler ensures:
- fairness
- responsiveness
- real-time behavior

---

# 3. High-Level Scheduling Flow

```text
Multiple Tasks Ready
        ↓
Scheduler Chooses Task
        ↓
CPU Executes Task
        ↓
Time Slice Expires / Task Blocks
        ↓
Context Switch
        ↓
Next Task Runs
``` id="flow1"

---

# 4. Linux Process States

| State | Meaning |
|------|---------|
| Running | Executing on CPU |
| Runnable | Ready to run |
| Sleeping | Waiting for event |
| Stopped | Suspended |
| Zombie | Exited but not cleaned |

---

# 5. Process Lifecycle

```text
Create Process
      ↓
Runnable
      ↓
Running
      ↓
Sleeping / Waiting
      ↓
Runnable Again
      ↓
Exit
``` id="life1"

---

# 6. Scheduler Goals

Linux scheduler aims for:
- fairness
- low latency
- high throughput
- responsiveness
- scalability

---

# 7. Scheduler Components

| Component | Purpose |
|----------|---------|
| Run Queue | Ready tasks |
| Context Switcher | Switch tasks |
| Scheduler Classes | Scheduling policies |
| Timer Tick | Preemption timing |

---

# 8. Run Queue

Runnable tasks stored in:
```text
run queue
```

Each CPU usually has:
```text
per-CPU run queue
```

---

# 9. Scheduler Architecture

```text
+----------------------+
| Linux Scheduler      |
+----------------------+
| Run Queues           |
| Scheduler Classes    |
| Load Balancer        |
| Context Switcher     |
+----------------------+
| CPU Hardware         |
+----------------------+
``` id="arch1"

---

# 10. Context Switching

Switching CPU from one process to another.

Includes:
- saving registers
- loading new task context
- memory mapping switch

---

# 11. Context Switch Flow

```text
Running Task
      ↓
Save CPU Registers
      ↓
Load New Task Registers
      ↓
Resume New Task
``` id="ctx1"

---

# 12. Scheduling Policies

Linux supports multiple policies.

| Policy | Purpose |
|--------|---------|
| CFS | Normal scheduling |
| FIFO | Real-time |
| RR | Real-time round-robin |
| DEADLINE | Deadline scheduling |

---

# 13. Completely Fair Scheduler (CFS)

Default Linux scheduler.

Goal:
```text
fair CPU sharing
```

---

# 14. CFS Working Principle

CFS tracks:
```text
virtual runtime (vruntime)
```

Task with:
```text
smallest vruntime
```

gets CPU next.

---

# 15. CFS Flow

```text
Runnable Tasks
       ↓
Compare vruntime
       ↓
Select Lowest vruntime
       ↓
Execute Task
``` id="cfs1"

---

# 16. Red-Black Tree in CFS

CFS stores tasks in:
```text
Red-Black Tree
```

for efficient scheduling.

---

# 17. CFS Advantages

- fairness
- scalable
- low latency
- efficient for desktop/server

---

# 18. Time Slice

Scheduler gives CPU for:
```text
limited duration
```

called:
```text
time slice
```

---

# 19. Preemption

Kernel can interrupt running task:
```text
before completion
```

to schedule another task.

---

# 20. Scheduler Tick

Periodic timer interrupt triggers:
- accounting
- preemption
- scheduler decisions

---

# 21. Real-Time Scheduling

Linux supports:
```text
real-time tasks
```

with deterministic behavior.

---

# 22. Real-Time Policies

| Policy | Behavior |
|--------|----------|
| SCHED_FIFO | First-in-first-out |
| SCHED_RR | Round robin |
| SCHED_DEADLINE | Deadline-based |

---

# 23. SCHED_FIFO

Characteristics:
- highest priority runs
- no time slicing
- runs until block/yield

---

# 24. FIFO Flow

```text
Highest Priority Task Ready
         ↓
Runs Until:
  - Blocks
  - Yields
  - Higher Priority Arrives
``` id="fifo1"

---

# 25. SCHED_RR

Round-robin real-time scheduling.

Adds:
```text
time slices
```

between equal-priority tasks.

---

# 26. Round Robin Flow

```text
Task A
  ↓
Task B
  ↓
Task C
  ↓
Back to Task A
``` id="rr1"

---

# 27. SCHED_DEADLINE

Advanced real-time scheduler.

Uses:
- runtime
- deadline
- period

---

# 28. Priority Levels

Linux priorities:

| Type | Range |
|------|-------|
| Real-time | 0–99 |
| Normal | nice values |

---

# 29. nice Values

Normal process priorities controlled by:
```bash
nice
renice
```

Range:
```text
-20 to +19
```

---

# 30. Higher Priority Meaning

| Priority | Meaning |
|----------|---------|
| Lower nice value | Higher priority |
| Higher RT priority | Higher scheduling priority |

---

# 31. Scheduler Classes

Linux internally uses:
- stop class
- deadline class
- RT class
- fair class
- idle class

---

# 32. Multi-Core Scheduling

Modern Linux schedules tasks across:
```text
multiple CPUs
```

---

# 33. Load Balancing

Scheduler balances tasks between CPUs.

---

# 34. SMP Scheduling Flow

```text
CPU0 Run Queue
CPU1 Run Queue
CPU2 Run Queue
       ↓
Load Balancer
       ↓
Task Migration
``` id="smp1"

---

# 35. CPU Affinity

Restricts process to specific CPU.

---

## Example

```bash
taskset -c 0 ./app
``` id="aff1"

---

# 36. Kernel Threads

Kernel creates:
```text
kernel threads
```

Examples:
- kworker
- ksoftirqd

---

# 37. Interrupt Context

Interrupt handlers:
- not regular processes
- handled specially

---

# 38. SoftIRQ and Scheduling

SoftIRQs execute:
- deferred interrupt work
- networking tasks

---

# 39. Real-Time Linux (PREEMPT_RT)

PREEMPT_RT patch:
- improves determinism
- reduces latency
- makes Linux more RT-capable

---

# 40. Scheduling Latency

Latency:
```text
time before task gets CPU
```

Critical for:
- robotics
- industrial control
- audio systems

---

# 41. Embedded Linux Scheduling

Embedded systems often require:
- deterministic timing
- low latency
- RT scheduling

---

# 42. Process Scheduling Commands

---

## View Processes

```bash
ps
``` id="ps1"

---

## Real-Time Priority

```bash
chrt
``` id="rt1"

---

## CPU Usage

```bash
top
``` id="top1"

---

# 43. Viewing Scheduler Info

```bash
cat /proc/sched_debug
``` id="dbg1"

---

# 44. Process Priority Example

```bash
nice -n -5 ./app
``` id="nice1"

---

# 45. Real-Time Process Example

```bash
chrt -f 90 ./app
``` id="fifo2"

---

# 46. Scheduling in Kernel

Important kernel files:

```text
kernel/sched/
```

Contains:
- CFS
- RT scheduler
- load balancing

---

# 47. Common Scheduling Problems

| Problem | Description |
|---------|-------------|
| Starvation | Low priority never runs |
| Priority inversion | Lower priority blocks higher |
| Excess context switching | Performance overhead |

---

# 48. Priority Inversion

Occurs when:
- low-priority task holds resource
- high-priority task waits

Solution:
```text
priority inheritance
```

---

# 49. Complete Scheduling Workflow

```text
Processes Become Runnable
         ↓
Added to Run Queue
         ↓
Scheduler Selects Task
         ↓
Context Switch
         ↓
CPU Executes Task
         ↓
Task Blocks / Slice Ends
         ↓
Scheduler Runs Again
``` id="final1"

---

# 50. Scheduling in Embedded Linux Boot

```text
Kernel Boot
    ↓
Scheduler Initialized
    ↓
Init Process Starts
    ↓
User Processes Created
    ↓
Normal Scheduling Begins
``` id="boot2"

---

# 51. Advantages of Linux Scheduler

- scalable
- fair
- supports real-time
- multi-core aware
- highly optimized

---

# 52. Summary

- Scheduler controls CPU execution
- Linux uses CFS by default
- Supports real-time scheduling
- Uses run queues and context switching
- Essential for multitasking systems
- Critical in embedded Linux systems

---

````
