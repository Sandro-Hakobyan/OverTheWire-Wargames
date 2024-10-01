## Overview

In this level, we are tasked with accessing a Git repository to find the password for Bandit Level 29. This time, we need to investigate the Git history of the `README.md` file, as the password is hidden in a past version.

---

## A little bit of Theory

In addition to the Git commands learned in the previous level, this level introduces two more:

- `git log`: Shows the commit log of the repository.
- `git show <commit>`: Displays the content of a specific commit.

By inspecting the commit history, we can potentially recover sensitive information like passwords that were removed from later versions.

---

## Solution

### Step 1: Log in and clone the repository

First, log in to the system using SSH with the credentials provided for Bandit Level 28:

```bash
ssh bandit28@bandit.labs.overthewire.org -p 2220
```

Password: `Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN`

Next, create a temporary directory and navigate into it:

```bash
bandit28@bandit:~$ mktemp -d
```

Change to the new directory:

```bash
bandit28@bandit:~$ cd /tmp/tmp.lGUWKxK6CU
```

Clone the Git repository:

```bash
bandit28@bandit:/tmp/tmp.lGUWKxK6CU$ git clone ssh://bandit28-git@localhost/home/bandit28-git/repo
```

When prompted for a password, use the password for Bandit Level 28.

### Step 2: Check the contents of the repository

List the contents of the repository directory:

```bash
bandit28@bandit:/tmp/tmp.lGUWKxK6CU$ ls
```

Change to the repository directory:

```bash
bandit28@bandit:/tmp/tmp.lGUWKxK6CU$ cd repo
```

List all files in the directory, including hidden ones:

```bash
bandit28@bandit:/tmp/tmp.lGUWKxK6CU/repo$ ls -la
```

You will see the `.git` directory and a `README.md` file.

### Step 3: Inspect the `README.md` file

Open the `README.md` file to see what is mentioned:

```bash
bandit28@bandit:/tmp/tmp.lGUWKxK6CU/repo$ cat README.md
```

The file contains notes for Level 29, but it does not directly include the password.

### Step 4: Check the Git history

To find the password, inspect the Git log:

```bash
bandit28@bandit:/tmp/tmp.lGUWKxK6CU/repo$ git log
```

Look for a commit with a message like "fix info leak," which suggests that sensitive information was removed.

### Step 5: View the commit that fixed the info leak

Use `git show` to inspect the details of the commit that fixes the information leak:

```bash
bandit28@bandit:/tmp/tmp.lGUWKxK6CU/repo$ git show edd935d60906b33f0619605abd1689808ccdd5ee
```

You will see the difference between the previous and updated versions of the `README.md` file. The password for **Bandit Level 29** is revealed in the commit:

```
- password: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
+ password: xxxxxxxxxx
```

---

## Official Level Link

[Bandit Level 29](https://overthewire.org/wargames/bandit/bandit29.html)
