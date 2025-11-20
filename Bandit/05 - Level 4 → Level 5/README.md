# Bandit - Write-Up
# Level 4 → Level 5

## Overview

Search for a file that is a human-readable.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit5.html

---

## Step 1 — Understanding the Challenge

<img width="1832" height="250" alt="image" src="https://github.com/user-attachments/assets/ad1f5390-b332-4b3e-9d63-f0ddfc3a1259" />

```bash
ssh bandit4@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: 2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ

---

## Step 2 — Implementation

### Check the inhere directory, and open it to check its content.
```bash
bandit4@bandit:~$ ls
inhere
bandit4@bandit:~$ cd inhere/
bandit4@bandit:~/inhere$ ls
-file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07  -file08  -file09
```

There are multible files, but we do not know which one is a human-readable.

Let's see the "file" command usage.

```bash
bandit4@bandit:~$ whatis file
FILE (3type)         - input/output stream
file (1)             - determine file type
```

Gooe, now we use it inside inhere directory, i will use ./ as all files starts with "-", also will use * to get all files.

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

Very good! Here we have the ./-file07 file with format ASCII text, let's display it.

```bash
bandit4@bandit:~/inhere$ cat ./-file07
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
```

Brilliant! We found the password for the next level!

password: 4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw

### Well Done! :)
