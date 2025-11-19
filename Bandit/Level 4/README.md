# Bandit - Write-Up
# Level 4

## Overview

Display the content of a hidden file located inside a specific directory.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit4.html

---

## Step 1 — Understanding the Challenge

<img width="929" height="239" alt="image" src="https://github.com/user-attachments/assets/7c77483d-06f9-486c-af2f-fe8cd8d7aabc" />

```bash
ssh bandit3@bandit.labs.overthewire.org -p 2220
```

Password captured last level: MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx

---

## Step 2 — Implementation

### Check the inhere directory, and open it to check its content.
```bash
bandit3@bandit:~$ ls
inhere
bandit3@bandit:~$ cd inhere/
bandit3@bandit:~/inhere$ ls
bandit3@bandit:~/inhere$ 
```

The directy appeared as empty with normal ls command.

Let's check the ls help page and find some useful arguments.

```bash
bandit3@bandit:~/inhere$ ls --help
Usage: ls [OPTION]... [FILE]...
List information about the FILEs (the current directory by default).
Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.

Mandatory arguments to long options are mandatory for short options too.
  -a, --all                  do not ignore entries starting with .
  -A, --almost-all           do not list implied . and ..
      --author               with -l, print the author of each file
  -b, --escape               print C-style escapes for nongraphic characters
      --block-size=SIZE      with -l, scale sizes by SIZE when printing them;
                             e.g., '--block-size=M'; see SIZE format below
...
...
...
...
```

The argument -a is interested!, let's try it.

```bash
bandit3@bandit:~/inhere$ ls -a
.  ..  ...Hiding-From-You
```

Good, wo found a file called ...Hiding-From-You, let's try to display it.

```bash
bandit3@bandit:~/inhere$ cat ...Hiding-From-You
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
```

Amazing! We found the password for the next level!

password: 2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ

### Welldone! :)
