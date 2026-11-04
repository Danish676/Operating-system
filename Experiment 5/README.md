# Experiment 5: String Operations in Shell Script and C

## Aim
To write a Bash shell script and an equivalent C program to:
- Find the length of a string entered by the user.
- Extract a substring using a starting position and the number of characters.
- Compare the execution time of both programs using the Linux `time` utility.

## Overview
This experiment demonstrates two approaches to string manipulation:
- **Bash:** Uses parameter expansion to find string length and extract a substring.
- **C:** Uses a character array, a loop, and array indexing to calculate string length and print a substring.

The experiment also introduces the `time` utility and its `real`, `user`, and `sys` measurements.

## Part A: Bash Shell Script (`exp7.sh`)

### Program
```bash
#!/bin/bash
read -p "Enter a string: " str
echo "Length of string: ${#str}"
read -p "Enter starting position: " start
read -p "Enter number of characters: " length
echo "Substring: ${str:$start:$length}"
```

### Key Concepts
- `read -p`: displays a prompt and stores the user's input in a variable.
- `${#str}`: returns the length of the string stored in `str`.
- `${str:$start:$length}`: extracts the requested number of characters, beginning at the specified position.
- Bash string positions are zero-based, so the first character is at position `0`.

### Create and Run
```bash
nano exp7.sh
chmod +x exp7.sh
time ./exp7.sh
```

### Sample Output
```text
Enter a string: OperatingSystem
Length of string: 15
Enter starting position: 9
Enter number of characters: 6
Substring: System
```

## Part B: C Program (`exp7.c`)

### Program
```c
#include <stdio.h>

int main()
{
    char str[100];
    int start, length, i, count = 0;

    printf("Enter a string: ");
    fgets(str, 100, stdin);

    while (str[count] != '\0' && str[count] != '\n')
    {
        count++;
    }

    printf("Length of string: %d\n", count);

    printf("Enter starting position: ");
    scanf("%d", &start);

    printf("Enter number of characters: ");
    scanf("%d", &length);

    printf("Substring: ");

    for (i = start; i < start + length && i < count; i++)
    {
        printf("%c", str[i]);
    }

    printf("\n");
    return 0;
}
```

### Key Concepts
- `stdio.h`: provides standard input/output functions.
- `char str[100]`: declares a character array to store the input string.
- `fgets(str, 100, stdin)`: reads a line of input into the character array.
- `'\0'`: marks the end of a C string.
- `'\n'`: represents the newline character read by `fgets`.
- The `while` loop counts characters until it reaches the newline or null terminator.
- `scanf("%d", &start)` and `scanf("%d", &length)`: read the starting position and requested substring length.
- The `for` loop prints characters from the selected starting position. The condition `i < count` prevents reading beyond the counted string length.

### Compile and Run
```bash
gcc exp7.c -o exp7
./exp7
```

### Sample Output
```text
Enter a string: OperatingSystem
Length of string: 15
Enter starting position: 9
Enter number of characters: 6
Substring: System
```

## Comparing Execution Time

Use the `time` command to measure each program:

```bash
time ./exp7
time ./exp7.sh
```

The output generally contains three measurements:

| Field | Meaning |
|---|---|
| `real` | Total elapsed wall-clock time from start to finish |
| `user` | CPU time spent executing user-level code |
| `sys` | CPU time spent in kernel/system calls on behalf of the program |

Example:
```text
real    0m0.005s
user    0m0.001s
sys     0m0.004s
```

These values may vary between runs. CPU scheduling, system load, terminal input/output, cache state, and virtual-machine overhead can affect the measurements. Small variations are normal when running Ubuntu inside VirtualBox.

## Requirements
- Linux or Ubuntu environment.
- Bash shell.
- GCC compiler.
- Terminal and a text editor such as `nano`.
- VirtualBox if the experiment is being performed in a virtual machine.

## Learning Outcomes
After completing this experiment, a student should be able to:
- Find string length in Bash using `${#str}`.
- Extract a substring using Bash parameter expansion.
- Read and process strings in C using character arrays and `fgets()`.
- Calculate string length by traversing a character array.
- Extract and display a substring using a C `for` loop.
- Compile and execute a C program using GCC.
- Measure and interpret `real`, `user`, and `sys` execution times using `time`.

## Important Notes
- Bash substring positions start at `0`.
- The supplied C program stores input in a fixed-size array of 100 characters.
- The C loop stops at either the newline or null terminator.
- Run both programs with comparable input when comparing their timings.
- Timing results are not guaranteed to remain identical across repeated runs.
