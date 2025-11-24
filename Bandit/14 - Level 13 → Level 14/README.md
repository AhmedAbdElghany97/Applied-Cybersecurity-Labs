# Bandit - Write-Up
# Level 13 → Level 14

## Overview

Work with a hexdump file that has been compressed multiple times.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit14.html

---

## Step 1 — Understanding the Challenge

<img width="2392" height="292" alt="image" src="https://github.com/user-attachments/assets/4d03301b-5fcd-4254-98a8-fdfaf1d3b416" />

```bash
ssh bandit13@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn

---

## Step 2 — Implementation

### Discover the file in the current directory, and check its content.
```bash
bandit13@bandit:~$ ls
sshkey.private
bandit13@bandit:~$ cat sshkey.private 
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAxkkOE83W2cOT7IWhFc9aPaaQmQDdgzuXCv+ppZHa++buSkN+
gg0tcr7Fw8NLGa5+Uzec2rEg0WmeevB13AIoYp0MZyETq46t+jk9puNwZwIt9XgB
ZufGtZEwWbFWw/vVLNwOXBe4UWStGRWzgPpEeSv5Tb1VjLZIBdGphTIK22Amz6Zb
ThMsiMnyJafEwJ/T8PQO3myS91vUHEuoOMAzoUID4kN0MEZ3+XahyK0HJVq68KsV
ObefXG1vvA3GAJ29kxJaqvRfgYnqZryWN7w3CHjNU4c/2Jkp+n8L0SnxaNA+WYA7
jiPyTF0is8uzMlYQ4l1Lzh/8/MpvhCQF8r22dwIDAQABAoIBAQC6dWBjhyEOzjeA
J3j/RWmap9M5zfJ/wb2bfidNpwbB8rsJ4sZIDZQ7XuIh4LfygoAQSS+bBw3RXvzE
pvJt3SmU8hIDuLsCjL1VnBY5pY7Bju8g8aR/3FyjyNAqx/TLfzlLYfOu7i9Jet67
xAh0tONG/u8FB5I3LAI2Vp6OviwvdWeC4nOxCthldpuPKNLA8rmMMVRTKQ+7T2VS
nXmwYckKUcUgzoVSpiNZaS0zUDypdpy2+tRH3MQa5kqN1YKjvF8RC47woOYCktsD
o3FFpGNFec9Taa3Msy+DfQQhHKZFKIL3bJDONtmrVvtYK40/yeU4aZ/HA2DQzwhe
ol1AfiEhAoGBAOnVjosBkm7sblK+n4IEwPxs8sOmhPnTDUy5WGrpSCrXOmsVIBUf
laL3ZGLx3xCIwtCnEucB9DvN2HZkupc/h6hTKUYLqXuyLD8njTrbRhLgbC9QrKrS
M1F2fSTxVqPtZDlDMwjNR04xHA/fKh8bXXyTMqOHNJTHHNhbh3McdURjAoGBANkU
1hqfnw7+aXncJ9bjysr1ZWbqOE5Nd8AFgfwaKuGTTVX2NsUQnCMWdOp+wFak40JH
PKWkJNdBG+ex0H9JNQsTK3X5PBMAS8AfX0GrKeuwKWA6erytVTqjOfLYcdp5+z9s
8DtVCxDuVsM+i4X8UqIGOlvGbtKEVokHPFXP1q/dAoGAcHg5YX7WEehCgCYTzpO+
xysX8ScM2qS6xuZ3MqUWAxUWkh7NGZvhe0sGy9iOdANzwKw7mUUFViaCMR/t54W1
GC83sOs3D7n5Mj8x3NdO8xFit7dT9a245TvaoYQ7KgmqpSg/ScKCw4c3eiLava+J
3btnJeSIU+8ZXq9XjPRpKwUCgYA7z6LiOQKxNeXH3qHXcnHok855maUj5fJNpPbY
iDkyZ8ySF8GlcFsky8Yw6fWCqfG3zDrohJ5l9JmEsBh7SadkwsZhvecQcS9t4vby
9/8X4jS0P8ibfcKS4nBP+dT81kkkg5Z5MohXBORA7VWx+ACohcDEkprsQ+w32xeD
qT1EvQKBgQDKm8ws2ByvSUVs9GjTilCajFqLJ0eVYzRPaY6f++Gv/UVfAPV4c+S0
kAWpXbv5tbkkzbS0eaLPTKgLzavXtQoTtKwrjpolHKIHUz6Wu+n4abfAIRFubOdN
/+aLoRQ0yBDRbdXMsZN/jvY44eM+xRLdRVyMmdPtP8belRi2E2aEzA==
-----END RSA PRIVATE KEY-----
```

Wow! There is an RSA private key here.

As per bandit, we can use it to login to bandit14 user.

Let's read the help page for SSH command to check how to use this identity for login.

```bash
bandit13@bandit:~$ ssh --help
...
...
...
           [-e escape_char] [-F configfile] [-I pkcs11] [-i identity_file]
...
...
...
```

W can use -i for our mission, let's try it from inside server.

```bash
bandit13@bandit:~$ ssh bandit14@bandit.labs.overthewire.org -p 2220 -i sshkey.private 
...
...
...
!!! You are trying to log into this SSH server with a password on port 2220 from localhost.
!!! Connecting from localhost is blocked to conserve resources.
!!! Please log out and log in again.

backend: gibson-0
Received disconnect from 127.0.0.1 port 2220:2: no authentication methods enabled
Disconnected from 127.0.0.1 port 2220
```

The message here means that we can not connect to the bandit server from localhost, let's try from the own device.

From my device, i will use nano command to create a file, then i will the paste the key inside it.

```bash
┌──(kali㉿kali)-[~/BanditLabs/bandit14]
└─$ nano bandit14_key    
                                                                                            
┌──(kali㉿kali)-[~/BanditLabs/bandit14]
└─$ ssh bandit14@bandit.labs.overthewire.org -p 2220 -i bandit14_key 
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-0
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

Super warning here! the file is unprotected, after some search i discovered that the key file must be protected by adding permission for only the owner.

We need a command for changing permissions.

```bash
┌──(kali㉿kali)-[~/BanditLabs/bandit14]
└─$ apropos permission      
...
chmod (2)            - change permissions of a file
...
...
...
```

Let's check its help page.

```bash
┌──(kali㉿kali)-[~/BanditLabs/bandit14]
└─$ chmod --help
Usage: chmod [OPTION]... MODE[,MODE]... FILE...
  or:  chmod [OPTION]... OCTAL-MODE FILE...
  or:  chmod [OPTION]... --reference=RFILE FILE...
...
...
...
```

Looking at the below table, let's provide the file a least permission to the owner, which will be read only, our syntax will be 400, ( 4,0,0 --> user,group,other).

<img width="346" height="305" alt="image" src="https://github.com/user-attachments/assets/246fa9ec-6a3f-4ce3-9635-af01f2393525" />

```bash
┌──(kali㉿kali)-[~/BanditLabs/bandit14]
└─$ chmod 400 bandit14_key
```

Now trying to connect.

<img width="2462" height="1286" alt="image" src="https://github.com/user-attachments/assets/64c6d086-b4f4-486d-b707-054f664d65e7" />


What a smooth connection!

Let's now get the password from tha path mentioned on bandit site.

```bash
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
```

Perfect! We found the password for the next level!

password: MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS

Last step is to try connecting using the password also.

<img width="2457" height="1279" alt="image" src="https://github.com/user-attachments/assets/32711bfa-06fc-4232-ac0b-fc2bf25837aa" />

### Well Done! :)
