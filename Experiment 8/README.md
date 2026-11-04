# Experiment 8: Observing Process Memory Maps Using `pmap`

## Aim
To examine and compare the memory mappings of the CPU-bound and I/O-bound C programs from Experiment 7 using the Linux `pmap` command.

## Objectives
- Inspect the virtual address space of a running process.
- Identify executable mappings, data/BSS, stack, heap, shared libraries, and other memory regions.
- Understand the `pmap -x` columns: Address, Kbytes, RSS, Dirty, Mode, and Mapping.
- Examine read, write, and execute permissions.
- Compare the memory maps of CPU-bound and I/O-bound processes.
- Record actual memory-mapping statistics and clean up the processes after the experiment.

## Requirements
- Linux environment (such as Ubuntu)
- GCC compiler
- The `io_bound` and `cpu_bound` programs from Experiment 7
- `pmap` utility

## 1. Run Both Programs

Start the programs in the background:

```bash
./io_bound &
./cpu_bound &
```

## 2. Find Their Process IDs

```bash
pgrep -af 'io_bound|cpu_bound'
```

Example output:

```text
2451 ./io_bound
2452 ./cpu_bound
```

Use the actual PIDs displayed on your system in the commands below.

## 3. Display the Process Memory Maps

For the CPU-bound process:

```bash
pmap <CPU_PID>
pmap -x <CPU_PID>
```

For the I/O-bound process:

```bash
pmap <IO_PID>
pmap -x <IO_PID>
```

The basic `pmap` command displays the process memory mappings. The `-x` option provides extended information, including mapping size, resident memory, dirty memory, permissions, and mapping name.

## 4. Understanding the Memory Regions

Both programs are C programs running under Linux, so their memory maps contain broadly similar types of regions.

| Memory region | Meaning |
|---|---|
| Executable code | Machine instructions generated from the C source |
| Data/BSS | Storage for applicable global or static variables |
| Stack | Function-call information and local variables, where stored on the stack |
| Heap | Dynamic memory allocations, if any |
| Shared libraries | Libraries required by the program, such as the C standard library |
| Other mappings | Additional regions used by the runtime and operating system |

In the CPU-bound program, `counter` is a local variable. In the I/O-bound program, `fp` is a local file pointer. Both programs may have stack mappings, although the compiler can choose how individual variables are stored. Neither program explicitly calls `malloc()`, so neither requires a large dynamic allocation for its main task.

## 5. Understanding `pmap -x` Output

Each row describes one contiguous region of the process's virtual address space.

| Column | Meaning |
|---|---|
| Address | Starting virtual address of the memory region |
| Kbytes | Total size of the mapping, in kilobytes |
| RSS | Resident Set Size: how much of the mapping is resident in physical RAM |
| Dirty | Amount of memory in the mapping reported as dirty |
| Mode | Access permissions for the mapping |
| Mapping | File, library, or memory region associated with the mapping |

### Address
The address is the starting point of a region in the process's virtual address space. It is not necessarily a physical RAM address. Different processes have separate virtual address spaces, so similar virtual addresses in different processes do not imply that they use the same physical memory.

### Kbytes
This is the total size of the individual mapping. A large Kbytes value does not mean that all of the mapping is currently resident in physical RAM.

### RSS
RSS stands for Resident Set Size. It reports how much of the mapping is resident in physical RAM at the time of inspection. Shared pages can also be counted in RSS, so RSS should not be treated as exact private memory usage.

### Dirty
Dirty reports the amount of memory associated with modified pages that are not in a clean state relative to their backing storage. Its interpretation can differ for anonymous and file-backed memory. A value of zero means no dirty memory was reported for that mapping.

### Mode
The mode field shows the access permissions:

| Permission | Meaning |
|---|---|
| `r` | Read permission |
| `w` | Write permission |
| `x` | Execute permission |
| `-` | Permission not granted |

Examples:
- `r-x--`: the region can be read and executed, but not written.
- `rw---`: the region can be read and written, but not executed.
- `r----`: the region can only be read.

These permissions help protect memory regions from inappropriate access.

### Mapping
The mapping column identifies the file or memory region associated with the mapping. Common examples include:

| Mapping | Meaning |
|---|---|
| `cpu_bound` or `io_bound` | Mapping of the compiled executable |
| `libc.so.6` | C standard library |
| `ld-linux-x86-64.so.2` | Dynamic linker/loader |
| `[ anon ]` | Anonymous memory mapping not directly associated with a named file |
| `[ stack ]` | Stack mapping of the main thread |

Names and exact mappings can differ between Linux systems.

### Reading a Complete Row

Illustrative example:

```text
000076ec1b228000 1632 1056 0 r-x-- libc.so.6
```

| Column | Value | Meaning |
|---|---:|---|
| Address | `000076ec1b228000` | Starting virtual address |
| Kbytes | 1632 KB | Total size of this mapping |
| RSS | 1056 KB | Resident memory for this mapping |
| Dirty | 0 KB | No dirty memory reported |
| Mode | `r-x--` | Read and execute permissions |
| Mapping | `libc.so.6` | C standard library mapping |

### Total Row

An illustrative total row:

```text
total kB 2772 1744 96
```

This represents:
- `2772 KB`: total mapped size of all listed mappings.
- `1744 KB`: total reported RSS.
- `96 KB`: total reported dirty memory.

These are totals for the process's listed mappings, not values for a single region.

**Quick summary:** Address tells where a mapping starts; Kbytes tells its total mapped size; RSS tells how much is resident; Dirty reports modified memory; Mode shows permissions; and Mapping identifies the associated file or memory type.

## 6. Observation Table

Record the actual readings from your system.

| Parameter | CPU-bound process | I/O-bound process |
|---|---|---|
| Executable mapping | Present | Present |
| Shared libraries | Typically present | Typically present |
| Stack | Typically present | Typically present |
| Total mapped memory | Record actual value | Record actual value |
| RSS | Record actual value | Record actual value |
| Dirty memory | Record actual value | Record actual value |

## 7. Analysis

The CPU-bound program performs repeated calculations, while the I/O-bound program writes to a file and sleeps. These differences primarily affect CPU utilization and waiting time, not necessarily the size of the memory map.

The `pmap` command helps inspect how memory is mapped for each process. It does not directly measure CPU share; that is the role of the `top` command.

## 8. Cleanup

Find the relevant process IDs:

```bash
pgrep -af 'io_bound|cpu_bound'
```

Terminate the processes using their actual PIDs:

```bash
kill <IO_PID> <CPU_PID>
```

This prevents the infinite loops from continuing to run after the experiment.

## Result
The memory maps of both programs were examined using `pmap` and `pmap -x`. Their executable mappings, stack, shared libraries, and memory statistics were observed and compared.

## Conclusion
This experiment demonstrates how Linux organizes a process's virtual address space into memory mappings. Using `pmap -x`, the mapping size, resident memory, dirty memory, permissions, and associated files or memory regions can be inspected. Comparing the CPU-bound and I/O-bound programs also shows that their different workloads do not necessarily produce substantially different memory-map structures.
