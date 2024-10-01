## Overview

In this level, we are given a setuid binary that connects to `localhost` on a specified port. It reads a line of text from the connection and compares it to the password from the previous level (`bandit20`). If the password is correct, it sends back the password for the next level.

---
## A little bit of Theory

Netcat (`nc`) is a tool used for networking tasks, such as setting up a simple server or client connection.

- The `-l` flag makes netcat listen for incoming connections (server mode).
- The `-p` flag specifies the port to listen on.
- The `echo` command is used to send data into the connection.
- The `&` symbol runs a process in the background, allowing us to execute other commands simultaneously.

Setuid binaries were introduced in the previous level. They allow a binary to run with the privileges of its owner instead of the user executing it.

---
## Solution

First, we start a **netcat listener** on an arbitrary port (e.g., 1234) and send the password for `bandit20` using `echo`:

```bash
bandit20@bandit:~$ echo -n '0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO' | nc -l -p 1234 &
```

### Output:

```
[1] 24661
```

Next, we execute the **setuid binary** (`suconnect`) and make it connect to our listener on port `1234`:

```bash
bandit20@bandit:~$ ./suconnect 1234
```

### Output:

```
Read: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
Password matches, sending next password
EeoULMCra2q0dSkYj561DX7s1CpBuOBt
[1]+  Done  
```

The password for **Level 21** is:

`EeoULMCra2q0dSkYj561DX7s1CpBuOBt`

---

## Official Level Link

[Bandit Level 21](https://overthewire.org/wargames/bandit/bandit21.html)