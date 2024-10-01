
## Overview

In this level, the task is to find the password for the next level, which is stored in a hidden file located in the `inhere` directory. Hidden files in Linux start with a dot (`.`), and the task requires identifying and reading this file.

---
## Challenge

We are given the task to locate the password in a hidden file within the `inhere` directory. The hidden file is not shown by default when listing the files, so we must ensure we list hidden files and locate the correct one.

### Understanding the Problem

In Linux, hidden files begin with a dot (`.`), and the `ls` command will not show them unless you use the `-a` option. The hidden file containing the password is named `.hidden`, and we need to access it to get the password.

---
## Key Commands

1. **Navigate to the `inhere` directory:**

```bash
cd inhere
```

1. **List all files, including hidden ones, using the `-a` flag:**

```bash
ls -a
```

1. **Read the contents of the `.hidden` file to retrieve the password:**

```bash
cat .hidden
```

The contents of the file reveal the password:

```bash
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
```

### Alternative Solution (Without Directory Traversal):

Instead of navigating to the `inhere` directory first, we could directly specify the path to the hidden file using the full or relative path in the `ls` and `cat` commands:

1. **List hidden files without changing the directory:**

```bash
ls -a \inhere
```

1. **Read the contents of the `.hidden` file without changing the directory:**

```bash
cat inhere/.hidden
```

The contents of the file reveal the password for the next level:

```bash
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
```
---
### Official Level Link

[Bandit Level 4](https://overthewire.org/wargames/bandit/bandit4.html)
