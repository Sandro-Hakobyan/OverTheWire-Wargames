
## Overview

In this level, the task is to find a specific file on the server with the following properties:

- Owned by user `bandit7`
- Owned by group `bandit6`
- 33 bytes in size

---
## Challenge

We need to find a file with specific properties: ownership by `bandit7` and `bandit6`, and a size of 33 bytes. The file is located somewhere on the server, but we need to locate it using the proper Linux commands.

#### Understanding the Problem

The `find` command in Linux is an efficient way to search for files based on various criteria such as ownership, size, and file type. In this case, we can use the following flags with the `find` command:

- `-type f` to search for regular files.
- `-user bandit7` to find files owned by the `bandit7` user.
- `-group bandit6` to find files owned by the `bandit6` group.
- `-size 33c` to find files that are exactly 33 bytes in size.

When running the command, we may encounter "Permission denied" errors for files we cannot access. To suppress these errors, we can redirect them to `/dev/null`.

---
## Solution

1. Run the `find` command from the root directory to search the entire file system:

```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

1. This command finds the file `/var/lib/dpkg/info/bandit7.password`.
    
3. We can now read the file to get the password:
    

```bash
cat /var/lib/dpkg/info/bandit7.password
```

The contents of the file reveal the next password:

```bash
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
```

---
### Official Level Link

[Bandit Level 7](https://overthewire.org/wargames/bandit/bandit7.html)
