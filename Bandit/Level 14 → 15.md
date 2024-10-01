
## Overview

In this level, we are tasked with submitting the password from Level 14 to a service running on port 30000 on localhost (127.0.0.1). This challenge introduces the use of `nc` (netcat) to connect to a service and send data over the network.

---
## Challenge:

- The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

---
## A little bit of Theory

Localhost is a hostname and its IP address is ‘127.0.0.1’. It is used to access network services on the same device that is running these services.

`nc` or `netcat` is a command that allows to read and write data over a network connection. It can be used for TCP and UDP connections. To connect to a service (as client) on a network the command syntax is the following: `nc <host> <port>`. To create a server that listens to incoming packets, the command looks like this: `nc -l <port>`.

---

## Solution

1. **Login to the server as `bandit14`:**
    
    - Use the private SSH key obtained in the previous level to log into the server:
    
    ```bash
    ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
    ```
    
2. **Connect to the service running on port 30000 on localhost** using `nc` (netcat):
    
    - The task is to submit the current password to port 30000 on localhost.
    - Use `nc` to connect to the service and send the password:
    
    ```bash
    nc localhost 30000
    ```
    
3. **Submit the password**:
    
    - When connected, type the password for Level 14:
    
    ```
    MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
    ```
    
    - After submitting the password, the system will respond with the password for Level 15:
    
    ```
    Correct!
    8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
    ```
    
4. **The password for Level 15 is**: `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`.
    

---

#### **Commands:**

1. `ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220`
2. `nc localhost 30000`
3. Type the password `MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS` and hit Enter.

---

### Official Level Link

[Bandit Level 15](https://overthewire.org/wargames/bandit/bandit15.html)
