# Bandit - Write-Up
# Level 5

## Overview

The password is stored in a human-readable file, so we will filter to get that file and capture the password included.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit5.html

---

## Step 1 — Understanding the Challenge

<img width="1828" height="250" alt="image" src="https://github.com/user-attachments/assets/148fb8d0-12bb-4477-8ca9-20611009a263" />

```bash
ssh bandit4@bandit.labs.overthewire.org -p 2220
```

Password captured last level: 2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ

---

## Step 2 — Implementation

### Check a description of some usefull  commands.
```bash
bandit4@bandit:~$ whatis file
FILE (3type)         - input/output stream
file (1)             - determine file type
bandit4@bandit:~$ whatis grep
grep (1)             - print lines that match patterns
```

Let's open inhere directory, then check its content.

```bash
bandit4@bandit:~$ ls
inhere
bandit4@bandit:~$ cd inhere/
bandit4@bandit:~/inhere$ ls
-file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07  -file08  -file09
```

Here we will use file command to return the files type, we will use * to match any file inside the current (./) directory.
```bash
bandit4@bandit:~/inhere$ file ./*
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
```

Mmm, here ./-file07 with format ASCII text, let's open it.

```bash
bandit4@bandit:~/inhere$ cat ./-file07
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
```

Okie Dokie! We found the password for the next level!

password: 4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw

### Welldone! :)
