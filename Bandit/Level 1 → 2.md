
## Overview

In this level, the password for `bandit2` is stored in a file named `-`. However, since `-` is a special character in Linux, it requires a different approach to read its contents.

---
## Challenge

Retrieve the password stored in a file named `-` in the home directory.

### Understanding the Problem

In Linux, `-` is typically used to represent **standard input (stdin)** or an **option flag**. This means that running:

```bash
cat -
```

Would make `cat` wait for input instead of reading the file.

To handle this, we must specify the **full path** or use `./` to explicitly reference the file.

---
## Key Commands

1. **Log in via SSH:**
    
    ```bash
    ssh bandit1@bandit.labs.overthewire.org -p 2220
    ```
    
    Password: `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`
    
2. **List the files in the directory:**
    
    ```bash
    ls
    ```
    
    Output: `-`
    
3. **Read the file using its relative path:**
    
    ```bash
    cat ./-
    ```
    
    Output: `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`
    

This is the password for `bandit2`, completing **Level 1 → 2**.

---
### Official Level Link

[Bandit Level 2](https://overthewire.org/wargames/bandit/bandit2.html)
