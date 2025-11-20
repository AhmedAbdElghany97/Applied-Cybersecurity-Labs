# Bandit - Write-Up
# Level 7 → Level 8

## Overview

Deep search in the whole server, with a condition related to users and groups.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit8.html

---

## Step 1 — Understanding the Challenge

<img width="1027" height="250" alt="image" src="https://github.com/user-attachments/assets/3ef03584-24b2-49a8-883f-a52d0489ed89" />

```bash
ssh bandit7@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj

---

## Step 2 — Implementation

### Check for the data.txt in the main directory.
```bash
bandit7@bandit:~$ ls
data.txt
```

We can search for the word millionth using "grep" as we used on the past levels.

```bash
bandit7@bandit:~$ cat data.txt | grep "millionth"
millionth       dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```

Easy! Right? We found the password for the next level!

password: dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc

### Well Done! :)
