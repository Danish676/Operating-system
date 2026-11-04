# Experiment 3: Bash Scripting, Conditionals and Loops

## Aim

To understand the fundamentals of Bash scripting and practise using
command-line arguments, conditional statements, loops, command
substitution, and file-system operations in Linux.

## Overview

This experiment introduces Bash shell scripting as a way to automate
repetitive terminal tasks. It covers the basic structure of a shell
script, positional parameters, file and numeric tests, conditional
execution, loops, and filename manipulation.

## Topics Covered

-   Shell scripts and the Bash interpreter (`#!/bin/bash`)
-   Command-line arguments and positional parameters:
    -   `$0`: script name or path
    -   `$1`, `$2`, etc.: positional arguments
    -   `$#`: number of arguments
    -   `$@`: all arguments as separate items
-   Conditional statements: `if`, `if-else`, and `if-elif-else`
-   Numeric comparison operators: `-eq`, `-ne`, `-lt`, `-le`, `-gt`, and
    `-ge`
-   File tests: `-e`, `-f`, `-d`, `-s`, `-r`, `-w`, and `-x`
-   Loops: `for` and `while`
-   Command substitution using `$(command)`
-   Filename conversion using `tr`
-   Renaming files using `mv`

## Tasks

### Task 1: Check Whether an Argument Is a File or Directory

The script accepts one command-line argument and checks whether the
given path is: - A regular file - A directory - An existing path that is
neither a regular file nor a directory - A path that does not exist

It also checks that exactly one argument has been supplied.

Example:

``` bash
chmod +x check_type.sh
./check_type.sh /etc/passwd
./check_type.sh /var/log
```

### Task 2: Rename Files to Uppercase

The script accepts one or more filenames as command-line arguments. It
loops through the supplied filenames, converts each filename to
uppercase using `tr`, and renames it using `mv`.

Example:

``` bash
chmod +x rename.sh
touch notes.txt report.txt hello.txt
./rename.sh notes.txt report.txt hello.txt
```

The files are renamed to `NOTES.TXT`, `REPORT.TXT`, and `HELLO.TXT`. The
file contents remain unchanged.

## Creating and Running a Bash Script

1.  Create a script file:

    ``` bash
    nano script_name.sh
    ```

2.  Add the Bash code and save the file.

3.  Give the script execute permission:

    ``` bash
    chmod +x script_name.sh
    ```

4.  Run the script:

    ``` bash
    ./script_name.sh
    ```

5.  Pass command-line arguments when required:

    ``` bash
    ./script_name.sh argument1 argument2
    ```

## Requirements

-   A Linux environment or another environment with Bash available
-   A terminal
-   A text editor such as `nano`
-   Basic familiarity with Linux commands and file paths

## Learning Outcomes

After completing this experiment, a student should be able to: - Create
and execute basic Bash scripts. - Accept and access command-line
arguments. - Use conditional statements to make decisions. - Apply
numeric and file-test operators. - Use `for` and `while` loops to
automate repeated operations. - Capture command output using command
substitution. - Convert filenames to uppercase and rename files.

## Important Notes

-   The first line `#!/bin/bash` identifies Bash as the script
    interpreter.
-   Quote variables such as `"$1"` and `"$file"` to handle paths
    containing spaces.
-   The `"$@"` parameter preserves command-line arguments as separate
    items when quoted.
-   Use `chmod +x` before executing a script directly with `./`.
-   The uppercase-renaming task changes filenames only; it does not
    modify file contents.

