# Bandit - Write-Up
# Level 15 → Level 16

## Overview

Connect to localhost on a specific port that using TLS/SSL encryption.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit16.html

---

## Step 1 — Understanding the Challenge

<img width="1781" height="477" alt="image" src="https://github.com/user-attachments/assets/79060b65-70ee-4654-8f56-ec546385d288" />

```bash
ssh bandit15@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: 8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo

---

## Step 2 — Implementation

### Lookig for a command that can open a network connection.

AI inquuiry on ChatGPT.

<img width="1394" height="402" alt="image" src="https://github.com/user-attachments/assets/12535576-4f41-47c8-8624-d690eae061fe" />

Let's check its manual page also.

```bash
bandit15@bandit:~$ man openssl
...
...
...
s_client
           This implements a generic  SSL/TLS  client  which  can  establish  a
           transparent  connection  to  a  remote server speaking SSL/TLS. It's
           intended for testing purposes only  and  provides  only  rudimentary
           interface functionality but internally uses mostly all functionality
           of the OpenSSL ssl library.
...
...
...
```

Now, we can try the connection using openssl s_client [hostname]:[port], then submiting the password for the current level.

```bash
bandit15@bandit:~$ openssl s_client localhost:30001
CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 CN = SnakeOil
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN = SnakeOil
verify return:1
---
Certificate chain
 0 s:CN = SnakeOil
   i:CN = SnakeOil
   a:PKEY: rsaEncryption, 4096 (bit); sigalg: RSA-SHA256
   v:NotBefore: Jun 10 03:59:50 2024 GMT; NotAfter: Jun  8 03:59:50 2034 GMT
...
...
...
read R BLOCK
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx

closed
```

Great! We found the password for the next level!

password: kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx

<img width="2276" height="1027" alt="image" src="https://github.com/user-attachments/assets/2a8f956b-4d83-4bab-8962-9ae7ae0c766e" />

### Well Done! :)
