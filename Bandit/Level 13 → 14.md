
## Overview

In this level, the goal is to obtain the password for the next level. However, rather than providing a password directly, we are given a private SSH key that can be used to log into Level 14. The password is stored in a file `/etc/bandit_pass/bandit14`, but it can only be accessed by the user `bandit14`. This introduces us to SSH key-based authentication instead of password-based login.

---
## Challenge:

- The task is to download the private SSH key from the home directory, set appropriate permissions for it, and then use it to SSH into the next level.
- After gaining access to the next level, we will be able to access the password for Level 14.

---

## Solution

1. **Log into the server as `bandit13`**:
    
    - Use the provided SSH credentials:
    
    ```bash
    ssh bandit13@bandit.labs.overthewire.org -p 2220
    Password: FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
    ```
    
2. **List the files in the home directory**:
    
    - We can see the private SSH key `sshkey.private` in the home directory of `bandit13`:
    
    ```bash
    bandit13@bandit:~$ ls
    sshkey.private
    ```
    
3. **Exit the SSH session**:
    
    - Exit the current session:
    
    ```bash
    bandit13@bandit:~$ exit
    ```
    
4. **Use `scp` to transfer the private SSH key** to your local machine:
    
    - On your local machine, run the following `scp` command to download the private key:
    
    ```bash
    scp -P 2220 bandit13@bandit.labs.overthewire.org:sshkey.private .
    ```
    
    - You will be prompted for the password again, but this time it is for `bandit13` (the one you used earlier).
5. **Fix the file permissions for the SSH key**:
    
    - By default, the private key file may have too open permissions, so you need to restrict it. Run the following command to change the permissions:
    
    ```bash
    chmod 600 sshkey.private
    ```
    
6. **Log into the next level (Level 14)** using the private key:
    
    - Now that the key has the correct permissions, use the `-i` flag to specify the private key and log in as `bandit14`:
    
    ```bash
    ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
    ```
    
7. **Access the password**:
    
    - After successfully logging in as `bandit14`, the password for Level 14 will be in the file `/etc/bandit_pass/bandit14`.
    - Simply read the file to obtain the password:
    
    ```bash
    cat /etc/bandit_pass/bandit14
    ```
    
	- The password for the next level is `MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`

---

#### **Commands:**

1. `ssh bandit13@bandit.labs.overthewire.org -p 2220`
2. `ls`
3. `exit`
4. `scp -P 2220 bandit13@bandit.labs.overthewire.org:sshkey.private .`
5. `chmod 600 sshkey.private`
6. `ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220`
7. `cat /etc/bandit_pass/bandit14`

---

### Official Level Link

[Bandit Level 14](https://overthewire.org/wargames/bandit/bandit14.html)
