# Shell Redirection and the `sed` Stream Editor

A beginner-friendly guide to shell output redirection and using `sed` to
display, delete, search, replace, and rearrange text in Linux.

## Topics Covered

-   Combining commands with `;` and `&&`
-   Redirecting command output to a file
-   Printing all, specific, or selected lines with `sed`
-   Deleting lines from command output or editing the original file
-   Replacing the first or all occurrences of text
-   Editing files in place and creating backups
-   Matching text with regular-expression symbols
-   Capturing groups and reordering words

## 1. Shell Redirection

Display the current date and save the list of logged-in users to a file:

``` bash
date; who > logged_users.txt
```

-   `date` displays the current date and time.
-   `;` runs the next command after the previous command finishes.
-   `who` lists logged-in users.
-   `>` redirects the output of `who` into `logged_users.txt`.

The `who` command may not be available or reliable in every environment.
Depending on the system, `w` or `whoami` may be useful alternatives.

## 2. What Is `sed`?

`sed` stands for **Stream Editor**. It reads input, applies the
requested text operation, and prints the result. It is commonly used to:

-   Print selected lines
-   Delete lines from output
-   Search for text
-   Replace text
-   Apply pattern-based transformations

Unlike an interactive editor, `sed` normally processes text without
opening the file for interactive editing.

## 3. Common `sed` Commands

Assume the input file is named `Names.txt`.

  -----------------------------------------------------------------------
  Task                                Command
  ----------------------------------- -----------------------------------
  Print the complete file             `sed -n 'p' Names.txt`

  Print line 2                        `sed -n '2p' Names.txt`

  Print lines 2 through 4             `sed -n '2,4p' Names.txt`

  Delete line 2 from displayed output `sed '2d' Names.txt`

  Delete the first line from          `sed '1d' Names.txt`
  displayed output                    

  Delete the last line from displayed `sed '$d' Names.txt`
  output                              

  Replace the first `John` on each    `sed 's/John/Amit/' Names.txt`
  line                                

  Replace every `John` on each line   `sed 's/John/Amit/g' Names.txt`

  Replace all spaces with underscores `sed 's/ /_/g' Names.txt`

  Replace only the first space on     `sed 's/ /_/' Names.txt`
  each line                           
  -----------------------------------------------------------------------

### Understanding `-n` and `p`

-   `-n` suppresses automatic printing.
-   `p` explicitly prints the selected line or lines.

For example, `sed -n '2p' Names.txt` prints only the second line.

### Deleting Lines

Without `-i`, a delete command changes only the output produced by
`sed`; it does not modify the original file.

``` bash
sed '2d' Names.txt
```

To permanently delete line 2 from the file:

``` bash
sed -i '2d' Names.txt
```

## 4. Substitution and In-Place Editing

The general substitution format is:

``` bash
sed 's/old_text/new_text/' filename
```

By default, only the first matching occurrence on each line is replaced.
Add `g` to replace every occurrence on each line:

``` bash
sed 's/John/Amit/g' Names.txt
```

To save the changes directly to the original file, use `-i`:

``` bash
sed -i 's/John/Amit/g' Names.txt
```

To create a backup before editing:

``` bash
sed -i.bak 's/John/Amit/g' Names.txt
```

This creates `Names.txt.bak` containing the original version.

## 5. Regular-Expression Symbols

  Symbol      Meaning
  ----------- ----------------------------------------------------
  `^`         Beginning of a line
  `$`         End of a line
  `.`         Any single character
  `*`         Zero or more occurrences of the preceding pattern
  `[^ ]`      Any character except a space
  `\(...\)`   Capturing group in basic `sed` regular expressions
  `\1`        Text captured by the first group
  `\2`        Text captured by the second group

Examples:

``` bash
# Replace John only at the beginning of a line
sed 's/^John/Amit/' Names.txt

# Replace John only at the end of a line
sed 's/John$/Amit/' Names.txt

# Match a three-character pattern ending in "at"
sed 's/.at/XXX/' file.txt
```

## 6. Capturing Groups

Capturing groups allow matched text to be reused in the replacement.

Replace the first word on each line with `WORD`:

``` bash
sed 's/[^ ]*/WORD/' Names.txt
```

Wrap the first word in square brackets:

``` bash
sed 's/\([^ ]*\)/[\1]/' Names.txt
```

Capture the first two words and join them with a hyphen:

``` bash
sed 's/\([^ ]*\) \([^ ]*\)/\1-\2/' Names.txt
```

In these examples, `\1` refers to the first captured group and `\2`
refers to the second.

## 7. Final Experiment: Swap the First Two Words

Suppose `Names.txt` contains:

``` text
John Doe
Alice Smith
Rahul Sharma
Aniket Singh
```

Run:

``` bash
sed 's/^\([^ ]*\) \([^ ]*\)/\2 \1/' Names.txt
```

Output:

``` text
Doe John
Smith Alice
Sharma Rahul
Singh Aniket
```

How it works:

1.  `^` starts the match at the beginning of each line.
2.  `\([^ ]*\)` captures the first word as Group 1.
3.  The literal space matches the separator.
4.  The second `\([^ ]*\)` captures the second word as Group 2.
5.  `\2 \1` writes the second word first, followed by the first word.

To modify the original file:

``` bash
sed -i 's/^\([^ ]*\) \([^ ]*\)/\2 \1/' Names.txt
```

To keep a backup:

``` bash
sed -i.bak 's/^\([^ ]*\) \([^ ]*\)/\2 \1/' Names.txt
```

## 8. Quick Reference

  Command or symbol   Meaning
  ------------------- ------------------------------------------
  `sed`               Stream Editor
  `-n`                Suppress automatic printing
  `p`                 Print
  `d`                 Delete from processed output
  `s`                 Substitute
  `g`                 Replace all matches on each line
  `-i`                Edit the original file in place
  `-i.bak`            Edit in place and create a `.bak` backup
  `^`                 Beginning of line
  `$`                 End of line
  `.`                 Any single character
  `*`                 Zero or more repetitions
  `[^ ]`              Any character except a space
  `\(...\)`           Capture a group
  `\1`, `\2`          Refer to captured groups

## Learning Outcome

After working through this guide, you should be able to use shell
redirection and basic `sed` commands to process text files, select or
remove lines, perform substitutions, use regular expressions, and
rearrange words using captured groups.

**Tip:** Test a command without `-i` first. When you are ready to change
the original file, consider using `-i.bak` so that you retain a backup.
