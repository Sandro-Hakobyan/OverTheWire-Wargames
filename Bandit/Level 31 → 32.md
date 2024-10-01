## Overview

In this level, we are tasked with finding the password for Bandit Level 32 by interacting with a Git repository. Our objective is to add a new file, commit the change, and push it to the remote repository. The password for the next level is revealed after a successful push.

---

## A little bit of Theory

**Git commands relevant to this level:**

- `git clone`: Clones a repository into a new directory.
- `git add`: Adds changes to the staging area for the next commit.
- `git commit`: Commits the changes, including a message describing what was changed.
- `git push`: Pushes local commits to a remote repository.
- `.gitignore`: A file that specifies files to be ignored by Git (in this case, files with `.txt` extensions).

---

## Solution

### Step 1: Log in and clone the repository

Log in to Bandit Level 31 using SSH:

```bash
ssh bandit31@bandit.labs.overthewire.org -p 2220
```

Password: `fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy`

Create a temporary directory and navigate into it:

```bash
bandit31@bandit:~$ mktemp -d
```

Change to the new directory:

```bash
bandit31@bandit:~$ cd /tmp/tmp.IhtWdYHfZb
```

Clone the Git repository:

```bash
bandit31@bandit:/tmp/tmp.IhtWdYHfZb$ git clone ssh://bandit31-git@localhost/home/bandit31-git/repo
```

List the contents of the directory:

```bash
bandit31@bandit:/tmp/tmp.IhtWdYHfZb$ ls
repo
```

Navigate into the `repo` directory:

```bash
bandit31@bandit:/tmp/tmp.IhtWdYHfZb/repo$ cd repo
```

### Step 2: Check the contents of the repository

List all files in the repository:

```bash
bandit31@bandit:/tmp/tmp.IhtWdYHfZb/repo$ ls -la
```

You will see the `.git` directory, a `.gitignore` file, and a `README.md` file. Let's open the `README.md` file:

```bash
bandit31@bandit:/tmp/tmp.IhtWdYHfZb/repo$ cat README.md
```

The file contains the message:

```
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master
```

### Step 3: Check the `.gitignore` file

List the contents of the `.gitignore` file:

```bash
bandit31@bandit:/tmp/tmp.IhtWdYHfZb/repo$ cat .gitignore
```

The file contains the line:

```
*.txt
```

This means any file with the `.txt` extension will be ignored by Git.

### Step 4: Create the `key.txt` file

Create a new file named `key.txt` and add the required content:

```bash
bandit31@bandit:/tmp/tmp.IhtWdYHfZb/repo$ echo 'May I come in?' > key.txt
```

### Step 5: Force Git to add the `.txt` file

Since `.txt` files are ignored by Git (due to the `.gitignore` file), use the `-f` flag to force the addition of the `key.txt` file:

```bash
bandit31@bandit:/tmp/tmp.IhtWdYHfZb/repo$ git add -f key.txt
```

### Step 6: Commit the changes

Commit the changes to the repository. Git will open the default editor (nano) for the commit message. You can use any message:

```bash
bandit31@bandit:/tmp/tmp.IhtWdYHfZb/repo$ git commit -a
```

### Step 7: Push the changes to the remote repository

Push the changes to the remote repository:

```bash
bandit31@bandit:/tmp/tmp.IhtWdYHfZb/repo$ git push -u origin master
```

You should see the following output:

```
remote: ### Attempting to validate files... ####
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
remote: Well done! Here is the password for the next level:
remote: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
```

The password for **Bandit Level 32** is:

```
3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
```

---

## Official Level Link

[Bandit Level 32](https://overthewire.org/wargames/bandit/bandit32.html)
