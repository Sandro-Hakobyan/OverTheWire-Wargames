
## Overview

[OverTheWire](https://overthewire.org/) provides free wargames to help users learn and practice security concepts. Bandit is the recommended starting point as it teaches essential Linux and Git commands, which are foundational for more advanced challenges.

This walkthrough covers **Level 0**, where the goal is to establish an SSH connection to the remote server.

---

## Challenge

Log into the Bandit server using SSH with the following credentials:

- **Host:** `bandit.labs.overthewire.org`
- **Port:** `2220`
- **Username:** `bandit0`
- **Password:** `bandit0`

---

## Approach

SSH (**Secure Shell Protocol**) is a common method to securely connect to remote systems. It encrypts the communication between client and server, ensuring confidentiality and integrity.

In Linux, SSH connections are made using the `ssh` command in the terminal. The basic syntax is:

```bash
ssh <username>@<server> -p <port>
```

For this challenge, we replace the placeholders with the provided credentials:

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

After executing this command, we enter the password `bandit0` when prompted.

---
### Key Commands

- `ssh bandit0@bandit.labs.overthewire.org -p 2220` → Connect to the Bandit server using SSH.
- `man ssh` → Display the SSH manual for more details.

Once successfully logged in, a welcome message confirms the connection, completing **Level 0**.

---
### Official Level Link

[Bandit Level 0](https://overthewire.org/wargames/bandit/bandit0.html)
