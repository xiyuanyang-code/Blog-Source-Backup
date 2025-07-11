---
title: Bash_commands
date: 2025-02-17 13:07:33
index_img: /img/cover/bash.png
excerpt: This blog concisely introduces common Bash commands, including grep, awk and explains how to adhere to the core Linux philosophy "KISS principle" when automating file processing in Linux.
categories:
  - Efficient Tools
  - Missing Semesters
tags:
  - Bash
  - Linux
  - Finished
  - tutorial
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Bash Commands Memo

## Introduction

### KISS principle

When using Linux, one problem that may easily be ignored by greenhands is: **Why Linux**. For programmers, Linux has great advantages over Windows OS for its light, free and open-source characteristics. If you **use Linux the same way you use Windows**, you won't be able to harness the spirit and greatest strengths of Linux.

Therefore, for every Linux beginner, it is very important to **familiarize themselves with Linux/Unix philosophy and practice it in their usage**! I recommend this Blog[^1] (author: Yifeng Ruan) which clearly explains the philosophy of Unix.

In this Blog, it says:

{% note primary %}

The Wikipedia page lists several versions of the Unix philosophy, with different people offering their own summaries. Doug McIlroy, the inventor of the pipe command, summarized it in three principles, while Eric S. Raymond, in his book *The Art of Unix Programming*, expanded it to a total of **17 principles** (in both the English and Chinese editions).

However, I noticed that everyone agrees on one fundamental principle of the Unix philosophy: the "**Principle of Simplicity**"—solving problems in the simplest way possible. This is also known as the famous **KISS principle** (**Keep It Simple, Stupid**), which means "keep things simple and straightforward."

![From Yifeng Ruan's Blog](https://lh3.ggpht.com/_6p3hNkUNWrQ/SjpEiMoM3TI/AAAAAAAABdE/9lkeDQLzXUY/s800/bg2009061801.gif)

{% endnote %}

Specifically, what is the **Principle of Simplicity**? From my perspective, the Principle of Simplicity means **refining every operation to its utmost simplicity**, **focusing solely on** accomplishing a single, independent small task, and then efficiently combining these tasks to achieve more complex objectives.

For example, using **pipes**`|` to connect commands enables us to use several small commands and connect them together so as to finish customed and complex tasks. (If you don't know what pipe is, you can go through **Missing semester for Lec1 first**.) In contrast, **commands in powershell** don't seem to follow the Principle of Simplicity in some circumstances.

- **IF** you want to list all the processes named "Notepad" and stop them:

In Bash:

```bash
ps aux | grep [n]otepad | awk '{print $2}' | xargs kill
```

In PowerShell:

```powershell
Get-Process | Where-Object {$_.Name -eq "notepad"} | Stop-Process
```

- **IF** you want to count total lines in all `.log` files recursively:

In Bash:

```bash
find . -name "*.log" -exec cat {} + | wc -l  
```

In PowerShell:

```powershell
Get-ChildItem -Recurse -Filter *.log | ForEach-Object { Get-Content $_ } | Measure-Object -Line | Select-Object -ExpandProperty Lines  
```

- **IF** you want to list all files in a directory with size and last modified time:

In Bash:

```bash
ls -l --time-style=+"%Y-%m-%d %H:%M:%S"  
```

In PowerShell:

```powershell
Get-ChildItem | Select-Object Name, Length, LastWriteTime | Format-Table -AutoSize
```

> PowerShell is **object-oriented**, meaning it treats command outputs as objects with properties and methods, allowing for **more complex** and manipulations directly within the shell, though it may require more verbose code to access and manipulate these properties. In contrast, Bash is text-based, handling command outputs as plain text streams, which are then processed using text utilities like `awk` and `sed` for parsing and transformation. While Bash's text-based approach can be simpler for straightforward tasks, PowerShell's object-oriented nature provides richer functionality and easier handling of structured data, albeit with potentially increased complexity in scripting.

### How to learn Bash commands

Thus, it's of great importance to follow **KISS** rules while learning thousands of Bash commands. **All commands are used for efficiency and all commands must remain simple and "stupid" independently.**

In the following articles, I will continuously update my journey of learning Bash commands and share concise study notes for your reference. Several basic commands and commands that have few options like `ls` and `logout` won't be listed into the updating plan. This Blog only focuses on those commands that are simple but powerful.

### Recommended Tools

I strongly recommend **tldr**[^2] for learning Bash commands in a simple way instead of searching for horribly long `man` files tutorial.

You can go to the https://tldr.sh/ for searching. You can also install tldr locally in your Linux systems but it will be a little bit slow.

**Remember, keep it simple, stupid**.

# Commands

## Table of contents

- [Grep Commands](#grep-command)
- [Awk Commands](#awk-command)
- [Find Commands](#find-commands)


## `grep` command

`grep` is a powerful command-line tool in Unix/Linux used to search for specific patterns within files or input text. 

```bash
grep [options] pattern [file]
```

- **`pattern`**: The text or **regular expression** to search for.
- **`file`**: The file(s) to search within. If omitted, `grep` reads from standard input.

For example, you can using the `grep` command by using pipes.

```bash
cat /etc/ssh/sshd_config | grep Port
```

This command will first output the file contents of `sshd_config` while the content itself won't be printed directly into the screen. It will be transmitted as the input of the grep command.

### **Common Options**

- `-i`: Ignore case (case-insensitive search).

```bash
grep -i "hello" file.txt
```

- `-v`: Invert match (show lines that do **not** match the pattern).

```bash
grep -v "error" file.txt
```

- `-r` or `-R`: Recursively search directories.

```bash
grep -r "pattern" /path/to/dir
```

- `-n`: Show line numbers of matching lines.

```bash
grep -n "pattern" file.txt
```

- `-c`: Count the number of matching lines.

```bash
grep -c "pattern" file.txt
```

- `-l`: List filenames containing the pattern.

```bash
grep -l "pattern" *.txt
```

- `-w`: Match whole words only.

```bash
grep -w "word" file.txt
```

- `-A`, `-B`, `-C`: Show lines after, before, or around the match.

```bash
grep -A 2 "pattern" file.txt  # Show 2 lines after the match
grep -B 2 "pattern" file.txt  # Show 2 lines before the match
grep -C 2 "pattern" file.txt  # Show 2 lines before and after the match
```

For example, I have a directory (current) and exists files as below:

```bash
.
├── grep_test.txt
└── sub_dirc
    ├── modified_1.txt
    └── modified_2.txt

2 directories, 3 files

❯ cat grep_test.txt
Hello my name is Jack.
I am 18 year's old.
Do you like me?
I want to make friends with you.
Remember my name, my name is JACK!
HAahah
do you know my favourite motto?
Knowledge isn't free, you have to pay attention.

❯ cat ./sub_dirc/modified_1.txt
Hello my name is Henry.
I am 17 year's old.
Do you like me?
I want to make friends with you.
Remember my name, my name is Henry!
HAahah
do you know my favourite motto?
Nothing is possible

❯ cat ./sub_dirc/modified_2.txt
Hello my name is Kate.
I am 20 year's old.
Do you like us?
I want to make friends with you.
Remember my name, my name is KatE!
HAahah
do you know my favourite motto?
Open source is the best thing in the world!
```

Now I can use several commands as below to efficiently search specific information that I want!

I can use `-r` to search for the whole current directory.

```bash
grep -r "Hello" ./
```

```bash
./sub_dirc/modified_1.txt:Hello my name is Henry.
./sub_dirc/modified_2.txt:Hello my name is Kate.
./grep_test.txt:Hello my name is Jack.
```

</br>

```bash
grep -nri "Jack" ./
```

```bash
./grep_test.txt:1:Hello my name is Jack.
./grep_test.txt:5:Remember my name, my name is JACK!
```

</br>

```bash
grep -nrc "Hello" ./
```

```bash
./sub_dirc/modified_1.txt:1
./sub_dirc/modified_2.txt:1
./grep_test.txt:1
```

</br>

```bash
grep -nrv "Hello" ./
```

```bash
./sub_dirc/modified_1.txt:2:I am 17 year's old.
./sub_dirc/modified_1.txt:3:Do you like me?
./sub_dirc/modified_1.txt:4:I want to make friends with you.
./sub_dirc/modified_1.txt:5:Remember my name, my name is Henry!
./sub_dirc/modified_1.txt:6:HAahah
./sub_dirc/modified_1.txt:7:do you know my favourite motto?
./sub_dirc/modified_1.txt:8:Nothing is possible
./sub_dirc/modified_1.txt:9:
./sub_dirc/modified_2.txt:2:I am 20 year's old.
./sub_dirc/modified_2.txt:3:Do you like us?
./sub_dirc/modified_2.txt:4:I want to make friends with you.
./sub_dirc/modified_2.txt:5:Remember my name, my name is KatE!
./sub_dirc/modified_2.txt:6:HAahah
./sub_dirc/modified_2.txt:7:do you know my favourite motto?
./sub_dirc/modified_2.txt:8:Open source is the best thing in the world!
./sub_dirc/modified_2.txt:9:
./grep_test.txt:2:I am 18 year's old.
./grep_test.txt:3:Do you like me?
./grep_test.txt:4:I want to make friends with you.
./grep_test.txt:5:Remember my name, my name is JACK!
./grep_test.txt:6:HAahah
./grep_test.txt:7:do you know my favourite motto?
./grep_test.txt:8:Knowledge isn't free, you have to pay attention.
./grep_test.txt:9:
```

</br>

```bash
grep -ri -A 1 "motto" ./
```

```bash
./sub_dirc/modified_1.txt:do you know my favourite motto?
./sub_dirc/modified_1.txt-Nothing is possible
--
./sub_dirc/modified_2.txt:do you know my favourite motto?
./sub_dirc/modified_2.txt-Open source is the best thing in the world!
--
./grep_test.txt:do you know my favourite motto?
./grep_test.txt-Knowledge isn't free, you have to pay attention.
```

You can make the command line more complex to get more specific data!

```bash
 grep -ri -A 1 "motto" ./ | grep -v "motto"
```

```bash
./sub_dirc/modified_1.txt-Nothing is possible
--
./sub_dirc/modified_2.txt-Open source is the best thing in the world!
--
./grep_test.txt-Knowledge isn't free, you have to pay attention.
```

More specific:

```
grep -rih -A 1 "motto" ./ | grep -v "motto" | grep -v "^--$"
```

Using the `-h` options to forbid grep showing filenames while searching the directory recursively. Adding more pipes and using **Regex** can enable developers to make more specific pattern matching problems more freely.

```Bash
Nothing is possible
Open source is the best thing in the world!
Knowledge isn't free, you have to pay attention.
```

By using this command, you can **get all the mottos for all person's files**, which will significantly enhance one's working efficiency. For more options, you can typing `man grep` for advanced usage.

## `awk` command

AWK is a powerful **text-processing language designed for pattern scanning and processing**. AWK is particularly useful for manipulating structured data, such as log files, CSV files, or any text files with consistent formatting. It operates on a **line-by-line basis**.

> AWK is named after its creators—Alfred Aho, Peter Weinberger, and Brian Kernighan. 

AWK is a powerful command. It can also be seen as a programming language! An AWK program consists of a series of **patterns** and **actions**. The general syntax is:

```bash
pattern { action }
```

- **Pattern**: Specifies **when** the action should be executed (e.g., matching a specific line or condition).
- **Action**: Defines what to do when the pattern matches (e.g., print a field, perform a calculation).

If no pattern is provided, the action is applied to every line. If no action is provided, the default action is to **print the entire line**.  By default, AWK **splits each line into fields based on whitespace (spaces or tabs)**. Fields are accessed using `$1`, `$2`, `$3`, etc., where `$1` is the first field, `$2` is the second, and so on. The entire line is stored in `$0`.

### Common Options

- Printing Specific Fields

- Filtering Lines Based on a Condition

- Using Built-in Variables

- Changing the Field Separator
- Using BEGIN and END Blocks
- Using Regular Expression
- Conditional Statements
- Associative Arrays
- String Manipulation
- Custom Functions
- Output Redirection

Assuming I have a file like below:

```bash
ID      Name          Gender  Age  Class     Chinese  Math  English  Physics  Chemistry
2023001 John_Doe      Male    18   Class_1   85       92    88       90       87
2023002 Alice_Smith   Female  17   Class_2   78       85    80       82       79
2023003 Bob_Johnson   Male    19   Class_1   92       88    95       89       94
2023004 Emma_Wilson   Female  18   Class_3   65       72    68       70       62
2023005 Mike_Brown    Male    17   Class_2   80       76    85       78       82
2023006 Lily_Davis    Female  19   Class_3   88       90    92       87       91
2023007 Tom_Miller    Male    18   Class_1   73       68    75       70       65
```

- **Printing Specific Fields**

```bash
awk '{ print $1, $3 }' file.txt
```

---

- **Filtering Lines Based on a Condition**

```bash
awk '$3 > 25 { print $0 }' file.txt
```

The basic logic for AWK is **if-else** statement, it first scans the text and used the judgements for `if $3 > 25, then print $0`

- Using Built-in Variables

AWK provides several built-in variables:
- `NR`: Current line number.
- `NF`: Number of fields in the current line.
- `FS`: Field separator (default is whitespace).
- `OFS`: Output field separator (default is space).

```bash
awk '{ print NR, $NF }' file.txt
```

---

- **Changing the Field Separator**

Use the `FS` variable to specify a custom field separator, such as a comma for CSV files.

**Example**: Print the second field from a CSV file.
```bash
awk -F, '{ print $2 }' data.csv
```

**Input (`data.csv`)**:

```
John,Doe,30,Engineer
Jane,Smith,25,Designer
```

**Output**:
```
Doe
Smith
```

---

- **Using BEGIN and END Blocks**

The `BEGIN` block is executed before processing any input, and the `END` block is executed after all input is processed. You can use it as a programming language for complex tasks!

```bash
awk '
BEGIN { sum = 0 }
{ sum += $3 }
END { print "Total lines:", NR, "Sum of third field:", sum }
' file.txt
```

**Output**:
```
Total lines: 2 Sum of third field: 55
```

---

- Regular Expressions: AWK supports regular expressions for pattern matching. Use `~` to match and `!~` to negate a match.

**Example**: Print lines where the second field contains "Smith".

```bash
awk '$2 ~ /Smith/ { print $0 }' file.txt
```

---

- **Conditional Statements**

AWK supports `if-else` statements for more complex logic.

**Example**: Classify ages as "Young" or "Old".

```bash
awk '{
    if ($3 < 30)
        print $1, "is Young"
    else
        print $1, "is Old"
}' file.txt
```

---

- **Associative Arrays**

AWK supports **associative arrays (like dictionaries)** for storing and retrieving data.

**Example**: Count the frequency of each unique value in the third field.

```bash
awk '{ count[$3]++ }
END {
    for (age in count)
        print age, ":", count[age]
}' file.txt
```

---

- **String Manipulation**

AWK provides functions for string manipulation, such as `length`, `substr`, and `toupper`. Like the `<string>` toolbox in cpp!

**Example**: Print the length of the first field.

```bash
awk '{ print length($1) }' file.txt
```

---

- **Custom Functions**

You can define custom functions in AWK for reusable logic.

**Example**: Define a function to calculate the square of a number.
```bash
awk '
function square(x) {
    return x * x
}
{ print square($3) }
' file.txt
```

---

- **Output Redirection**

AWK allows you to redirect output to files.

**Example**: Write the first field to a new file.
```bash
awk '{ print $1 > "output.txt" }' file.txt
```

## `find` commands

### `find` command

`find` is a very powerful command-line tool used to search for files and directories in a directory tree. It supports a large number of parameters and options, allowing searches based on file name, type, size, time, permissions, and many other conditions. Below are some commonly used `find` parameters and options, categorized by functionality:

#### 1. **Basic Search**

- **`.`**: Start searching from the current directory.
- **`/path/to/dir`**: Start searching from the specified directory.

#### 2. **Search by File Name**

- **`-name "pattern"`**: Match file names (case-sensitive).
	- Example: `find . -name "*.txt"` finds all `.txt` files.
- **`-iname "pattern"`**: Match file names (case-insensitive).
	- Example: `find . -iname "readme*"` finds all `README`, `readme`, etc. files.
- **`-regex "pattern"`**: Match file names using regular expressions.
	- Example: `find . -regex ".*\.txt$"` finds all files ending with `.txt`.

#### 3. **Search by File Type**

- **`-type f`**: Find regular files.
- **`-type d`**: Find directories.
- **`-type l`**: Find symbolic links.
- **`-type s`**: Find socket files.
- **`-type p`**: Find named pipes (FIFO).
- **`-type c`**: Find character device files.
- **`-type b`**: Find block device files.

#### 4. **Search by File Size**

- **`-size +n`**: Find files larger than `n`.
- **`-size -n`**: Find files smaller than `n`.
- **`-size n`**: Find files exactly `n` in size.
	- Units can be:
		- `c`: Bytes (default).
		- `k`: Kilobytes.
		- `M`: Megabytes.
		- `G`: Gigabytes.
	- Example: `find . -size +100M` finds files larger than 100MB.

#### 5. **Search by Time**

- **`-mtime n`**: Find files modified `n` days ago.
	- `-mtime +n`: Files modified more than `n` days ago.
	- `-mtime -n`: Files modified within the last `n` days.
- **`-atime n`**: Find files accessed `n` days ago.
- **`-ctime n`**: Find files whose status (e.g., permissions or ownership) changed `n` days ago.
- **`-mmin n`**: Find files modified `n` minutes ago.
	- `-mmin +n`: Files modified more than `n` minutes ago.
	- `-mmin -n`: Files modified within the last `n` minutes.
- **`-amin n`**: Find files accessed `n` minutes ago.
- **`-cmin n`**: Find files whose status changed `n` minutes ago.

#### 6. **Search by Permissions**

- **`-perm mode`**: Find files with permissions exactly matching `mode`.
	- Example: `find . -perm 644` finds files with permissions `644`.
- **`-perm -mode`**: Find files with permissions including `mode`.
	- Example: `find . -perm -u=r` finds files readable by the user.
- **`-perm /mode`**: Find files with any of the `mode` bits set.

#### 7. **Search by User and Group**

- **`-user username`**: Find files owned by the specified user.
- **`-group groupname`**: Find files owned by the specified group.
- **`-uid uid`**: Find files owned by the specified user ID.
- **`-gid gid`**: Find files owned by the specified group ID.

#### 8. **Logical Operations**

- **`-and` or `-a`**: Logical AND (default).
- **`-or` or `-o`**: Logical OR.
- **`-not` or `!`**: Logical NOT.
- **`()`**: Group conditions.
	- Example: `find . \( -name "*.txt" -o -name "*.md" \)` finds `.txt` or `.md` files.

#### 9. **Execute Actions**

- **`-exec command {} \;`**: Execute a command on the found files.
	- Example: `find . -name "*.log" -exec rm {} \;` deletes all `.log` files.
- **`-exec command {} +`**: Pass multiple files to the command at once.
	- Example: `find . -name "*.txt" -exec cp {} /backup/ +` copies all `.txt` files to `/backup`.
- **`-ok command {} \;`**: Similar to `-exec`, but prompts for confirmation before executing the command.
- **`-delete`**: Delete the found files.
- **`-print`**: Print the path of the found files (default behavior).
- **`-ls`**: Display the found files in `ls -dils` format.

#### 10. **Other Common Options**

- **`-maxdepth n`**: Limit the maximum directory depth for the search.
	- Example: `find . -maxdepth 2 -name "*.txt"` searches for `.txt` files only in the current directory and its immediate subdirectories.
- **`-mindepth n`**: Limit the minimum directory depth for the search.
- **`-empty`**: Find empty files or directories.
- **`-readable`**: Find readable files.
- **`-writable`**: Find writable files.
- **`-executable`**: Find executable files.

#### 11. **Comprehensive Examples**

Find and delete all `.log` files in the current directory:

```bash
find . -name "*.log" -delete
```

Find files larger than 100MB in `/var/log` and display detailed information:

```bash
find /var/log -size +100M -ls
```

Find `.txt` files modified within the last 7 days in the current directory and copy them to `/backup`:

```bash
find . -name "*.txt" -mtime -7 -exec cp {} /backup/ \;
```

Find and delete all empty directories in the current directory:

```bash
find . -type d -empty -delete
```

Find files with permissions `644` in the current directory and change their permissions to `755`:

```bash
find . -type f -perm 644 -exec chmod 755 {} \;
```

## Bash Scripting

### `$` in Bash

{% note primary %}

In Bash, `$` has special meanings:

1. **Variable Reference**: Access variable values (`$var`).
2. **Special Variables**: 
	- `$0`: Script name.
	- `$1`-`$9`: Script arguments.
	- `$?`: Exit status of the last command.
	- `$$`: Current script's PID.
3. **Command Substitution**: Capture command output (`$(command)`).
4. **Arithmetic Operations**: Perform math (`$((expression))`).
5. **Environment Variables**: Access (`$HOME`, `$PATH`).

Here `$1` is the first argument to the script/function. Unlike other scripting languages, bash uses a variety of special variables to refer to arguments, error codes, and other relevant variables. Below is a list of some of them. A more comprehensive list can be found [here](https://tldp.org/LDP/abs/html/special-chars.html).

- `$0` - Name of the script
- `$1` to `$9` - Arguments to the script. `$1` is the first argument and so on.
- `$@` - All the arguments
- `$#` - Number of arguments
- `$?` - Return code of the previous command
	- If the previous command execute successfully, the arguments will probably return `0`.
	- If it fails, it will return none-0 value.
- `$$` - **Process identification number (PID)** for the current script
- `!!` - **Entire last command, including arguments**. A common pattern is to execute a command only for it to fail due to missing permissions; you can quickly re-execute the command with sudo by doing `sudo !!`
- `$_` - Last argument from the last command. If you are in an interactive shell, you can also quickly get this value by typing `Esc` followed by `.` or `Alt+.`

{% endnote %}

# References

[^1]:https://www.ruanyifeng.com/blog/2009/06/unix_philosophy.html
[^2]: https://github.com/tldr-pages/tldr
