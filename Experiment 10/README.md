# Experiment 10: Observing Virtual Memory Addresses in C

**Model Institute of Engineering & Technology (Autonomous)**  
**Course:** COM-312 Operating System Lab

## Aim

To write, compile, and execute a C program that prints the address of a local variable, run multiple instances concurrently, and compare the printed virtual addresses with Address Space Layout Randomization (ASLR) enabled and disabled.

## Objectives

- Understand that C pointers in a normal Linux process represent virtual addresses, not direct physical DRAM addresses.
- Run multiple independent processes concurrently using the shell background operator (`&`).
- Observe how ASLR randomizes process memory layout, including stack locations.
- Disable ASLR for a test process using `setarch $(uname -m) -R` and compare the resulting addresses.
- Understand how the Memory Management Unit (MMU) and per-process page tables provide memory isolation.

## 1. Theory

### 1.1 Virtual address space

Each Linux process has its own virtual address space. It contains areas for program code, initialized and uninitialized data, the heap, shared libraries, and the stack. The operating system and hardware manage the mapping from virtual addresses to physical memory.

Two different processes can use the same numeric virtual address without accessing the same physical memory. Their address translations are managed independently.

### 1.2 Virtual addresses and physical addresses

A pointer such as `&var` contains the virtual address of the variable in the current process. When the CPU accesses memory, the Memory Management Unit (MMU) translates the virtual address through the active page tables to a physical memory location.

Normal user-space C programs work with virtual addresses; they do not directly expose physical DRAM addresses.

### 1.3 Address Space Layout Randomization (ASLR)

ASLR is a security feature that makes memory locations less predictable. On Linux, it can randomize locations such as the stack, shared libraries, and other memory mappings. This makes certain memory-corruption exploits harder to carry out.

With ASLR disabled for a process, its memory layout can become more predictable. The exact result can depend on the executable, system configuration, environment, and architecture.

## 2. Source Code

Save the following program as `virtual_addr.c`:

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    int var = 42;

    printf("PID: %d | Address: %p\n", getpid(), (void *)&var);
    fflush(stdout);

    while (1) {
        sleep(1);
    }

    return 0;
}
```

### Code explanation

| Statement | Purpose |
|---|---|
| `#include <stdio.h>` | Provides `printf()` and `fflush()`. |
| `#include <unistd.h>` | Provides `getpid()` and `sleep()`. |
| `int var = 42;` | Declares a local automatic variable, normally stored on the process stack. |
| `(void *)&var` | Gets the variable's address and converts it to the type expected by `%p`. |
| `getpid()` | Returns the current process ID. |
| `printf()` | Prints the process ID and the variable's address. |
| `fflush(stdout)` | Flushes the output immediately. |
| `while (1)` | Keeps the process running. |
| `sleep(1)` | Suspends execution for one second during each loop iteration. |
| `return 0;` | Normal return statement; it is not reached while the infinite loop continues. |

## 3. Execution Procedure

### Step 1: Compile the program

```bash
gcc virtual_addr.c -o virtual_addr
```

### Step 2: Start four instances with ASLR enabled

Run each command in the terminal:

```bash
./virtual_addr &
./virtual_addr &
./virtual_addr &
./virtual_addr &
```

Illustrative output recorded during the experiment:

```text
PID: 5101 | Address: 0x7ffd8a3e41ac
PID: 5102 | Address: 0x7ffc1b9028dc
PID: 5103 | Address: 0x7ffe44d1953c
PID: 5104 | Address: 0x7ffdf012b76c
```

The addresses differ because the processes have independently randomized memory layouts.

### Step 3: Verify the running processes

```bash
pgrep -a virtual_addr
```

Recorded output:

```text
5101 ./virtual_addr
5102 ./virtual_addr
5103 ./virtual_addr
5104 ./virtual_addr
```

The output lists the matching process IDs and commands.

### Step 4: Stop the first group

```bash
killall virtual_addr
```

### Step 5: Compile without PIE and run with ASLR disabled

Compile a non-PIE executable:

```bash
gcc -no-pie virtual_addr.c -o virtual_addr_fixed
```

Start four instances with ASLR disabled for each launched process:

```bash
setarch $(uname -m) -R ./virtual_addr_fixed &
setarch $(uname -m) -R ./virtual_addr_fixed &
setarch $(uname -m) -R ./virtual_addr_fixed &
setarch $(uname -m) -R ./virtual_addr_fixed &
```

Illustrative output recorded during the experiment:

```text
PID: 5210 | Address: 0x7fffffffde4c
PID: 5211 | Address: 0x7fffffffde4c
PID: 5212 | Address: 0x7fffffffde4c
PID: 5213 | Address: 0x7fffffffde4c
```

In this recorded run, all four processes printed the same virtual address.

### Step 6: Stop the second group

```bash
killall virtual_addr_fixed
```

If `setarch` reports that changing the personality is not permitted, the environment may restrict this operation. The command's success can depend on system permissions and configuration.

## 4. Observations and Analysis

### 4.1 ASLR enabled

| Process | PID | Printed virtual address |
|---|---:|---|
| 1 | 5101 | `0x7ffd8a3e41ac` |
| 2 | 5102 | `0x7ffc1b9028dc` |
| 3 | 5103 | `0x7ffe44d1953c` |
| 4 | 5104 | `0x7ffdf012b76c` |

The addresses are different. Their high-order portions are similar, while the middle portions vary. This is consistent with randomized stack placement.

### 4.2 ASLR disabled

| Process | PID | Printed virtual address |
|---|---:|---|
| 1 | 5210 | `0x7fffffffde4c` |
| 2 | 5211 | `0x7fffffffde4c` |
| 3 | 5212 | `0x7fffffffde4c` |
| 4 | 5213 | `0x7fffffffde4c` |

In the recorded run, all four processes printed the same virtual address. This does not mean that they share the same physical memory.

### 4.3 Combined observation table

| ASLR mode | Process | PID | Printed virtual address | Memory collision |
|---|---|---:|---|---|
| Enabled | 1 | 5101 | `0x7ffd8a3e41ac` | None observed |
| Enabled | 2 | 5102 | `0x7ffc1b9028dc` | None observed |
| Enabled | 3 | 5103 | `0x7ffe44d1953c` | None observed |
| Enabled | 4 | 5104 | `0x7ffdf012b76c` | None observed |
| Disabled | 1 | 5210 | `0x7fffffffde4c` | None observed |
| Disabled | 2 | 5211 | `0x7fffffffde4c` | None observed |
| Disabled | 3 | 5212 | `0x7fffffffde4c` | None observed |
| Disabled | 4 | 5213 | `0x7fffffffde4c` | None observed |

**Note:** The physical frame labels or addresses are not measured by this program. The important observation is that identical virtual addresses in separate processes do not imply a physical-memory collision.

## 5. Why Identical Virtual Addresses Do Not Collide

Each process has its own virtual memory mappings and page-table context. When a process accesses a virtual address, the MMU translates it using the page tables associated with the currently active address space.

During a context switch, the operating system switches the active memory-management context. On x86-64 systems, the CR3 register is involved in identifying the active page-table hierarchy (with details depending on the architecture and kernel configuration).

Therefore, the same virtual address in two processes can translate to different physical frames. A process normally cannot access another process's private memory through its own page tables. Invalid or unauthorized accesses can cause a page fault and may result in a signal such as `SIGSEGV`.

The physical frame examples below are conceptual illustrations, not values obtained from the experiment:

```text
Process 1: Virtual address -> Process 1 page tables -> Physical frame A
Process 2: Virtual address -> Process 2 page tables -> Physical frame B
Process 3: Virtual address -> Process 3 page tables -> Physical frame C
Process 4: Virtual address -> Process 4 page tables -> Physical frame D
```

## 6. Quick Summary

- **Virtual is not physical:** A pointer printed by a normal C program is a virtual address.
- **ASLR:** Randomizes parts of a process's memory layout to make addresses harder to predict.
- **Disabling ASLR:** `setarch $(uname -m) -R` requests that ASLR be disabled for the launched process.
- **MMU and page tables:** Translate virtual addresses to physical memory and help isolate processes.
- **Identical addresses:** Separate processes can print the same virtual address without colliding because their address translations are separate.

## 7. Result

The `virtual_addr.c` program was compiled and executed in multiple concurrent background processes. With ASLR enabled, the recorded processes printed different stack-variable virtual addresses. With ASLR disabled using `setarch $(uname -m) -R`, the recorded processes printed the same virtual address.

The experiment demonstrates that C pointers represent virtual addresses and that process-specific memory mappings and MMU page-table translation allow independent processes to use identical virtual address values without interfering with one another.
