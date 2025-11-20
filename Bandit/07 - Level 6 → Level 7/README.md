# Bandit - Write-Up
# Level 6 → Level 7

## Overview

Deep search in the whole server, with a condition related to users and groups.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit7.html

---

## Step 1 — Understanding the Challenge

<img width="1244" height="393" alt="image" src="https://github.com/user-attachments/assets/7be3e1aa-635a-4314-b5bd-b4795e6a98c2" />

```bash
ssh bandit6@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: HWasnPhtq9AVKe0dmk45nxy20cvUa6EG

---

## Step 2 — Implementation

### Check some usefull arguments with "find" command.
```bash
bandit6@bandit:~$ find --help | grep "\-user"
      -used N -user NAME -xtype [bcdpfls]
bandit6@bandit:~$ find --help | grep "\-group"
      -ctime N -empty -false -fstype TYPE -gid N -group NAME -ilname PATTERN
```

We can use -user and -group to determine a specific user and group.

```bash
bandit5@bandit:~$ ls
inhere
bandit5@bandit:~$ cd inhere/
bandit5@bandit:~/inhere$ ls
maybehere00  maybehere03  maybehere06  maybehere09  maybehere12  maybehere15  maybehere18
maybehere01  maybehere04  maybehere07  maybehere10  maybehere13  maybehere16  maybehere19
maybehere02  maybehere05  maybehere08  maybehere11  maybehere14  maybehere17
```

There are multible directories, so we need to list all files inside these directories.

Let's check the help page for find command

```bash
bandit6@bandit:~$ find / -user bandit7 -group bandit6
find: ‘/proc/tty/driver’: Permission denied
find: ‘/proc/1/task/1/fd’: Permission denied
find: ‘/proc/1/task/1/fdinfo’: Permission denied
...
...
...
find: ‘/manpage/manpage3-pw’: Permission denied
find: ‘/dev/mqueue’: Permission denied
find: ‘/dev/shm’: Permission denied
```

Good, but we are getting alot of permission denied, we can ignore it by output the error in a seprate null place.

```bash
bandit6@bandit:~$ find / -user bandit7 -group bandit6 2>/tmp/null
/var/lib/dpkg/info/bandit7.password
```

Great! We found a file called /var/lib/dpkg/info/bandit7.password, let's open it.

```bash
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
```

So, Genius! We found the password for the next level!

password: morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj

<img width="2073" height="1092" alt="image" src="https://github.com/user-attachments/assets/8c54b94c-0460-4c0a-b6df-f0e6683c6c1b" />

### Well Done! :)
