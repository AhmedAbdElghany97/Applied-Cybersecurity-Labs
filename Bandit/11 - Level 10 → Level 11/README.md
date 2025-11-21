# Bandit - Write-Up
# Level 10 → Level 11

## Overview

Search for human-readable text inside a file with binary type.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit11.html

---

## Step 1 — Understanding the Challenge

<img width="1167" height="246" alt="image" src="https://github.com/user-attachments/assets/8a415dae-9a3b-4286-8e69-959427b166bd" />

```bash
ssh bandit10@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey

---

## Step 2 — Implementation

### Check for the data.txt in the main directory, then check its content.
```bash
bandit10@bandit:~$ ls
data.txt
bandit10@bandit:~$ cat data.txt 
VGhlIHBhc3N3b3JkIGlzIGR0UjE3M2ZaS2IwUlJzREZTR3NnMlJXbnBOVmozcVJyCg==
```

This is a base64 encoded, let's try to find a command that should help on decode it.

```bash
bandit10@bandit:~$ apropos base64
base64 (1)           - base64 encode/decode data and print to standard output
```

We have our man! The "base64" command will help, let's check its help page.

```bash
bandit10@bandit:~$ base64 --help
Usage: base64 [OPTION]... [FILE]
Base64 encode or decode FILE, or standard input, to standard output.

With no FILE, or when FILE is -, read standard input.

Mandatory arguments to long options are mandatory for short options too.
  -d, --decode          decode data
  -i, --ignore-garbage  when decoding, ignore non-alphabet characters
  -w, --wrap=COLS       wrap encoded lines after COLS character (default 76).
                          Use 0 to disable line wrapping
      --help        display this help and exit
      --version     output version information and exit
...
...
...
```

Good! We have -d argument which will decode! 

```bash
bandit10@bandit:~$ base64 -d data.txt 
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```

Pravo! now we found the password for the next level!

password: dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr

<img width="2054" height="1083" alt="image" src="https://github.com/user-attachments/assets/e9502255-ac0e-4efb-82da-3e75ba9d9884" />

### Well Done! :)
