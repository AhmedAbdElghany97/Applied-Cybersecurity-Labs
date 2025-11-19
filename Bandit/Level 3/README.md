# Bandit - Write-Up
# Level 3

## Overview

Display the content of file that its name contains spaces and also start with -.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit3.html

---

## Step 1 — Understanding the Challenge

<img width="1442" height="253" alt="image" src="https://github.com/user-attachments/assets/7c05c3f7-53c7-40ae-981b-c0acaad0afd6" />

```bash
ssh bandit2@bandit.labs.overthewire.org -p 2220
```

Password captured last level: 263JGJPfgU6LtdEvgfWU1XP5yac29mFx

---

## Step 2 — Implementation

### Check the file, then using the same technique from last level, by add the path befor file name.
```bash
bandit2@bandit:~$ ls
--spaces in this filename--
bandit2@bandit:~$ cat ./--spaces in this filename--
cat: ./--spaces: No such file or directory
cat: in: No such file or directory
cat: this: No such file or directory
cat: filename--: No such file or directory
```

Not working as it has been splitted.

So, let's try add it between double quotes.

```bash
bandit2@bandit:~$ cat "./--spaces in this filename--"
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```

Wow! We found the password for the next level!

password: MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx

### Welldone! :)
