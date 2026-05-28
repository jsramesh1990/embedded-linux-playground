# C Preprocessing

## Overview

The **preprocessor** is the first step in the C compilation process. It processes directives starting with `#` before actual compilation begins.

It handles:
- Macros (`#define`)
- File inclusion (`#include`)
- Conditional compilation (`#if`, `#ifdef`)
- Code transformation

---

# 1. Compilation Flow (Context)

```

Source Code (.c)
↓
Preprocessor
↓
Expanded C Code
↓
Compiler
↓
Assembler
↓
Linker
↓
Executable

````id="prep01"

---

# 2. #define (Macros)

## 2.1 Simple Macro

```c
#define PI 3.14159
``` id="def01"

### Usage
```c
float area = PI * r * r;
````

### Preprocessor Output

```c
float area = 3.14159 * r * r;
```

---

## 2.2 Function-like Macros

````c
#define SQUARE(x) ((x) * (x))
``` id="def02"

### Usage
```c
int a = SQUARE(5);
````

### Expands to

```c
int a = ((5) * (5));
```

---

## 2.3 Macro Pitfall (IMPORTANT)

````c
#define SQUARE(x) x * x
``` id="pit01"

### Problem:
````

SQUARE(2 + 3)
= 2 + 3 * 2 + 3
= 11 (WRONG)

````

### Correct version:
```c
#define SQUARE(x) ((x) * (x))
````

---

# 3. Types of Macros

## 3.1 Object-like Macro

````c
#define BUFFER_SIZE 1024
``` id="obj01"

Used for constants.

---

## 3.2 Function-like Macro
```c
#define MAX(a,b) ((a) > (b) ? (a) : (b))
``` id="func01"

Used for inline logic.

---

## 3.3 Multi-line Macro

```c
#define DEBUG_PRINT(x)   \
    printf("DEBUG: %s\n", x)
``` id="multi01"

---

# 4. #include Directive

## 4.1 Standard Include

```c
#include <stdio.h>
``` id="inc01"

- Searches system include paths

---

## 4.2 User Include

```c
#include "myfile.h"
``` id="inc02"

- Searches current directory first

---

# 5. Conditional Compilation

## 5.1 #ifdef

```c
#define DEBUG

#ifdef DEBUG
printf("Debug mode\n");
#endif
``` id="cond01"

---

## 5.2 #ifndef

```c
#ifndef CONFIG_H
#define CONFIG_H
#endif
``` id="cond02"

Used for header guards.

---

## 5.3 #if / #elif / #else

```c
#define VERSION 2

#if VERSION == 1
printf("V1");
#elif VERSION == 2
printf("V2");
#else
printf("Unknown");
#endif
``` id="cond03"

---

# 6. Macro vs Function

| Feature | Macro | Function |
|--------|-------|----------|
| Execution | Compile-time | Runtime |
| Speed | Faster | Slower |
| Type checking | No | Yes |
| Debugging | Hard | Easy |

---

# 7. Advanced Macros

## 7.1 Stringizing (#)

```c
#define STR(x) #x
``` id="str01"

### Usage:
```c
STR(hello)
````

### Expands to:

```
"hello"
```

---

## 7.2 Token Pasting (##)

````c
#define CONCAT(a,b) a##b
``` id="tok01"

### Usage:
```c
CONCAT(var, 1)
````

### Expands to:

```
var1
```

---

# 8. Variadic Macros

````c
#define LOG(fmt, ...) printf(fmt, __VA_ARGS__)
``` id="var01"

### Usage:
```c
LOG("%d %d", a, b);
````

---

# 9. Predefined Macros

| Macro      | Meaning           |
| ---------- | ----------------- |
| `__FILE__` | Current file name |
| `__LINE__` | Line number       |
| `__DATE__` | Compilation date  |
| `__TIME__` | Compilation time  |

---

### Example:

```c
printf("Error in %s at line %d\n", __FILE__, __LINE__);
```

---

# 10. Embedded Systems Use Cases

* Hardware register definitions
* Feature toggles
* Platform-specific code
* Debug logging
* Memory-mapped I/O

---

## Example (Hardware Register)

````c
#define GPIO_BASE 0x40020000
#define GPIO_ODR (*(volatile int*)(GPIO_BASE + 0x14))
``` id="emb01"

---

# 11. Common Mistakes

## 11.1 Missing Parentheses
```c
#define ADD(x,y) x + y
``` id="err01"

Problem:
````

ADD(2,3) * 4 → 2 + 3 * 4 = 14 (WRONG)

````

---

## 11.2 Side Effects
```c
#define INC(x) (x + 1)
``` id="err02"

If x is expression → evaluated multiple times.

---

# 12. Debugging Preprocessor Output

Generate expanded code:

````

gcc -E file.c

```id="dbg01"

---

# 13. Summary

- Preprocessor runs before compilation
- `#define` creates macros (constants or functions)
- Macros are text substitution, not real functions
- Conditional compilation controls build features
- Essential for embedded hardware control

---

