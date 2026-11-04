# Experiment 9: Process Creation and Termination Using C

**Institution:** Model Institute of Engineering & Technology (Autonomous)  
**Course:** COM-312 Operating System Lab

## Aim
To write and execute C programs that retrieve a process ID, create a child process, wait for its termination, and display the terminated child's PID under Linux.

## Objectives
- Understand what a Process ID (PID) is and retrieve it using `getpid()`.
- Demonstrate process creation using `fork()` and understand its return values.
- Synchronize parent and child processes using `wait(NULL)`.
- Display the PID of the child process after it terminates.

## Requirements
- Linux environment
- GCC compiler
- Terminal

## 1. Step 1: Retrieving a Process ID Using `getpid()`

Every running process is assigned a unique identification number called a Process ID (PID). The `getpid()` function, available through `unistd.h`, returns the PID of the currently running process.

**Filename:** `step1_pid.c`

```c
#include <stdio.h>
#include <unistd.h>

int main()
{
    printf("I am a simple process. My PID is: %d\n", getpid());
    return 0;
}
```

### Compile and Run

```bash
gcc step1_pid.c -o step1_pid
./step1_pid
```

### Sample Output

```text
I am a simple process. My PID is: 3412
```

The PID shown will vary each time the program is executed.

**Key takeaway:** One running program instance is represented by a process with its own PID.

## 2. Step 2: Creating a Child Process Using `fork()`

The `fork()` system call creates a child process. After the call, both parent and child continue executing the program.

- In the child process, `fork()` returns `0`.
- In the parent process, `fork()` returns the positive PID of the child.

**Filename:** `step2_fork.c`

```c
#include <stdio.h>
#include <unistd.h>

int main()
{
    pid_t p = fork();

    if (p == 0)
    {
        printf("I am the CHILD process! My own PID: %d\n", getpid());
    }
    else
    {
        printf("I am the PARENT process! fork() gave me child's PID: %d\n", p);
    }

    return 0;
}
```

### Compile and Run

```bash
gcc step2_fork.c -o step2_fork
./step2_fork
```

### Sample Output

```text
I am the PARENT process! fork() gave me child's PID: 3450
I am the CHILD process! My own PID: 3450
```

The sample illustrates the two execution paths. The actual PID values and the order of the output can vary because the parent and child execute independently. In the sample, the child's PID is printed by both processes.

## 3. Step 3: Synchronizing Parent and Child Using `wait()`

The `wait()` function, declared in `sys/wait.h`, allows the parent to wait until a child process terminates. `wait(NULL)` blocks the parent until a child exits and returns the PID of the terminated child.

**Filename:** `process_creation_wait.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

int main()
{
    pid_t p = fork();

    if (p == 0)
    {
        printf("[Child] Work started. My PID: %d\n", getpid());
        sleep(2);
        printf("[Child] Work finished! Exiting now.\n");
        exit(0);
    }
    else
    {
        printf("[Parent] Child started. Now waiting for it to finish...\n");

        pid_t terminated_pid = wait(NULL);

        printf("[Parent] Child with PID %d has terminated!\n", terminated_pid);
    }

    return 0;
}
```

### Compile and Run

```bash
gcc process_creation_wait.c -o exp9
./exp9
```

### Sample Output

```text
[Parent] Child started. Now waiting for it to finish...
[Child] Work started. My PID: 3450
[Child] Work finished! Exiting now.
[Parent] Child with PID 3450 has terminated!
```

The child pauses for two seconds using `sleep(2)`, making it easier to observe that the parent waits. The parent continues after the child terminates and `wait(NULL)` returns the child's PID.

## 4. Line-by-Line Concept Breakdown

| Code statement | Operating system explanation |
|---|---|
| `pid_t p = fork();` | Creates a child process. Returns `0` to the child and the child's PID to the parent. |
| `if (p == 0)` | Selects the child process's execution branch. |
| `getpid()` | Retrieves the PID of the process that calls it. |
| `sleep(2);` | Pauses the child for approximately two seconds. |
| `exit(0);` | Terminates the child process successfully. |
| `else` | Selects the parent process's execution branch when `fork()` succeeds. |
| `wait(NULL);` | Blocks the parent until a child terminates and returns the terminated child's PID. |

## 5. Quick Summary

- **`getpid()`**: Retrieves the current process's own PID.
- **`fork()`**: Creates a child process. It returns `0` in the child and the child's PID in the parent.
- **`wait(NULL)`**: Makes the parent wait for a child to terminate and returns the terminated child's PID.

## 6. Observation Table

Record the actual PIDs and return values observed during execution.

| Execution step | Observed process | PID / return value |
|---|---|---|
| Step 1 (`getpid`) | Single process | `getpid()` returns the process's PID |
| Step 2 (`fork`) | Parent | `fork()` returns the child's PID |
| Step 2 (`fork`) | Child | `fork()` returns `0` |
| Step 3 (`wait`) | Parent | `wait(NULL)` returns the terminated child's PID |
| Step 3 (`wait`) | Child | Child terminates after completing its work |

PIDs are assigned by the operating system and will differ between runs.

## Result
The three C programs demonstrating process identification using `getpid()`, process creation using `fork()`, and parent-child synchronization using `wait()` were compiled and executed. The experiment demonstrated how Linux identifies processes, creates child processes, and coordinates process termination.

## Conclusion
This experiment demonstrates the basic process-management operations provided by Linux. The `getpid()` function identifies the currently running process, `fork()` creates a child process with a separate execution path, and `wait(NULL)` allows the parent to synchronize with the child's termination and obtain its PID.
