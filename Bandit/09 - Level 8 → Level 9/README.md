# Bandit - Write-Up
# Level 8 → Level 9

## Overview

Search for the unique text in a specific file.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit9.html

---

## Step 1 — Understanding the Challenge

<img width="1265" height="246" alt="image" src="https://github.com/user-attachments/assets/70260979-74a1-40a3-a205-4b3b16fd1b9a" />

```bash
ssh bandit8@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc

---

## Step 2 — Implementation

### Check for the data.txt in the main directory.
```bash
bandit8@bandit:~$ ls
data.txt
```

There is a usefull command i noticed, "apropos" that we can search for specific command by description!

Let's try

```bash
bandit8@bandit:~$ apropos repeat
kbdrate (8)          - reset the keyboard repeat rate and delay time
uniq (1)             - report or omit repeated lines
yes (1)              - output a string repeatedly until killed
```

Good, we found "uniq" command that can help.

Let's see its help page.

```bash
bandit8@bandit:~$ uniq --help
Usage: uniq [OPTION]... [INPUT [OUTPUT]]
...
...
...
Mandatory arguments to long options are mandatory for short options too.
  -c, --count           prefix lines by the number of occurrences
  -d, --repeated        only print duplicate lines, one for each group
  -D                    print all duplicate lines
      --all-repeated[=METHOD]  like -D, but allow separating groups
                                 with an empty line;
                                 METHOD={none(default),prepend,separate}
  -f, --skip-fields=N   avoid comparing the first N fields
      --group[=METHOD]  show all items, separating groups with an empty line;
                          METHOD={separate(default),prepend,append,both}
  -i, --ignore-case     ignore differences in case when comparing
  -s, --skip-chars=N    avoid comparing the first N characters
  -u, --unique          only print unique lines
  -z, --zero-terminated     line delimiter is NUL, not newline
  -w, --check-chars=N   compare no more than N characters in lines
...
...
...
```

There is a good argument called -c that prefix each line with its count.

```bash
bandit8@bandit:~$ uniq data.txt -c
      1 UD0JGdEzC9MvLEFryrg13oTd5Hb07iWd
      1 7GmAoWty7FVrx69vVdHsWI3K7bhXB7ck
      1 XFZ2qtQ5m9FCzyje1e5fCvm2F1TeU5pJ
...
...
...
      1 a8EPDYuGBd3mvar2s0aF5VFdaAY0GbUP
      1 YGTz538EOOqQztF12JHBHXGPY7yY8jMp
      1 8MNnqNdEQCk107HPCHvn10HhkUF607u4
```

Let's try sort them first to make all duplicates under each other, then try the same uniq command.

```bash
bandit8@bandit:~$ sort data.txt | uniq -c
     10 037tttR5YpQVPyHblRMYKlykrsuwdK01
     10 0kUWzRKPfW3yanU9l7ShZYQQHiwh6kPR
     10 2E01uUQMBOGXxVooSFO0uezEWAfActMH
...
...
...
     10 YGTz538EOOqQztF12JHBHXGPY7yY8jMp
     10 ykv70dIkOGKzRnOThR9sCizEd6L3lU0Y
     10 z5EqE31wi09wq0OhIfDPgoo5Dw0Etq1J
```

We are close! Let's seach for line start with " 1 ".
```bash
bandit8@bandit:~$ sort data.txt | uniq -c | grep " 1 "
      1 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```

Super smooth! now we found the password for the next level!

password: 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM

### Well Done! :)
