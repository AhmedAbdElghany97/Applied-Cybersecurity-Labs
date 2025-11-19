# Bandit - Write-Up
# Level 0

## Overview
**Level: 0**

This lab is to connect to the Bandit server using SSH command.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit0.html

---

## Step 1 — Understanding the Challenge
<img width="2378" height="278" alt="image" src="https://github.com/user-attachments/assets/b0589745-f510-4fea-9a04-8235c2f23ff1" />

**Host:** bandit.labs.overthewire.org

**Port**: 2220

**Username**: bandit0

**Password**: bandit0

---

## Step 2 — Implementation

### SSH description
```bash
# SSH description
whatis ssh
```

```markdown
ssh (1)              - OpenSSH remote login client
```

### Help page for SSH

```bash
# Help page for SSH
ssh --help
```

```markdown
unknown option -- -
usage: ssh [-46AaCfGgKkMNnqsTtVvXxYy] [-B bind_interface] [-b bind_address]
           [-c cipher_spec] [-D [bind_address:]port] [-E log_file]
           [-e escape_char] [-F configfile] [-I pkcs11] [-i identity_file]
           [-J destination] [-L address] [-l login_name] [-m mac_spec]
           [-O ctl_cmd] [-o option] [-P tag] [-p port] [-R address]
           [-S ctl_path] [-W host:port] [-w local_tun[:remote_tun]]
           destination [command [argument ...]]
       ssh [-Q query_option]
```

### Now connecting to the server

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

<img width="1664" height="838" alt="image" src="https://github.com/user-attachments/assets/71d41218-0904-4c56-8c80-0537e199eacd" />

password: bandit0

<img width="1653" height="827" alt="server-connected" src="https://github.com/user-attachments/assets/281a62f6-fba3-4c24-b4c9-855f653650b4" />

### Welldone! :)
