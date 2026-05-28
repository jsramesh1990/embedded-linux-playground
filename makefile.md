# Makefile 

## Overview

A **Makefile** is a build automation file used by the `make` tool to compile and link programs efficiently. It defines rules to build targets from source files.

---

# 1. Basic Structure of a Makefile

```

target: dependencies <TAB> command

```

### Example:
```

app: main.o math.o
gcc -o app main.o math.o

```

---

# 2. Simple Example (C Program)

## Project Structure
```

project/
├── main.c
├── math.c
├── math.h
└── Makefile

````

## Makefile
```makefile
CC = gcc
CFLAGS = -Wall -Wextra -O2

TARGET = app
SRCS = main.c math.c
OBJS = main.o math.o

all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) -o $(TARGET) $(OBJS)

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -f $(OBJS) $(TARGET)
````

---

# 3. Explanation of Key Variables

## CC

Compiler used

```
CC = gcc
```

## CFLAGS

Compiler flags

```
CFLAGS = -Wall -Wextra -O2
```

## TARGET

Output binary name

```
TARGET = app
```

## SRCS

Source files

```
SRCS = main.c math.c
```

## OBJS

Object files

```
OBJS = main.o math.o
```

---

# 4. Important Makefile Concepts

## 4.1 Rule Format

```
target: dependencies
	command
```

## IMPORTANT

* Command line MUST start with TAB (not spaces)

---

## 4.2 Automatic Variables

| Variable | Meaning          |
| -------- | ---------------- |
| `$@`     | Target name      |
| `$<`     | First dependency |
| `$^`     | All dependencies |

### Example:

```
%.o: %.c
	gcc -c $< -o $@
```

---

## 4.3 Pattern Rules

Used for generic compilation:

```
%.o: %.c
	gcc -c $< -o $@
```

---

# 5. Common Make Targets

## all

Default build target

```
all: app
```

## clean

Remove build files

```
clean:
	rm -f *.o app
```

## install

Install binary

```
install:
	cp app /usr/local/bin/
```

---

# 6. Embedded Linux Makefile Example

## Cross Compilation Setup

```
CC = arm-linux-gnueabihf-gcc
CFLAGS = -Wall -O2
```

## Example

```makefile
CC = arm-linux-gnueabihf-gcc
CFLAGS = -Wall -O2

TARGET = embedded_app
SRCS = main.c driver.c utils.c
OBJS = $(SRCS:.c=.o)

all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) -o $@ $^

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -f $(OBJS) $(TARGET)
```

---

# 7. Advanced Makefile Features

## 7.1 Conditional Compilation

```
DEBUG ?= 0

ifeq ($(DEBUG),1)
	CFLAGS += -g -DDEBUG
else
	CFLAGS += -O2
endif
```

---

## 7.2 Include Other Makefiles

```
include config.mk
```

---

## 7.3 Functions

```
SRCS = $(wildcard *.c)
OBJS = $(patsubst %.c,%.o,$(SRCS))
```

---

# 8. Clean Build Flow

```
make all   → compile project
make clean → remove objects
make       → default build
```

---

# 9. Common Errors

## Missing TAB

```
Makefile: *** missing separator.  Stop.
```

## Fix:

* Ensure commands start with TAB, not spaces

---

# 10. Build Flow Diagram

```
Source Files (.c)
      ↓
Compiler (gcc / cross-compiler)
      ↓
Object Files (.o)
      ↓
Linker
      ↓
Executable Binary
```

---

# 11. Summary

* Makefile automates build process
* Uses rules: target → dependencies → commands
* Supports variables, patterns, and automation
* Essential for embedded Linux development

---


