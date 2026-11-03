# Linux System Administration & Process Management

A practical reference to essential Linux commands for directory and file
operations, input/output redirection, pipes, text editing, file
searching, and process management.

## Table of Contents

-   [1. Directory Operations](#1-directory-operations)
-   [2. File Operations](#2-file-operations)
-   [3. Input/Output Redirection](#3-inputoutput-redirection)
-   [4. Pipes and Text Processing](#4-pipes-and-text-processing)
-   [5. Finding Files](#5-finding-files)
-   [6. Text Editors](#6-text-editors)
-   [7. Linux Process Management](#7-linux-process-management)
-   [8. Quick Process-Management
    Summary](#8-quick-process-management-summary)

> **Note:** Examples use `/home/ubuntu` as the home directory. The `~`
> symbol represents the current user's home directory.

------------------------------------------------------------------------

## 1. Directory Operations

  ----------------------------------------------------------------------------------
  Command                 Purpose                 Example
  ----------------------- ----------------------- ----------------------------------
  `pwd`                   Displays the absolute   `pwd`
                          path of the current     
                          directory.              

  `cd DIR`                Changes the current     `cd /etc/netplan`
                          directory.              

  `mkdir DIR`             Creates a directory.    `mkdir ~/scripts`

  `rmdir DIR`             Removes an empty        `rmdir ~/Templates`
                          directory.              

  `chmod MODE DIR`        Changes directory       `chmod 755 ~/Documents/projects`
                          permissions.            
  ----------------------------------------------------------------------------------

### Useful `cd` shortcuts

``` bash
cd ..       # Move up one directory
cd -        # Return to the previous directory
cd ~        # Go to the home directory
cd          # Also go to the home directory
```

### Useful `mkdir` options

``` bash
mkdir -p ~/Documents/projects/app/src  # Create parent directories as needed
mkdir -v ~/Downloads/iso               # Display each created directory
mkdir -m 700 ~/.ssh                    # Set permissions during creation
```

### Useful `rmdir` options

``` bash
rmdir -p ~/Documents/projects/app/src  # Remove empty parent directories
rmdir -v ~/Downloads/iso               # Display removal confirmation
```

`rmdir` removes empty directories only. Use `rm -r` when a directory and
its contents must be removed.

------------------------------------------------------------------------

## 2. File Operations

### Listing files: `ls`

``` bash
ls -lah ~
```

  Option   Meaning
  -------- --------------------------------------------------------------
  `-l`     Long listing: permissions, owner, group, size, and timestamp
  `-a`     Include hidden files
  `-h`     Human-readable file sizes
  `-t`     Sort by modification time, newest first
  `-R`     List contents recursively

### Creating files and updating timestamps: `touch`

``` bash
touch ~/.bash_aliases
touch -c ~/.bashrc
touch -t 202601011200 ~/install.log
touch -d "2026-04-23" ~/release_notes.txt
```

-   `-c`: Do not create a file if it does not exist.
-   `-t`: Set a timestamp using `[[CC]YY]MMDDhhmm[.ss]`.
-   `-d`: Set a timestamp using a human-readable date or time.

### Copying files: `cp`

``` bash
cp /etc/skel/.bashrc ~/.bashrc.bak
cp -r /etc/netplan ~/backup_netplan
cp -i /etc/hosts ~/hosts
cp -p /etc/timezone ~/timezone_backup
```

-   `-r` / `-R`: Copy directories recursively.
-   `-i`: Ask before overwriting.
-   `-p`: Preserve file mode, ownership, and timestamps.

### Moving or renaming: `mv`

``` bash
mv ~/Desktop ~/Workspace
mv -i ~/test.conf /etc/netplan/
mv -n ~/.bashrc.bak ~/.bashrc
mv -v ~/Downloads/*.deb ~/Packages/
```

-   `-i`: Ask before overwriting.
-   `-n`: Do not overwrite an existing destination.
-   `-v`: Display moved items.

### Removing files: `rm`

``` bash
rm -i ~/.bash_history
rm -r ~/Downloads/temp_setup/
rm -f /tmp/lockfile
```

-   `-r`: Remove directories and their contents recursively.
-   `-f`: Force removal without prompts.
-   `-i`: Ask for confirmation.

> **Caution:** `rm` permanently deletes files. Check the path carefully
> before using `rm -r` or `rm -f`, especially with elevated privileges.

### Displaying file contents

``` bash
cat /etc/os-release
cat -n /etc/shells
cat -b /etc/issue
head -n 10 /etc/passwd
tail -n 25 /var/log/auth.log
```

-   `cat`: Display file contents.
-   `cat -n`: Number every output line.
-   `cat -b`: Number non-blank lines.
-   `head -n N`: Display the first `N` lines.
-   `tail -n N`: Display the last `N` lines.

------------------------------------------------------------------------

## 3. Input/Output Redirection

Linux processes commonly use three standard streams:

  File descriptor   Stream   Purpose
  ----------------- -------- -----------------
  `0`               stdin    Standard input
  `1`               stdout   Standard output
  `2`               stderr   Standard error

### Redirecting output

``` bash
cat data1 data2 > data3
```

Combines the contents of `data1` and `data2` and writes the result to
`data3`.

``` bash
echo "hello" > myfile
```

`>` overwrites the destination file.

``` bash
echo "buddy" >> myfile
```

`>>` appends output to the end of the file without removing existing
content.

### Redirecting errors

``` bash
cd /root/ 2> error.txt
```

Redirects standard error (file descriptor `2`) to `error.txt`.

Using only `>` redirects standard output, not error messages.

### Redirecting both output and errors

``` bash
command &> error.txt
```

Sends both standard output and standard error to the same file.

### Redirecting input

``` bash
cat < data1
```

Uses the contents of `data1` as standard input to `cat`.

------------------------------------------------------------------------

## 4. Pipes and Text Processing

A pipe (`|`) sends the standard output of one command to the standard
input of another command.

### Combine and sort files

``` bash
cat names.txt countries.txt | sort
```

Combines the files and sorts the resulting lines alphabetically.

### Remove duplicate lines

``` bash
cat names.txt | sort | uniq
```

`uniq` removes adjacent duplicate lines. Sorting first brings identical
lines together.

### Display a line range

``` bash
cat data.txt | head -n 37 | tail -n 7
```

Displays lines 31 through 37.

### Save and display output with `tee`

``` bash
ls | tee test.txt
```

Displays the output on the terminal and writes it to `test.txt` at the
same time.

### Convert input into command arguments with `xargs`

``` bash
cat file_names.txt | xargs touch
```

Reads names from the input and passes them as arguments to `touch`.

### Search text with `grep`

``` bash
grep "Doctor" users.csv
grep -i "error" logs.txt
grep -v "error" logs.txt
grep -c "Doctor" users.csv
grep -w "Carly" users.csv
grep -n "Carly" users.csv
grep "Doctor" users.csv > doctor_data
```

  Option   Meaning
  -------- ------------------------------
  `-i`     Ignore case
  `-v`     Show lines that do not match
  `-c`     Count matching lines
  `-w`     Match a whole word
  `-n`     Show line numbers

------------------------------------------------------------------------

## 5. Finding Files

The `find` command searches for files and directories using specified
conditions.

**Syntax**

``` bash
find [path] [expression]
```

If the path is omitted, the current directory is used. A dot (`.`)
represents the current directory.

### Common examples

``` bash
find . -size 4M
find . -name "a123"
find . -iname "a123"
find . -user root
find . -empty
```

  Expression   Purpose
  ------------ -----------------------------------------
  `-size`      Search by file size
  `-name`      Search by exact, case-sensitive name
  `-iname`     Search by name without case sensitivity
  `-user`      Search by file owner
  `-empty`     Find empty files and directories

Size suffixes include `k` (kilobytes), `M` (megabytes), and `G`
(gigabytes).

------------------------------------------------------------------------

## 6. Text Editors

### Vi / Vim

Vim is a modal terminal editor. Its main modes include Normal mode,
Insert mode, and Command-line mode.

``` bash
vim filename
```

  Key / Command   Action
  --------------- -------------------------------
  `i`             Enter Insert mode
  `Esc`           Return to Normal mode
  `:w`            Save
  `:q`            Quit
  `:wq`           Save and quit
  `:q!`           Quit without saving
  `dd`            Delete (cut) the current line
  `yy`            Copy the current line
  `p`             Paste below the cursor
  `u`             Undo
  `/pattern`      Search forward
  `n` / `N`       Next / previous search match

### Nano

Nano is a modeless terminal editor that displays shortcuts on the
screen.

``` bash
nano filename
```

  Shortcut     Action
  ------------ ------------------------------------------
  `Ctrl + O`   Save (Write Out); press Enter to confirm
  `Ctrl + X`   Exit
  `Ctrl + W`   Search
  `Ctrl + K`   Cut the current line
  `Ctrl + U`   Paste the cut text
  `Ctrl + _`   Jump to a line number

------------------------------------------------------------------------

## 7. Linux Process Management

Process-management commands help users inspect, control, schedule, and
monitor running programs.

### 7.1 Shell job control

  -----------------------------------------------------------------------
  Command                 Purpose                 Example
  ----------------------- ----------------------- -----------------------
  `jobs`                  List jobs managed by    `jobs`
                          the current shell.      

  `bg`                    Resume a stopped job in `bg` or `bg %2`
                          the background.         

  `fg`                    Bring a background or   `fg` or `fg %2`
                          stopped job to the      
                          foreground.             
  -----------------------------------------------------------------------

Example workflow:

``` bash
sleep 100
# Press Ctrl + Z to stop the job
jobs
bg
jobs
fg
```

-   A **Job ID** (such as `[1]`) is used by shell job-control commands.
-   A **PID** is the system-wide process identifier.
-   For example, `fg %1` refers to shell job 1.

### 7.2 Inspecting processes with `ps`

  Command              Purpose
  -------------------- ----------------------------------------------------
  `ps`                 Show processes associated with the current session
  `ps -e`              Show all processes on the system
  `ps -ef`             Show all processes in full-format detail
  `ps -p PID`          Show a specific process
  `ps -p PID -f`       Show a specific process in full format
  `ps -o pid,ni,cmd`   Select the PID, nice value, and command columns
  `ps aux`             Show processes with CPU and memory information

Important `ps -ef` columns:

-   `UID`: User who owns the process.
-   `PID`: Process ID.
-   `PPID`: Parent Process ID.
-   `C`: CPU-related scheduling/accounting field.
-   `STIME`: Process start time.
-   `TTY`: Associated terminal.
-   `TIME`: CPU time consumed.
-   `CMD`: Command used to start the process.

**Important distinction:**

-   `ps -ef` is useful for detailed process information and parent/child
    relationships.
-   `ps aux` is useful for checking CPU and memory usage.

### 7.3 Terminating and controlling processes

The `kill` command sends a signal to a process identified by its PID.

``` bash
kill 6721
kill -15 6721
kill -9 6721
kill -l
kill -STOP 7000
kill -CONT 7000
```

  Command / Signal   Meaning
  ------------------ --------------------------------------------------
  `kill PID`         Sends `SIGTERM` (signal 15) by default
  `kill -15 PID`     Requests graceful termination
  `kill -9 PID`      Sends `SIGKILL` for immediate forced termination
  `kill -l`          Lists available signals
  `kill -STOP PID`   Stops/suspends a process
  `kill -CONT PID`   Continues a stopped process

**SIGTERM vs SIGKILL**

-   `SIGTERM` asks a process to terminate normally, giving it an
    opportunity to save data and clean up.
-   `SIGKILL` forces termination. It cannot be caught, blocked, or
    ignored, and does not give the application an opportunity to clean
    up.

Recommended approach: try `kill PID` first. Use `kill -9 PID` only if
the process does not terminate normally.

Other signal examples:

  Signal        Number Meaning
  ----------- -------- ------------------------------
  `SIGHUP`           1 Hangup
  `SIGINT`           2 Interrupt
  `SIGKILL`          9 Force termination
  `SIGTERM`         15 Graceful termination request
  `SIGCONT`         18 Continue
  `SIGSTOP`         19 Stop

### 7.4 Process priority: `nice` and `renice`

The nice value influences a process's CPU scheduling priority.

-   Range: `-20` (more favorable scheduling) to `0` (default) to `+19`
    (less favorable scheduling).
-   A lower nice value means more favorable scheduling.
-   A higher nice value means less favorable scheduling.
-   `nice` sets the value when starting a process.
-   `renice` changes the value of an existing process.

``` bash
nice -n 10 sleep 300 &
ps -o pid,ni,cmd

renice 10 -p PID
```

A nice value of `10` does **not** mean that a process uses 10% CPU. It
changes its scheduling priority weight.

### 7.5 Live process monitoring with `top`

``` bash
top
```

`top` provides a continuously updating view of processes and system
resource usage. Unlike `ps aux`, which provides a snapshot, `top`
updates while it runs.

Useful interactive keys inside `top`:

  Key   Action
  ----- -----------------------------------------------
  `q`   Quit
  `k`   Send a signal to a process
  `c`   Toggle short command name / full command line
  `u`   Filter by username
  `f`   Manage displayed fields
  `z`   Toggle color display
  `b`   Toggle highlighting of running processes

------------------------------------------------------------------------

## 8. Quick Process-Management Summary

  Task                                     Command
  ---------------------------------------- ------------------
  View jobs in the current shell           `jobs`
  Resume a stopped job in the background   `bg`
  Bring a job to the foreground            `fg`
  View current-session processes           `ps`
  View all processes                       `ps -e`
  View detailed process information        `ps -ef`
  Inspect one process                      `ps -p PID`
  View CPU and memory usage                `ps aux`
  Request graceful termination             `kill PID`
  Force termination                        `kill -9 PID`
  Stop a process                           `kill -STOP PID`
  Continue a stopped process               `kill -CONT PID`
  List signals                             `kill -l`
  Set priority at process start            `nice`
  Change priority of a running process     `renice`
  Monitor processes live                   `top`

------------------------------------------------------------------------

## Learning Outcomes

After working through these notes, you should be able to:

1.  Navigate and manage directories in Linux.
2.  Create, copy, move, inspect, and remove files.
3.  Redirect standard input, output, and error streams.
4.  Combine commands using pipes and process text with tools such as
    `grep`, `sort`, and `uniq`.
5.  Search for files using `find`.
6.  Perform basic editing with Vim and Nano.
7.  Inspect processes, manage shell jobs, send process signals, adjust
    scheduling priority, and monitor system activity.

## Safety Reminders

-   Verify paths before using deletion commands.
-   Prefer `kill` / `SIGTERM` before using `kill -9`.
-   Be careful when sending `SIGSTOP` to a shell or terminal-related
    process, because it can make the terminal appear unresponsive.
-   Use `sudo` only when elevated privileges are required.

