## Overview

In this level, we are tasked with finding the password for Bandit Level 31 by exploring a Git repository. The password is hidden in a Git tag, and our goal is to find and display it using Git commands.

---

## A little bit of Theory

Git tagging allows us to mark specific points in the history of the repository. This is useful for marking release points or other important milestones in a project.

**Git commands relevant to this level:**

- `git tag`: Lists all tags in the repository.
- `git show <tag_name>`: Displays the details of a specific tag, including the commit and any associated message.

By using the `git tag` command, we can identify and view a tag that contains the password for the next level.

---

## Solution

### Step 1: Log in and clone the repository

Log in to Bandit Level 30 using SSH:

```bash
ssh bandit30@bandit.labs.overthewire.org -p 2220
```

Password: `qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL`

Create a temporary directory and navigate into it:

```bash
bandit30@bandit:~$ mktemp -d
```

Change to the new directory:

```bash
bandit30@bandit:~$ cd /tmp/tmp.iFnbTcdMf4
```

Clone the Git repository:

```bash
bandit30@bandit:/tmp/tmp.iFnbTcdMf4$ git clone ssh://bandit30-git@localhost/home/bandit30-git/repo
```

List the contents of the directory:

```bash
bandit30@bandit:/tmp/tmp.iFnbTcdMf4$ ls
repo
```

Navigate into the `repo` directory:

```bash
bandit30@bandit:/tmp/tmp.iFnbTcdMf4$ cd repo
```

### Step 2: Check the contents of the repository

List all files in the repository:

```bash
bandit30@bandit:/tmp/tmp.iFnbTcdMf4/repo$ ls -la
```

You will see the `.git` directory and a `README.md` file. Let’s open the `README.md` file:

```bash
bandit30@bandit:/tmp/tmp.iFnbTcdMf4/repo$ cat README.md
```

The file contains the message:

```
just an empty file... muahaha
```

### Step 3: Check for Git tags

List the tags in the Git repository:

```bash
bandit30@bandit:/tmp/tmp.iFnbTcdMf4/repo$ git tag
```

You will see a tag named `secret`, which likely contains the password.

### Step 4: Display the details of the `secret` tag

Use the `git show` command to display the details of the `secret` tag:

```bash
bandit30@bandit:/tmp/tmp.iFnbTcdMf4/repo$ git show secret
```

The password for **Bandit Level 31** is revealed:

```
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
```

---

## Official Level Link

[Bandit Level 31](https://overthewire.org/wargames/bandit/bandit31.html)