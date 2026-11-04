# Experiment 6: Memory Allocation & Virtual Memory in C

## Aim
To write and execute a C program that dynamically allocates memory using `malloc()`, accesses the allocated memory page by page, observes memory usage in Linux, and releases the memory when the user presses `Ctrl+C`.

## Objectives
- Understand dynamic memory allocation using `malloc()`.
- Learn how command-line arguments are passed through `argc` and `argv`.
- Observe the difference between reserving virtual address space and backing it with physical RAM.
- Understand page-based memory access and demand paging.
- Monitor memory usage using `free -h` and `watch`.
- Handle the `SIGINT` signal and release allocated memory using `free()`.

## Requirements
- Linux environment (such as Ubuntu or an Ubuntu virtual machine)
- GCC compiler
- Terminal

## Program Overview
The program accepts the requested memory size in megabytes as a command-line argument. It converts that value into bytes, allocates memory using `malloc()`, and writes to one byte in each 4096-byte page. It then remains active so that memory usage can be observed from another terminal.

## Source Code

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>

char *memory;

void handle_sigint(int sig)
{
    free(memory);
    printf("\nMemory released.\n");
    exit(0);
}

int main(int argc, char *argv[])
{
    int mb;

    if (argc != 2)
    {
        printf("Usage: %s <memory_in_MB>\n", argv[0]);
        return 1;
    }

    signal(SIGINT, handle_sigint);

    mb = atoi(argv[1]);
    size_t size = (size_t)mb * 1024 * 1024;

    memory = malloc(size);

    if (memory == NULL)
    {
        printf("Memory allocation failed.\n");
        return 1;
    }

    for (size_t i = 0; i < size; i += 4096)
    {
        memory[i] = 1;
    }

    printf("Memory allocated successfully.\n");
    printf("Press Ctrl+C to release memory and exit.\n");

    while (1)
    {
        sleep(1);
    }

    return 0;
}
```

## Compilation and Execution

Compile the program:

```bash
gcc memory.c -o memory
```

Run it by specifying the memory size. For example, to allocate 500 MiB:

```bash
./memory 500
```

Open a second terminal while the program is running and monitor memory usage:

```bash
free -h
```

To refresh the memory statistics every second:

```bash
watch -n 1 free -h
```

Press `Ctrl+C` in the program terminal to trigger `SIGINT`, call the signal handler, release the allocated memory, and exit.

## Explanation of Key Concepts

### 1. Command-Line Arguments: `argc` and `argv`
- `argc` stores the number of command-line arguments, including the program name.
- `argv` is an array of strings containing those arguments.
- When running `./memory 500`, `argc` is `2`, `argv[0]` is `./memory`, and `argv[1]` is the string `500`.
- `atoi()` converts the string argument into an integer.

### 2. Dynamic Memory Allocation: `malloc()`
The expression `(size_t)mb * 1024 * 1024` converts the requested size into bytes. `malloc(size)` requests a block of that size and returns a pointer to its starting address. If allocation fails, it returns `NULL`.

### 3. Virtual Memory and Demand Paging
A successful `malloc()` request reserves a virtual address range, but the corresponding physical pages may not all be allocated immediately. The program writes to one byte every 4096 bytes to touch each page and cause the operating system to make the page resident as needed.

### 4. Page Size
The program uses a 4096-byte (4 KiB) step, matching the commonly used Linux page size in this experiment. Touching one byte in each page is sufficient to access each page; writing every byte is unnecessary for demonstrating page allocation.

### 5. Monitoring Memory
- `free -h` displays memory usage in a human-readable format.
- `watch -n 1 free -h` runs the command repeatedly, once per second, so changes can be observed while the program is active.

### 6. Signal Handling and Cleanup
Pressing `Ctrl+C` sends the `SIGINT` signal. The program registers `handle_sigint()` with `signal(SIGINT, handle_sigint)`. The handler releases the allocated block with `free()` and then exits.

> Note: Calling `printf()` and `free()` directly inside a signal handler is not strictly signal-safe for production programs. The experiment uses this simplified approach for teaching; robust programs generally arrange for cleanup to occur in the main execution flow.

## Expected Observation
After running `./memory 500`, the program reports that memory has been allocated and remains active. The memory statistics can be observed in the second terminal using `free -h` or `watch -n 1 free -h`. When `Ctrl+C` is pressed, the program prints `Memory released.` and exits.

Actual memory figures may vary depending on system load, available RAM, virtual-machine settings, and operating-system memory management.

## Conclusion
This experiment demonstrates that requesting virtual memory with `malloc()`, accessing the allocated pages, and releasing the memory are distinct steps. By touching memory at 4096-byte intervals and monitoring the system with `free -h`, the behavior of demand paging and physical memory usage can be observed. Signal handling also demonstrates how a program can respond to `Ctrl+C` and perform an explicit cleanup operation.
