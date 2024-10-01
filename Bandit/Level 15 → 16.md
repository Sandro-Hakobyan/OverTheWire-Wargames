## Overview

In this level, the task is to retrieve the password for the next level by submitting the current level's password to port 30001 on localhost using SSL encryption.

---

## Challenge

- The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL encryption.

---
## A little bit of Theory

OpenSSL is a library for secure communication over networks. It implements the **Transport Layer Security** (TLS) and **Secure Sockets Layer** (SSL) cryptographic protocols that are, for example, used in HTTPS to secure the web traffic.

`openssl s_client` is the implementation of a simple client that connects to a server using SSL/TLS.

---
## Solution

1. **Login to the server as `bandit15`:**
    
    - SSH into the server using the password for Level 15:
    
    ```bash
    ssh bandit15@bandit.labs.overthewire.org -p 2220
    ```
    
2. **Use OpenSSL to connect to the service running on port 30001 on localhost**:
    
    - Since the task specifies using SSL encryption, we use the `openssl s_client` command to connect to localhost on port 30001.
    
    ```bash
    openssl s_client -connect localhost:30001
    ```
    
3. **Submit the password**:
    
    - When the connection is established, type the password for Level 15:
    
    ```
    8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
    ```
    
    - After submitting the password, the system will respond with the password for Level 16:
    
    ```
    Correct!
    kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
    ```
    
4. **The password for Level 16 is**: `kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`.
    

---

### Commands

1. `ssh bandit15@bandit.labs.overthewire.org -p 2220`
2. `openssl s_client -connect localhost:30001`
3. Type the password `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo` and hit Enter.

---

### Official Level Link

[Bandit Level 16](https://overthewire.org/wargames/bandit/bandit16.html)

