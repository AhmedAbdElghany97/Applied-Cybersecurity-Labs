# Bandit - Write-Up
# Level 18 → Level 19

## Overview

Non-interactive connection to a server to download a file.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit19.html

---

## Step 1 — Understanding the Challenge

<img width="1135" height="234" alt="image" src="https://github.com/user-attachments/assets/393b9ac2-e01d-49ae-bbca-48d6657b7fba" />

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220
```

Password obtained from previous level:

```markdown
x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

---

## Step 2 — Implementation

### Understanding the problem and discovering the solutions

When we are trying to connect to **bandit18**, it returns **Connection to bandit.labs.overthewire.org closed.**. This happens because someone modified **.bashrc** file to force close any ssh connection.

Our mission is to retrieve the file without interacting with a normal shell session.

### Looking for the command that we can use to transfer files remotely.

```bash
ahmed@ahmed-virtual-machine:~/Desktop/Practice/Bandit/18-19$ apropos transfer | grep transfer
...
...
...
sftp (1)             - OpenSSH secure file transfer
...
...
...
```


The command **sftp** looks interesting! Let's check its help page.

### Help page check

```bash
ahmed@ahmed-virtual-machine:~/Desktop/Practice/Bandit/18-19$ sftp -h
usage: sftp [-46AaCfNpqrv] [-B buffer_size] [-b batchfile] [-c cipher]
          [-D sftp_server_command] [-F ssh_config] [-i identity_file]
          [-J destination] [-l limit] [-o ssh_option] [-P port]
          [-R num_requests] [-S program] [-s subsystem | sftp_server]
          [-X sftp_option] destination


ahmed@ahmed-virtual-machine:~/Desktop/Practice/Bandit/18-19$ man sftp
SYNOPSIS
       sftp  [-46AaCfNpqrv]  [-B  buffer_size]  [-b  batchfile]  [-c cipher] [-D sftp_server_command] [-F ssh_config] [-i identity_file]
            [-J destination] [-l limit] [-o  ssh_option]  [-P  port]  [-R  num_requests]  [-S  program]  [-s  subsystem  |  sftp_server]
            [-X sftp_option] destination

```

### Using **sftp** command to download the target file

The file **readme** is stored in the homedirectory, which full path should be **/home/bandit18/readme**

Usage:

sftp [-P port] [source] [destination]

```bash
ahmed@ahmed-virtual-machine:~/Desktop/Practice/Bandit/18-19$ sftp -P 2220 bandit18@bandit.labs.overthewire.org:/home/bandit18/readme ./readme.txt
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-1
bandit18@bandit.labs.overthewire.org's password: 
Connected to bandit.labs.overthewire.org.
Fetching /home/bandit18/readme to ./readme.txt
readme                                                                                                    100%   33     0.1KB/s   00:00
```

We have downloaded the file! Let's check its content.

```bash
ahmed@ahmed-virtual-machine:~/Desktop/Practice/Bandit/18-19$ ls
readme.txt
ahmed@ahmed-virtual-machine:~/Desktop/Practice/Bandit/18-19$ cat readme.txt 
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

Perfect! We got the password.

Password:

```markdown
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

### Alternative fast solution | non-interactive SSH command execution

We can run a command directly using **ssh** command as follows.

<img width="744" height="254" alt="image" src="https://github.com/user-attachments/assets/85b2d533-9c52-437a-a8ba-eb0532bb68a1" />

Let's run it.

```bash
ahmed@ahmed-virtual-machine:~/Desktop/Practice/Bandit/18-19$ ssh bandit18@bandit.labs.overthewire.org -p 2220 -t 'cat /home/bandit18/readme;bash -l'
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-1
bandit18@bandit.labs.overthewire.org's password: 
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
Byebye !
Connection to bandit.labs.overthewire.org closed.
```

The password printed successfully in the terminal!

### Connecting to the target

```bash
ahmed@ahmed-virtual-machine:~/Desktop/Practice/Bandit/18-19$ ssh bandit19@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-1
bandit19@bandit.labs.overthewire.org's password: cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

<img width="1213" height="571" alt="image" src="https://github.com/user-attachments/assets/846dcf79-3fb7-4c9c-922e-219e23528fbf" />

We are in!

### Well Done! :)
