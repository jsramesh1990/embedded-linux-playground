# Storage Classes

## Overview

Storage classes in C define:
- **Scope (where variable is visible)**
- **Lifetime (how long it exists in memory)**
- **Linkage (whether it is shared across files)**

They are extremely important in:
- Embedded systems
- Linux kernel
- Multi-file projects
- Memory optimization

---

# 1. Types of Storage Classes

C provides 4 main storage classes:

1. `auto`
2. `register`
3. `static`
4. `extern`

---

# 2. auto (Default Storage Class)

## Definition

Automatic variables created inside a function.

```c
void func() {
    int a = 10;   // auto by default
}
``` id="auto1"

---

## Characteristics

| Feature | Value |
|--------|------|
| Scope | Local (function) |
| Lifetime | Function execution |
| Storage | Stack |
| Default keyword | Yes |

---

## Equivalent form

```c
auto int a = 10;
````

(rarely used explicitly)

---

# 3. register Storage Class

## Definition

Suggests storing variable in CPU register for faster access.

````c
register int counter = 0;
``` id="reg1"

---

## Characteristics

| Feature | Value |
|--------|------|
| Scope | Local |
| Lifetime | Function |
| Storage | CPU register (if possible) |
| Address (&) | Not allowed |

---

## Important Note

Compiler may ignore request.

---

## Example

```c
register int i;
for (i = 0; i < 10; i++) {
    // fast loop variable
}
``` id="reg2"

---

# 4. static Storage Class

## Definition

Preserves value across function calls and limits scope.

---

## 4.1 Static Local Variable

```c
void counter() {
    static int count = 0;
    count++;
    printf("%d\n", count);
}
``` id="stat1"

---

### Output
````

1
2
3

````

---

## 4.2 Static Global Variable

```c
static int value = 100;
``` id="stat2"

- Visible only in same file

---

## 4.3 Static Function

```c
static void helper() {
    // internal function
}
``` id="stat3"

---

## Characteristics

| Feature | Value |
|--------|------|
| Scope | File / Function |
| Lifetime | Entire program |
| Storage | Data segment |
| Linkage | Internal |

---

# 5. extern Storage Class

## Definition

Declares variable defined in another file.

```c
extern int global_var;
``` id="ext1"

---

## Example

### file1.c
```c
int global_var = 10;
``` id="ext2"

---

### file2.c
```c
extern int global_var;
``` id="ext3"

---

## Characteristics

| Feature | Value |
|--------|------|
| Scope | Global |
| Lifetime | Entire program |
| Storage | Data segment |
| Linkage | External |

---

# 6. Comparison Table

| Feature | auto | register | static | extern |
|--------|------|----------|--------|--------|
| Scope | Local | Local | Local/File | Global |
| Lifetime | Function | Function | Program | Program |
| Storage | Stack | Register | Data segment | Data segment |
| Linkage | None | None | Internal | External |

---

# 7. Memory Layout View

````

STACK
├── auto variables
├── register (CPU register preferred)

DATA SEGMENT
├── static variables
├── extern variables

TEXT SEGMENT
├── functions

````id="mem1"

---

# 8. Embedded Linux Usage

## auto
- Local computations
- Stack-based variables

## register
- Loop counters in performance-critical code

## static
- Driver state
- Interrupt counters
- Persistent buffers

## extern
- Shared driver state
- Kernel subsystem variables

---

# 9. Real Embedded Example

```c
static int irq_count = 0;

void irq_handler() {
    irq_count++;
}
``` id="emb1"

---

```c
extern int system_status;
````

---

# 10. Common Mistakes

## 10.1 Using register incorrectly

```c
register int *p;
printf("%p", p); // not allowed sometimes
```

---

## 10.2 Forgetting extern definition

```c
extern int x;  // error if not defined anywhere
```

---

## 10.3 Overusing static globally

* Reduces modularity if misused

---

# 11. Storage Class vs Scope vs Lifetime

| Concept       | Meaning                   |
| ------------- | ------------------------- |
| Scope         | Where variable is visible |
| Lifetime      | How long variable exists  |
| Storage class | Controls both             |

---

# 12. Key Differences Summary

* `auto` → default local variable
* `register` → CPU optimized local variable
* `static` → persistent or file-private variable
* `extern` → shared across files

---

# 13. Summary

Storage classes define:

* visibility
* lifetime
* memory behavior

They are essential in:

* Embedded systems
* Linux drivers
* Multi-file projects

---

