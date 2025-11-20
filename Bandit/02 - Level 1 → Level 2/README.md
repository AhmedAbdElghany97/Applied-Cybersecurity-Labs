# Bandit - Write-Up
# Level 1 → Level 2

## Overview

Display the content that contains - character with its name.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit2.html

---

## Step 1 — Understanding the Challenge

<img width="1037" height="248" alt="image" src="https://github.com/user-attachments/assets/888e7930-fde8-4656-b29a-dace44a7a255" />

```bash
ssh bandit1@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If

---

## Step 2 — Implementation

### Check the file, then try to open it
```bash
bandit1@bandit:~$ ls
-
bandit1@bandit:~$ cat -


```

We can not use - as it the terminal expects an argument after it.

Try add it between double quotes.

```bash
bandit1@bandit:~$ ls
-
bandit1@bandit:~$ cat -


```

Still the same stuck, let's try adding it with path.

```bash
bandit1@bandit:~$ cat ./-
263JGJPfgU6LtdEvgfWU1XP5yac29mFx
```

Perfect! We found the password for the next level!

password: 263JGJPfgU6LtdEvgfWU1XP5yac29mFx

### Well Done! :)
