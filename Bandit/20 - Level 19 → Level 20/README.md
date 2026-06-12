# Bandit - Write-Up
# Level 19 → Level 20

## Overview

Using the setuid binary to execute commands as other user.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit20.html

---

## Step 1 — Understanding the Challenge

<img width="1097" height="343" alt="image" src="https://github.com/user-attachments/assets/3092a5a4-55c4-44e3-a496-1e030bbcc6dc" />

```bash
ssh bandit19@bandit.labs.overthewire.org -p 2220
```

Password obtained from previous level:

```markdown
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

Understand setuid:

https://en.wikipedia.org/wiki/Setuid

---

## Step 2 — Implementation

### Understanding the problem and discovering the solutions

After connect, tried to list all files inside the main directory and found this red highlight! it means that this file has setuid that will allow us to run it in a permission of another user.

<img width="783" height="192" alt="image" src="https://github.com/user-attachments/assets/0c795a23-263b-40b5-a9a2-96c8d3f9d166" />

The owner is **bandit20**.

### Checking the functionality of the found script

```bash
bandit19@bandit:~$ ls -la
total 36
drwxr-xr-x   2 root     root      4096 Apr  3 15:17 .
drwxr-xr-x 150 root     root      4096 Apr  3 15:20 ..
-rwsr-x---   1 bandit20 bandit19 14888 Apr  3 15:17 bandit20-do
-rw-r--r--   1 root     root       220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root      3851 Apr  3 15:10 .bashrc
-rw-r--r--   1 root     root       807 Mar 31  2024 .profile
bandit19@bandit:~$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do whoami
bandit19@bandit:~$ ./bandit20-do whoami
bandit20
```

We can run the scirpt by bandit20 user permission! let's use this to find our password.

### Double confirm then finding the password

Let's create a test file to double confirm that we have the bandit20 permission.

```bash
bandit19@bandit:~$ ./bandit20-do touch /tmp/perm-check.txt; ./bandit20-do ls -la /tmp/perm-check.txt
-rw-rw-r-- 1 bandit20 bandit19 0 Jun 12 15:05 /tmp/perm-check.txt
```

Good! Let's now discover **/etc/bandit_pass** directory, and return only files with owner/group bandit20.

```bash
bandit19@bandit:~$ ./bandit20-do ls -la /etc/bandit_pass/ | grep bandit20
-r--------   1 bandit20 bandit20    33 Apr  3 15:17 bandit20
```

Our target is **/etc/bandit_pass/bandit20**, let's print the content.

```bash
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

The password is now ours!

### Connecting to the target

```bash
ahmed@Ahmed:~/write-ups$ ssh bandit20@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

backend: gibson-0
bandit20@bandit.labs.overthewire.org's password:

      ,----..            ,----,          .---.
     /   /   \         ,/   .`|         /. ./|
    /   .     :      ,`   .'  :     .--'.  ' ;
   .   /   ;.  \   ;    ;     /    /__./ \ : |
  .   ;   /  ` ; .'___,/    ,' .--'.  '   \' .
  ;   |  ; \ ; | |    :     | /___/ \ |    ' '
  |   :  | ; | ' ;    |.';  ; ;   \  \;      :
  .   |  ' ' ' : `----'  |  |  \   ;  `      |
  '   ;  \; /  |     '   :  ;   .   \    .\  ;
   \   \  ',  /      |   |  '    \   \   ' \ |
    ;   :    /       '   :  |     :   '  |--"
     \   \ .'        ;   |.'       \   \ ;
  www. `---` ver     '---' he       '---" ire.org


Welcome to OverTheWire!
...
...
bandit20@bandit:~$
```

<img width="1476" height="752" alt="image" src="https://github.com/user-attachments/assets/91955ae7-a92e-4359-9d11-1464b6e463bd" />

We are in!

### Well Done! :)
