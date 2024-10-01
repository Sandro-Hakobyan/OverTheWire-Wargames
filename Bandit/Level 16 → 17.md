## Overview

In this level, we need to submit the current password to a listening service on a port between `31000` and `32000` on `localhost`. The correct port will return the next level’s SSH private key.

## Challenge

- Find open ports in the given range.
- Identify which services are running on those ports.
- Determine which service accepts the password and returns the private SSH key.

---
## A little bit of Theory

Port scanning is a method to find open ports on a host. A **port** can be seen as an address for a specific service. Every computer has ports with the numbers 0 to 65535. Some services have standard ports, like HTTP/80 or SSH/22. An **open port** means that the host offers a service to the network on this port.

[Nmap](https://nmap.org/) is a network scanner. It can do Host Discovery, Port Scanning, Version Detection (Service Detection) and a lot more. For this task, the `-p` flag is important. This flag lets us choose which ports to scan. By default, Nmap scans the top 1000 ports (not the first 1000 ports). Use `-p-` to scan all 65535 ports. The `-sV` flag lets us do a service/version detection scan. It is possible to make Nmap perform all possible scans with the `-A` flag this will take a while though. A full scan would have the following command: `nmap -p- -A <host>`, where `<host>` could be either an IP address or the name.

This level also uses SSL again, which was described in Level 16

---
## Solution

### Step 1: SSH into the Bandit16 account

```sh
ssh bandit16@bandit.labs.overthewire.org -p 2220
```

Password:

```sh
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

---

### Step 2: Scan ports from 31000 to 32000

We use `nmap` to find open ports and identify services.

```sh
nmap -sV localhost -p 31000-32000
```

#### Output:

```sh
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
```

- Only ports `31518` and `31790` use **SSL**.
- `31518` runs only the **echo** service, so we focus on `31790`.

---

### Step 3: Connect to Port 31790 with OpenSSL

```sh
openssl s_client -connect localhost:31790
```

When prompted, enter the **Bandit16 password**:

```sh
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

#### Output:

```sh
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
...
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
...
-----END RSA PRIVATE KEY-----
```

This is the **SSH private key** for `bandit17`.

---

### Step 4: Save the SSH Private Key

1. Copy the key into a file on your local machine:

```sh
nano sshkey17.private
```

1. Paste the key and save it (`CTRL + X`, then `Y`, then `Enter`).
    
3. Set the correct permissions:
    

```sh
chmod 600 sshkey17.private
```

---

### Step 5: SSH into Bandit17 using the Private Key

```sh
ssh -i sshkey17.private bandit17@bandit.labs.overthewire.org -p 2220
```

This logs us into `bandit17`, completing the level. 🎉

### Step 6:  Access the password

 After successfully logging in as `bandit17`, the password for Level 17 will be in the file `/etc/bandit_pass/bandit17.
 
Simply read the file to obtain the password:

```bash
   cat /etc/bandit_pass/bandit17
  ```

The password for the next level is `EReVavePLFHtFlFsjn3hyzMlvSuSAcRD`

---

## Official Level Link

🔗 [Bandit Level 17](https://overthewire.org/wargames/bandit/bandit17.html)
