# Experiment 4: User Session Tracking, Line Ranges & Text Processing

## Aim
To practise Linux shell scripting techniques for tracking a user's login duration, extracting selected line ranges from text files, and deleting lines containing a specified word from multiple files.

## Overview
This experiment covers three Bash scripting tasks involving user-session information and text processing. It uses commands such as `who`, `awk`, `date`, `sed`, and Bash positional parameters to automate common Linux operations.

## Tasks

### Task 1: Determine a User's Working Period
The script asks for a username and checks the output of `who` to find that user's current login date and time. If the user is logged in, it converts the login time and current time into Unix timestamps, subtracts them, and displays the elapsed time in hours and minutes.

**Main commands and concepts:**
- `read -p`: prompts for and reads the username.
- `who`: lists currently logged-in users.
- `awk`: finds the row matching the username and extracts the date and time.
- `$(...)`: captures command output through command substitution.
- `date -d ... +%s`: converts a supplied date and time into a Unix timestamp.
- `date +%s`: obtains the current Unix timestamp.
- `$((...))`: performs arithmetic in Bash.
- `-z`: checks whether a string is empty.

**Run:**
```bash
chmod +x working_time.sh
./working_time.sh
```

Enter the username when prompted. The script reports that the user is not currently logged in if no matching session is found; otherwise, it displays the login time and calculated duration.

### Task 2: Display Lines Between a Start and End Line Number
The script accepts three command-line arguments: a filename, a starting line number, and an ending line number. It uses `sed` to print only the requested range.

**Main concepts:**
- `$#`: number of command-line arguments.
- `$1`, `$2`, `$3`: filename, start line, and end line.
- `sed -n`: suppresses automatic printing.
- `${start},${end}p`: prints the specified inclusive line range.

**Run:**
```bash
chmod +x display_lines.sh
./display_lines.sh names.txt 3 6
```

This displays lines 3 through 6 of `names.txt`.

### Task 3: Delete Lines Containing a Specified Word from Multiple Files
The script accepts a word followed by one or more filenames. It stores the word, uses `shift` to remove the first argument from the positional-parameter list, and loops through the remaining filenames. For each file, `sed` deletes lines containing the specified word.

**Main concepts:**
- `$#`: checks that a word and at least one filename were supplied.
- `$1`: stores the word to search for.
- `shift`: removes the first positional argument, leaving the filenames in `$@`.
- `for ... in "$@"`: processes each filename separately.
- `sed -i`: edits each file in place.
- `/pattern/d`: deletes lines matching the pattern.

**Run:**
```bash
chmod +x delete_word.sh
./delete_word.sh Aniket names.txt students.txt teachers.txt
```

The matching lines are removed from each specified file.

## Requirements
- Linux system or another environment with Bash and the required Unix utilities.
- Terminal access.
- A text editor such as `nano`.
- Input text files for the line-range and deletion tasks.
- Appropriate permissions to read and, for in-place editing, modify the files.

## Key Concepts
| Command or syntax | Purpose |
|---|---|
| `who` | Displays currently logged-in users |
| `awk` | Searches and extracts fields from text |
| `date +%s` | Returns the current Unix timestamp |
| `$(command)` | Captures command output |
| `$((expression))` | Performs arithmetic |
| `sed -n` | Prints selected lines without default output |
| `sed -i` | Edits a file directly |
| `shift` | Removes the first positional parameter |
| `"$@"` | Expands all remaining arguments as separate items |
| `for ... do ... done` | Repeats commands for each item |

## Learning Outcomes
After completing this experiment, a student should be able to:
- Find a logged-in user's session information using `who` and `awk`.
- Explain Unix timestamps and calculate elapsed time.
- Use Bash variables, command substitution, and arithmetic expansion.
- Extract a selected range of lines from a text file using `sed`.
- Process multiple filenames using positional parameters and loops.
- Use `shift` to separate an initial argument from the remaining arguments.
- Delete matching lines from files using `sed`.

## Notes
- A user-session lookup depends on the session information reported by `who`.
- Unix timestamps count seconds from the Unix epoch: January 1, 1970, 00:00:00 UTC.
- The line-range task expects exactly three arguments.
- The deletion task expects a search word and at least one filename.
- `sed -i` modifies the original files. Keep backups if the original contents must be preserved.
