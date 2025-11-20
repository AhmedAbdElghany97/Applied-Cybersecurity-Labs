# Bandit - Write-Up
# Level 0 → Level 1

## Overview

Locate for a specific file that contains the passoword and display it to us with next level.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit1.html

---

## Step 1 — Understanding the Challenge

<img width="2374" height="281" alt="image" src="https://github.com/user-attachments/assets/cfbabf39-6b51-4aee-9e5f-efed52d40963" />

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

password: bandit0

---

## Step 2 — Implementation

### Check the options that i can use.
```bash
bandit0@bandit:~$ whatis ls ; whatis cat
ls (1)               - list directory contents
cat (1)              - concatenate files and print on the standard output
```

### List the contect to find the file, then display its content.

```bash
bandit0@bandit:~$ ls
readme
```

```bash
bandit0@bandit:~$ cat readme 
Congratulations on your first steps into the bandit game!!
Please make sure you have read the rules at https://overthewire.org/rules/
If you are following a course, workshop, walkthrough or other educational activity,
please inform the instructor about the rules as well and encourage them to
contribute to the OverTheWire community so we can keep these games free!

The password you are looking for is: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
```

Good, now we found the password for the next level!

password: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If

### Well Done! :)
