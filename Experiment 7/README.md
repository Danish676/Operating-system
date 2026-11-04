# Experiment 7: CPU-Bound vs I/O-Bound C Programs Using `top`

## Aim
To write and run a CPU-bound C program and an I/O-bound C program, then compare their CPU utilization, process states, and accumulated CPU time using the Linux `top` command.

## Objectives
- Understand the difference between CPU-bound and I/O-bound processes.
- Write a C program that repeatedly performs file operations.
- Write a C program that continuously performs calculations.
- Run both programs in the background.
- Identify process IDs (PIDs) and monitor processes using `top`.
- Compare CPU utilization, process states, and accumulated CPU time.

## Requirements
- Linux environment (such as Ubuntu)
- GCC compiler
- Terminal

## 1. I/O-Bound Program

**Filename:** `io_bound.c`

```c
#include <stdio.h>
#include <unistd.h>

int main()
{
    FILE *fp;

    printf("Starting I/O task...\n");

    while (1)
    {
        fp = fopen("dummy.txt", "w");

        if (fp != NULL)
        {
            fprintf(fp, "Writing disk data\n");
            fclose(fp);
            printf("File operation done\n");
        }

        sleep(1);
    }

    return 0;
}
```

### Explanation
- `stdio.h` provides standard input/output and file-handling functions such as `printf()`, `fopen()`, `fprintf()`, and `fclose()`.
- `unistd.h` provides the `sleep()` function.
- `FILE *fp` declares a file pointer for the file stream.
- `fopen("dummy.txt", "w")` opens the file in write mode, creating it if necessary and truncating its previous contents.
- `if (fp != NULL)` checks whether the file opened successfully.
- `fprintf()` writes text to the file stream.
- `fclose()` closes the stream and flushes the C library's buffered output.
- `sleep(1)` suspends the process for approximately one second, so it spends much of its time waiting rather than actively using the CPU.

### Expected Output

```text
Starting I/O task...
File operation done
File operation done
File operation done
...
```

The confirmation message should appear approximately once per second. Actual timing may vary.

## 2. CPU-Bound Program

**Filename:** `cpu_bound.c`

```c
#include <stdio.h>

int main()
{
    long long counter = 0;

    while (1)
    {
        counter++;

        if (counter % 100000000 == 0)
        {
            printf("CPU task running: %lld\n", counter);
        }
    }

    return 0;
}
```

### Explanation
- `stdio.h` provides the `printf()` function.
- `long long counter = 0` declares and initializes a counter.
- `while (1)` creates an infinite loop.
- `counter++` repeatedly increments the counter.
- `counter % 100000000 == 0` checks whether the counter is divisible by 100 million.
- `printf()` displays the counter whenever the condition is true.
- The loop continuously performs calculations, making this a CPU-bound process.

### Expected Output

```text
CPU task running: 100000000
CPU task running: 200000000
CPU task running: 300000000
...
```

The output appears each time the counter reaches another multiple of 100 million.

## 3. Compile the Programs

Run the following commands:

```bash
gcc io_bound.c -o io_bound
gcc cpu_bound.c -o cpu_bound
```

## 4. Run Both Programs in the Background

```bash
./io_bound &
./cpu_bound &
```

The `&` symbol starts each program in the background, allowing the terminal to accept further commands.

## 5. Find the Process IDs

```bash
pgrep -af 'io_bound|cpu_bound'
```

Example output:

```text
2451 ./io_bound
2452 ./cpu_bound
```

The numbers shown are example PIDs. Use the actual PIDs displayed on your system in the monitoring commands.

## 6. Monitor Processes Using `top`

| Purpose | Command | Notes |
|---|---|---|
| Display all processes | `top` | Press `P` to sort by CPU utilization. |
| Monitor the two programs | `top -p 2451,2452` | Replace the example PIDs with the actual PIDs. |
| Refresh every second | `top -d 1` | Updates the display once per second. |
| Collect five snapshots | `top -b -n 5 -d 1` | Useful for recording readings. |
| View individual CPU cores | `top`, then press `1` | Displays utilization for each logical CPU. |

## 7. Observation

Record the actual readings from your system.

| Parameter | CPU-Bound Program | I/O-Bound Program |
|---|---|---|
| CPU utilization (`%CPU`) | Generally high | Generally low |
| Process state | Usually `R` when observed | Often `S` during sleep |
| CPU time (`TIME+`) | Increases rapidly | Increases more slowly |
| Main activity | Repeated counter increments | File writing and sleeping |

### Expected Observations
- The CPU-bound process should generally show high CPU utilization because it continuously performs calculations.
- The I/O-bound process should generally show low CPU utilization because it sleeps for one second after each file operation.
- The CPU-bound process generally accumulates CPU time faster.
- A single-threaded process may use approximately 100% of one logical CPU, depending on system load and how `top` displays CPU usage.

Actual values can vary with system load and scheduling.

## 8. Stop the Background Programs

Find the PIDs using `pgrep`, then stop the processes:

```bash
kill <io_bound_PID> <cpu_bound_PID>
```

Replace the placeholders with the actual process IDs. Alternatively, bring a background job to the foreground with `fg` and stop it with `Ctrl+C`.

## Result
Both C programs were executed successfully. The `top` command was used to compare CPU utilization, process states, and accumulated CPU time. The CPU-bound program generally consumed more CPU time, whereas the I/O-bound program used less CPU time because it spent much of its time sleeping.

## Conclusion
This experiment demonstrates the difference between CPU-bound and I/O-bound processes. The CPU-bound program continuously performs calculations and generally uses more CPU resources, while the I/O-bound program performs file operations and spends much of its time waiting. The Linux `top` command helps observe and compare their CPU utilization, process states, and accumulated CPU time.
