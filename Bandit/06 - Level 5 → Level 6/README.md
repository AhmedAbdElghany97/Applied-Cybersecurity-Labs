# Bandit - Write-Up
# Level 5 → Level 6

## Overview

Deep search for a file with a specific conditions!

**Lab Link**: https://overthewire.org/wargames/bandit/bandit6.html

---

## Step 1 — Understanding the Challenge

<img width="1489" height="392" alt="image" src="https://github.com/user-attachments/assets/82af5dfc-9239-4dcb-9af1-21771ee94249" />

```bash
ssh bandit5@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: 4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw

---

## Step 2 — Implementation

### Check a description of some usefull commands.
```bash
bandit5@bandit:~$ whatis find
find (1)             - search for files in a directory hierarchy
bandit5@bandit:~$ whatis du
du (1)               - estimate file space usage
```

Let's open inhere directory, then check its content.

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
bandit5@bandit:~/inhere$ find --help
Usage: find [-H] [-L] [-P] [-Olevel] [-D debugopts] [path...] [expression]
...
...
...
Tests (N can be +N or -N or N):
      -amin N -anewer FILE -atime N -cmin N -cnewer FILE -context CONTEXT
      -ctime N -empty -false -fstype TYPE -gid N -group NAME -ilname PATTERN
      -iname PATTERN -inum N -iwholename PATTERN -iregex PATTERN
      -links N -lname PATTERN -mmin N -mtime N -name PATTERN -newer FILE
      -nouser -nogroup -path PATTERN -perm [-/]MODE -regex PATTERN
      -readable -writable -executable
      -wholename PATTERN -size N[bcwkMG] -true -type [bcdpflsD] -uid N
      -used N -user NAME -xtype [bcdpfls]
...
...
...

Please see also the documentation at https://www.gnu.org/software/findutils/.
You can report (and track progress on fixing) bugs in the "find"
program via the GNU findutils bug-reporting page at
https://savannah.gnu.org/bugs/?group=findutils or, if
you have no web access, by sending email to <bug-findutils@gnu.org>.
```

What a huge library!

I can see that -type is intersted, it accepts "bcdpflsD" which we need to know their means.

Let's open the manual page and look for -type options

```bash
bandit5@bandit:~/inhere$ man find
...
...
...
-type c
              File is of type c:

              b      block (buffered) special

              c      character (unbuffered) special

              d      directory

              p      named pipe (FIFO)

              f      regular file

              l      symbolic  link; this is never true if the -L option or the -follow option
                     is in effect, unless the symbolic link is broken.  If you want to  search
                     for symbolic links when -L is in effect, use -xtype.

              s      socket

              D      door (Solaris)
...
...
...

```

Good! Let's list all files with type regular file f.

```bash
bandit5@bandit:~/inhere$ find . -type f
./maybehere07/.file1
./maybehere07/.file2
./maybehere07/.file3
...
...
...
./maybehere06/-file2
./maybehere06/spaces file1
./maybehere06/-file3
```

Perfect! Now wee need to filter with text format and display its sizes in byte.

Tricky, but i can do it! "du" command will handle.

```bash
bandit5@bandit:~/inhere$ du --help
...
...
...
  -0, --null            end each output line with NUL, not newline
  -a, --all             write counts for all files, not just directories
      --apparent-size   print apparent sizes rather than device usage; although
                          the apparent size is usually smaller, it may be
                          larger due to holes in ('sparse') files, internal
                          fragmentation, indirect blocks, and the like
  -B, --block-size=SIZE  scale sizes by SIZE before printing them; e.g.,
                           '-BM' prints sizes in units of 1,048,576 bytes;
                           see SIZE format below
  -b, --bytes           equivalent to '--apparent-size --block-size=1'
  -c, --total           produce a grand total
...
...
...
```

Interesting -a and -b right?


Let's use our amazing command du.

```bash
bandit5@bandit:~/inhere$ find . -type f | du -a -b
3065    ./maybehere07/.file1
1033    ./maybehere07/.file2
1997    ./maybehere07/.file3
...
...
...
3443    ./maybehere06/-file3
39304   ./maybehere06
802446  .
```

Finally, grep it with our targeted size!

```bash
bandit5@bandit:~/inhere$ find . -type f | du -a -b | grep 1033
1033    ./maybehere07/.file2
```

The covered man! ./maybehere07/.file2 is our target.

```bash
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        bandit5@bandit:~/inhere$ 
```

Great news! We found the password for the next level!

password: HWasnPhtq9AVKe0dmk45nxy20cvUa6EG

<img width="2065" height="1095" alt="image" src="https://github.com/user-attachments/assets/9a4fa908-227a-4f78-809b-450b537ed072" />

### Well Done! :)
