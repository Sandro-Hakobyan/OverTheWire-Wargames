
## Overview

In this level, we log into the Bandit server and retrieve the password for the next level, which is stored in a file named `readme` in the home directory.

---
## Challenge

Find the password for `bandit1`, which is stored in `readme` inside the home directory.

---
## Approach

When logging into a machine via **SSH**, we are placed in the home directory of the user. In this case, we log in as `bandit0`, so we start in `/home/bandit0/`.

To navigate and interact with the filesystem, we use basic Linux commands:

- `pwd` → Prints the current working directory.
- `ls` → Lists files in the directory.
- `cat <filename>` → Displays the contents of a file.

---
## Key Commands

1. **Log in via SSH:**
    
    ```bash
    ssh bandit0@bandit.labs.overthewire.org -p 2220
    ```
    
    Password: `bandit0`
    
2. **Check the current directory (optional):**
    
    ```bash
    pwd
    ```
    
    Output: `/home/bandit0/`
    
3. **List the files in the directory:**
    
    ```bash
    ls
    ```
    
    Output: `readme`
    
4. **Read the password from the file:**
    
    ```bash
    cat readme
    ```
    
    Output: `boJ9jbbUNNfktd78OOpsqOltutMc3MY1`
    

This is the password for `bandit1`, completing **Level 0 → 1**.

---
### Official Level Link

[Bandit Level 1](https://overthewire.org/wargames/bandit/bandit1.html)
