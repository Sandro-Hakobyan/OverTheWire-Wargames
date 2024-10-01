## Overview

In this level, the password for the next level is stored in a file named `readme` in the home directory. However, logging in via SSH immediately logs us out due to modifications in the `.bashrc` file. Our goal is to bypass this restriction and retrieve the password.

## A little bit of Theory

The `.bashrc` file is executed every time a new shell session starts. Since it has been modified to log us out immediately upon login, we need a way to access the `readme` file without starting an interactive shell.

One way to do this is by executing commands remotely using SSH. This allows us to run a command on the remote server without starting a full shell session.

## Solution

Instead of logging in interactively, we use SSH to execute commands remotely. First, we verify that `readme` exists:

```bash
$ ssh bandit18@bandit.labs.overthewire.org -p 2220 ls
```

### Output:

```
This is an OverTheWire game server. More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password: 
readme
```

Now, we read the file to retrieve the password:

```bash
$ ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
```

### Output:

```
This is an OverTheWire game server. More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password: 
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

**Password for Level 19:**  
`cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`

---

## Alternative Approach

Instead of executing a single command, we can try spawning a shell directly to bypass `.bashrc`:

```bash
$ ssh bandit18@bandit.labs.overthewire.org -p 2220 /bin/bash
```

or

```bash
$ ssh bandit18@bandit.labs.overthewire.org -p 2220 -t /bin/sh
```

The `-t` flag forces a pseudo-terminal, allowing us to execute multiple commands interactively.

---

## Official Level Link

[Bandit Level 19](https://overthewire.org/wargames/bandit/bandit19.html)

