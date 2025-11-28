# Bandit - Write-Up
# Level 14 → Level 15

## Overview

Connect to localhost on a specific port.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit15.html

---

## Step 1 — Understanding the Challenge

<img width="1473" height="257" alt="image" src="https://github.com/user-attachments/assets/101b46cf-7d45-4d9e-92f3-d8f9324dfd31" />

```bash
ssh bandit14@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS

---

## Step 2 — Implementation

### Lookig for a command that can open a network connection.
```bash
bandit14@bandit:~$ apropos tcp
...
...
...
nc (1)               - arbitrary TCP and UDP connections and listens
nc.openbsd (1)       - arbitrary TCP and UDP connections and listens
nc_openbsd (1)       - arbitrary TCP and UDP connections and listens
netcat (1)           - arbitrary TCP and UDP connections and listens
...
...
...
```

These commands are interesting, they are all the same description, which can be used for our connection.

Let's check its help page to discover how to use it.

```bash
OpenBSD netcat (Debian patchlevel 1.226-1ubuntu2)
usage: nc [-46CDdFhklNnrStUuvZz] [-I length] [-i interval] [-M ttl]
          [-m minttl] [-O length] [-P proxy_username] [-p source_port]
          [-q seconds] [-s sourceaddr] [-T keyword] [-V rtable] [-W recvlimit]
          [-w timeout] [-X proxy_protocol] [-x proxy_address[:port]]
          [destination] [port]
```

We can simply use it by nc [destination] [port].

As per bandit, we should connect to localhost on port 30000, then submitting our current password.

```bash
bandit14@bandit:~$ nc localhost 30000
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

Yamy! We found the password for the next level!

password: 8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo

<img width="2390" height="1023" alt="image" src="https://github.com/user-attachments/assets/950aaa09-a068-4cbf-8987-e23c2f2f7a24" />

### Well Done! :)
