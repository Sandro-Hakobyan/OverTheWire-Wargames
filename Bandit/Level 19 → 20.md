## Overview

In this level, we are given a special **setuid** binary called `bandit20-do`. Our goal is to execute this binary to gain access to the password for the next level, which is stored in `/etc/bandit_pass/bandit20`.

---
## A little bit of Theory

### Understanding File Permissions

Each file in Linux has three sets of permissions:

- **User (owner)**
- **Group**
- **Others**

Permissions are displayed as `rwxrwxrwx`, where:

- `r` = Read
- `w` = Write
- `x` = Execute
- `-` = No permission

The `ls -l` command displays file permissions, ownership, and other details.

### What is a SetUID Binary?

A **setuid** (Set User ID) binary allows a user to execute the file **with the permissions of the file owner**, rather than the user executing it. It is represented as an `s` in the user’s execute (`x`) position (`-rwsr-x---`).

To manually set the **setuid** permission on a file, we can use:

```bash
chmod u+s <filename>
```

---
## Solution

First, we list the files in our home directory:

```bash
bandit19@bandit:~$ ls -la
```

### Output:

```
total 28
drwxr-xr-x  2 root     root     4096 May  7  2020 .
drwxr-xr-x 41 root     root     4096 May  7  2020 ..
-rwsr-x---  1 bandit20 bandit19 7296 May  7  2020 bandit20-do
-rw-r--r--  1 root     root      220 May 15  2017 .bash_logout
-rw-r--r--  1 root     root     3526 May 15  2017 .bashrc
-rw-r--r--  1 root     root      675 May 15  2017 .profile
```

Here, the binary `bandit20-do` has **setuid** permissions (`-rwsr-x---`), is **owned by bandit20**, and can be executed by **bandit19** (our current user).

### Running the SetUID Binary

Running the binary without arguments provides usage instructions:

```bash
bandit19@bandit:~$ ./bandit20-do
```

### Output:

```
Run a command as another user.
  Example: ./bandit20-do id
```

Since it allows running commands as `bandit20`, we can use it to read the password file:

```bash
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
```

### Output:

```
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

**Password for Level 20:**  
`0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`

---

## Alternative Approach

We can also use this binary to confirm our effective user ID when executing it:

```bash
bandit19@bandit:~$ ./bandit20-do id
```

### Output:

```
uid=11020(bandit20) gid=11019(bandit19) groups=11019(bandit19)
```

This confirms that when we execute the binary, we are running commands **as bandit20**, allowing us to access files restricted to that user.

---

## Official Level Link

[Bandit Level 20](https://overthewire.org/wargames/bandit/bandit20.html)

