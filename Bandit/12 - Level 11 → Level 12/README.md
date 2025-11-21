# Bandit - Write-Up
# Level 11 → Level 12

## Overview

Translate each characher to be rotated by x number of positions.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit12.html

---

## Step 1 — Understanding the Challenge

<img width="1739" height="253" alt="image" src="https://github.com/user-attachments/assets/1645c1a6-edbd-4bdf-8d5f-dc7397854750" />

```bash
ssh bandit11@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr

---

## Step 2 — Implementation

### Check for the data.txt in the main directory, then check its content.
```bash
bandit11@bandit:~$ ls
data.txt
bandit11@bandit:~$ cat data.txt 
Gur cnffjbeq vf 7k16JArUVv5LxVuJfsSVdbbtaHGlw9D4
```

Let's try to find a command that works with characters.

```bash
bandit11@bandit:~$ apropos characters
...
...
...
tr (1)               - translate or delete characters
...
...
...
```

"tr" command is interested, let's see its help page.

```bash
bandit11@bandit:~$ tr --help
Usage: tr [OPTION]... STRING1 [STRING2]
Translate, squeeze, and/or delete characters from standard input,
writing to standard output.  STRING1 and STRING2 specify arrays of
characters ARRAY1 and ARRAY2 that control the action.
...
...
...
```

It takes 2 strings and match each index of characters on both arrays, let's try.

```bash
bandit11@bandit:~$ tr '1-2' '3-4'
> 1234
> 3434
```

First array '1-2' will be like [1,2], second array will be like '3,4'. So, if changed each 1 by the 3, and each 2 by 4.

I will do the same with characters by shifting all character by 13 positions.

```bash
bandit11@bandit:~$ cat data.txt | tr 'A-Z' 'N-ZA-M' | tr 'a-z' 'n-za-m'
The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
```

Here 'A...Z' will be translated to 'N...ZA...M', and also with lowers cases 'a...z' will be translated to 'n...za...m'.

Existing! now we found the password for the next level!

password: 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4

<img width="2058" height="1092" alt="image" src="https://github.com/user-attachments/assets/5d60b447-57f7-4231-a431-447b8307f391" />

### Well Done! :)
