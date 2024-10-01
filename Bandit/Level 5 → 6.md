
## Overview

In this level, the task is to find a file that contains the password for the next level. The file must meet the following criteria:

- Human-readable
- 1033 bytes in size
- Not executable

The password is located in a file under the `inhere` directory, which contains several subdirectories and files.

---
## Challenge

The main challenge in this level is to identify the file that satisfies all of the given criteria among many files in the `inhere` directory and its subdirectories.

#### Understanding the Problem

The file we need to locate must be:

- **Human-readable**: The content must be readable text, likely ASCII.
- **1033 bytes**: The file's size must exactly match 1033 bytes.
- **Not executable**: The file must not have execution permissions.

Given the large number of files, it would be inefficient to manually check each one. Therefore, combining commands like `file`, `du`, and `grep` will be essential for narrowing down the possibilities.

---
## Key Commands

1. **List files in the `inhere` directory:**

```bash
ls -la
```

1. **Search for human-readable files using the `file` command and filter with `grep`:**

```bash
file */{.,}* | grep "ASCII text" | grep -v ', with very long lines'
```

1. **Check file sizes using `du -b` to filter by 1033 bytes:**

```bash
du -b -a | grep 1033
```

1. **Find non-executable files using `find`:**

```bash
find . ! -executable
```

1. **One efficient command that combines all criteria:**

```bash
find . -type f -size 1033c ! -executable -exec file '{}' \; | grep ASCII
```

This command finds files that meet all criteria (human-readable, 1033 bytes, non-executable).

```bash
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```

---
### Official Level Link

[Bandit Level 6](https://overthewire.org/wargames/bandit/bandit6.html)
