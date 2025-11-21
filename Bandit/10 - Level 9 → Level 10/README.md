# Bandit - Write-Up
# Level 9 → Level 10

## Overview

Search for human-readable text inside a file with binary type.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit10.html

---

## Step 1 — Understanding the Challenge

<img width="1615" height="259" alt="image" src="https://github.com/user-attachments/assets/2716053a-82c0-4ba8-938b-3eb5e785a003" />

```bash
ssh bandit9@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM

---

## Step 2 — Implementation

### Check for the data.txt in the main directory, then check its type.
```bash
bandit9@bandit:~$ ls
data.txt
bandit9@bandit:~$ file data.txt 
data.txt: data
```

The file type is data, so i need to find a way to get the text inside it.

As most command names are descriptive, let's try to search for a command with name "string".

```bash
apropos string
...
...
...
string_to_av_perm (3) - convert between SELinux class and permission values and stri...
string_to_security_class (3) - convert between SELinux class and permission values a...
strings (1)          - print the sequences of printable characters in files
strlen (3)           - calculate the length of a string
...
...
...
```

The description "print the sequences of printable characters in files" is interested, let's see the help page of "strings" command.

```bash
bandit9@bandit:~$ strings --help
Usage: strings [option(s)] [file(s)]
 Display printable strings in [file(s)] (stdin by default)
 The options are:
  -a - --all                Scan the entire file, not just the data section [default]
  -d --data                 Only scan the data sections in the file
  -f --print-file-name      Print the name of the file before each string
  -n <number>               Locate & print any sequence of at least <number>
    --bytes=<number>         displayable characters.  (The default is 4).
  -t --radix={o,d,x}        Print the location of the string in base 8, 10 or 16
  -w --include-all-whitespace Include all whitespace as valid string characters
  -o                        An alias for --radix=o
  -T --target=<BFDNAME>     Specify the binary file format
  -e --encoding={s,S,b,l,B,L} Select character size and endianness:
...
...
...
```

Let's try it without arguments.

```bash
bandit9@bandit:~$ strings data.txt 
Dm|H
d:Bj
pgM,
...
...
...
A6nOT
ICJ9
+w(Y
```

Sounds good!, let now grep the lines that contains "==".

```bash
bandit9@bandit:~$ strings data.txt | grep "=="
========== the
========== password
f\Z'========== is
========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```

Excellent! now we found the password for the next level!

password: FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey

<img width="2059" height="1088" alt="image" src="https://github.com/user-attachments/assets/d5a0260c-46a4-445f-ac81-84a88f22caf5" />

### Well Done! :)
