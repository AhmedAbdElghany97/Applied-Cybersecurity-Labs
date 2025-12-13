# Bandit - Write-Up
# Level 16 → Level 17

## Overview

Find the service listening on localhost within the given port range that speaks SSL/TLS, then use it to retrieve the SSH private key for bandit17.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit17.html

---

## Step 1 — Understanding the Challenge

<img width="1005" height="222" alt="image" src="https://github.com/user-attachments/assets/14642abc-42a9-4785-b9f0-993ef79e2213" />

```bash
ssh bandit16@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx

---

## Step 2 — Implementation

### Looking for the command that we can use to check all listening ports.

SuperUser inquuiry:

<img width="741" height="507" alt="image" src="https://github.com/user-attachments/assets/4cc03b98-8e39-4dc8-b930-0df3b46af69b" />

From this clear answer, it seems that net-tools is depreacated, let's use **ss** command.

Check the the meaning of each option from help page:

```bash
bandit16@bandit:~$ ss --help
Usage: ss [ OPTIONS ]
       ss [ OPTIONS ] [ FILTER ]
..
   -n, --numeric       don't resolve service names
...
...
   -t, --tcp           display only TCP sockets
...
   -u, --udp           display only UDP sockets
...
```

Let's run the command, and grep only ports start with :3.

```bash
bandit16@bandit:~$ ss -lntu | grep :3
udp   UNCONN 0      0           127.0.0.1:323        0.0.0.0:*          
udp   UNCONN 0      0               [::1]:323           [::]:*          
tcp   LISTEN 0      4096          0.0.0.0:31790      0.0.0.0:*          
tcp   LISTEN 0      5             0.0.0.0:30000      0.0.0.0:*          
tcp   LISTEN 0      4096          0.0.0.0:30001      0.0.0.0:*          
tcp   LISTEN 0      5             0.0.0.0:30002      0.0.0.0:*          
tcp   LISTEN 0      4096          0.0.0.0:31518      0.0.0.0:*          
tcp   LISTEN 0      64                  *:31960            *:*          
tcp   LISTEN 0      64                  *:31691            *:*          
tcp   LISTEN 0      64                  *:31046            *:* 
---
```

Found candidate ports within range: 31790, 31518, 31960, 31691 and 31046.

Let's try them all using openssl to check which one is using TLS/SSL encryption.

```bash
bandit16@bandit:~$ openssl s_client localhost:31790
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
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIFBzCCAu+gAwIBAgIUBLz7DBxA0IfojaL/WaJzE6Sbz7cwDQYJKoZIhvcNAQEL
BQAwEzERMA8GA1UEAwwIU25ha2VPaWwwHhcNMjQwNjEwMDM1OTUwWhcNMzQwNjA4
MDM1OTUwWjATMREwDwYDVQQDDAhTbmFrZU9pbDCCAiIwDQYJKoZIhvcNAQEBBQAD
ggIPADCCAgoCggIBANI+P5QXm9Bj21FIPsQqbqZRb5XmSZZJYaam7EIJ16Fxedf+
jXAv4d/FVqiEM4BuSNsNMeBMx2Gq0lAfN33h+RMTjRoMb8yBsZsC063MLfXCk4p+
09gtGP7BS6Iy5XdmfY/fPHvA3JDEScdlDDmd6Lsbdwhv93Q8M6POVO9sv4HuS4t/
jEjr+NhE+Bjr/wDbyg7GL71BP1WPZpQnRE4OzoSrt5+bZVLvODWUFwinB0fLaGRk
GmI0r5EUOUd7HpYyoIQbiNlePGfPpHRKnmdXTTEZEoxeWWAaM1VhPGqfrB/Pnca+
vAJX7iBOb3kHinmfVOScsG/YAUR94wSELeY+UlEWJaELVUntrJ5HeRDiTChiVQ++
wnnjNbepaW6shopybUF3XXfhIb4NvwLWpvoKFXVtcVjlOujF0snVvpE+MRT0wacy
tHtjZs7Ao7GYxDz6H8AdBLKJW67uQon37a4MI260ADFMS+2vEAbNSFP+f6ii5mrB
18cY64ZaF6oU8bjGK7BArDx56bRc3WFyuBIGWAFHEuB948BcshXY7baf5jjzPmgz
mq1zdRthQB31MOM2ii6vuTkheAvKfFf+llH4M9SnES4NSF2hj9NnHga9V08wfhYc
x0W6qu+S8HUdVF+V23yTvUNgz4Q+UoGs4sHSDEsIBFqNvInnpUmtNgcR2L5PAgMB
AAGjUzBRMB0GA1UdDgQWBBTPo8kfze4P9EgxNuyk7+xDGFtAYzAfBgNVHSMEGDAW
gBTPo8kfze4P9EgxNuyk7+xDGFtAYzAPBgNVHRMBAf8EBTADAQH/MA0GCSqGSIb3
DQEBCwUAA4ICAQAKHomtmcGqyiLnhziLe97Mq2+Sul5QgYVwfx/KYOXxv2T8ZmcR
Ae9XFhZT4jsAOUDK1OXx9aZgDGJHJLNEVTe9zWv1ONFfNxEBxQgP7hhmDBWdtj6d
taqEW/Jp06X+08BtnYK9NZsvDg2YRcvOHConeMjwvEL7tQK0m+GVyQfLYg6jnrhx
egH+abucTKxabFcWSE+Vk0uJYMqcbXvB4WNKz9vj4V5Hn7/DN4xIjFko+nREw6Oa
/AUFjNnO/FPjap+d68H1LdzMH3PSs+yjGid+6Zx9FCnt9qZydW13Miqg3nDnODXw
+Z682mQFjVlGPCA5ZOQbyMKY4tNazG2n8qy2famQT3+jF8Lb6a4NGbnpeWnLMkIu
jWLWIkA9MlbdNXuajiPNVyYIK9gdoBzbfaKwoOfSsLxEqlf8rio1GGcEV5Hlz5S2
txwI0xdW9MWeGWoiLbZSbRJH4TIBFFtoBG0LoEJi0C+UPwS8CDngJB4TyrZqEld3
rH87W+Et1t/Nepoc/Eoaux9PFp5VPXP+qwQGmhir/hv7OsgBhrkYuhkjxZ8+1uk7
tUWC/XM0mpLoxsq6vVl3AJaJe1ivdA9xLytsuG4iv02Juc593HXYR8yOpow0Eq2T
U5EyeuFg5RXYwAPi7ykw1PW7zAPL4MlonEVz+QXOSx6eyhimp1VZC11SCg==
-----END CERTIFICATE-----
...
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 103FFA76F8A6C0AFD1FD72A4A49B8742B8CEC05AD28EE852AC57CA978A362B96
    Session-ID-ctx: 
    Resumption PSK: 1C763B22C295BB1175812094E9130F5722B28B2DB1E0EC4AF237816A18B75318E1699C17FD8D385DB04FCE48B6F17C6F
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 300 (seconds)
    TLS session ticket:
    0000 - 87 e3 3a 29 d2 a1 0e aa-46 bc 76 20 63 d5 f5 2d   ..:)....F.v c..-
    0010 - 0a a9 32 34 d3 92 24 f8-2b fb f9 07 5a be 5a c1   ..24..$.+...Z.Z.
    0020 - f2 33 07 69 66 41 47 33-e3 39 66 7d 0c 23 75 da   .3.ifAG3.9f}.#u.
    0030 - 94 7e 22 e3 15 e1 9f 6c-0c 35 eb 16 d0 9d 5a e6   .~"....l.5....Z.
    0040 - b3 f7 52 5f fa 85 2d 6c-75 f9 62 f1 2f d9 1d 1c   ..R_..-lu.b./...
    0050 - f3 dd 29 83 82 98 42 7a-cb 1d 5b a6 97 a5 71 59   ..)...Bz..[...qY
    0060 - fe c1 ae d9 d7 74 53 ab-d6 20 e6 f2 8d c3 c9 a6   .....tS.. ......
    0070 - 5d b7 6d 04 c5 7d b4 0f-14 a6 1a ee ec ed a9 df   ].m..}..........
    0080 - aa 6a 50 05 c1 d8 cb 34-1a 7b 61 14 c1 d4 a0 26   .jP....4.{a....&
    0090 - 9f 7c c9 14 df ee e7 0c-ca 6d 40 ff 93 c6 ce 3d   .|.......m@....=
    00a0 - a2 0b ff ee 44 28 a5 ce-48 70 bf f1 93 a8 0a 11   ....D(..Hp......
    00b0 - a3 11 aa a8 95 3b f3 0e-58 ff 90 83 4d aa ec 14   .....;..X...M...
    00c0 - ba bc 4b 92 90 38 3d e2-fe 5b 0e 20 eb 1f ac 1a   ..K..8=..[. ....
    00d0 - 23 5d 10 bf a3 ed 1c 75-36 1d e9 c8 0e 94 76 73   #].....u6.....vs

    Start Time: 1765636425
    Timeout   : 7200 (sec)
    Verify return code: 18 (self-signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
KEYUPDATE
```

The **KEYUPDATE** output is generated by OpenSSL during the TLS handshake and does not represent actual server data.

The below screenshot is from openssl documentation, these options will help.

<img width="676" height="182" alt="image" src="https://github.com/user-attachments/assets/e9fec60f-aed9-4ed4-a0ad-a86030c3568b" />

-quiet option will inhibit printing of session and certificate information.

We need to use it with -connect option to connect to the server.

```bash
bandit16@bandit:~$ openssl s_client -connect localhost:31790 -quiet
Can't use SSL_get_servername
depth=0 CN = SnakeOil
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN = SnakeOil
verify return:1
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```

Lucky! it worked with our first port :)

So, Perfect! We found the password (key) for the next level!

password:
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----

Let's create our key.

```bash
┌──(kali㉿kali)-[~/Practice/BanditLabs]
└─$ nano bandit17_key.pem
-----BEGIN RSA PRIVATE KEY-----
...
...
...
-----END RSA PRIVATE KEY-----
┌──(kali㉿kali)-[~/Practice/BanditLabs]
└─$ chmod 600 bandit17_key.pem 
```

Now connecting to the next level.

```bash
┌──(kali㉿kali)-[~/Practice/BanditLabs]
└─$ ssh bandit17@bandit.labs.overthewire.org -p 2220 -i bandit17_key.pem
```

<img width="2268" height="1035" alt="image" src="https://github.com/user-attachments/assets/7867490f-2a4d-4c14-b07a-6f5fd6b9d249" />

### Well Done! :)
