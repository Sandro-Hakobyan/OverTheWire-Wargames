
## Overview

In this level, the task is to find the password for the next level, which is stored in the only human-readable file located in the `inhere` directory. The file needs to be identified based on its content type, specifically looking for an ASCII text file.

---
## Challenge

We are given multiple files in the `inhere` directory. The main challenge is to identify the human-readable file among them, which contains the password for the next level.

#### Understanding the Problem

The password is stored in a human-readable file in the directory. The `file` command can help us determine the type of data in each file. Human-readable files are typically encoded in ASCII or Unicode, while other formats like ELF or binary files will not be human-readable.

---
## Key Commands

1. **List the files in the `inhere` directory:**

```bash
cd inhere
ls -la
```

1. **Use the `file` command with a wildcard to identify the file types:**

```bash
file ./*
```

1. **Look for files with "ASCII text" in their type and print the content of the file:**

```bash
cat ./-file07
```

In this case, `-file07` was the only file that was identified as an "ASCII text" file, which is the human-readable file we were looking for.

```bash
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
```

---
### Official Level Link

[Bandit Level 5](https://overthewire.org/wargames/bandit/bandit5.html)
