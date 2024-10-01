## Overview

**In this level, we are tasked with accessing a Git repository to find the password for Bandit Level 30. The password is hidden within a specific branch of the Git repository. We will need to inspect the repository and switch to the correct branch to find it.**

**---**

## A little bit of Theory

**In this level, we will work with Git branches. Git allows the use of branches to separate work into different versions. Each branch can have different versions of files, and by switching branches, you can explore various versions of the project.**

**Git commands used in this level:**

- **`git branch`: Lists available branches.**
- **`git checkout <branch_name>`: Switches to the specified branch.**
- **`git log`: Shows commit history.**
- **`git show <commit>`: Displays content from a specific commit.**

**By inspecting the different branches, we can identify which one holds the password for the next level.**

**---**

## Solution

### Step 1: Log in and clone the repository

**Log in to Bandit Level 29 using SSH:**

```bash
ssh bandit29@bandit.labs.overthewire.org -p 2220
```

**Password: `4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7`**

**Create a temporary directory and navigate into it:**

```bash
bandit29@bandit:~$ mktemp -d
bandit29@bandit:~$ cd /tmp/tmp.Qjbad6ocpi
```

**Clone the Git repository:**

```bash
bandit29@bandit:/tmp/tmp.Qjbad6ocpi$ git clone ssh://bandit29-git@localhost/home/bandit29-git/repo
```

**Enter the repository directory:**

```bash
bandit29@bandit:/tmp/tmp.Qjbad6ocpi$ cd repo
```

### Step 2: Inspect the repository contents

**List the contents of the repository:**

```bash
bandit29@bandit:/tmp/tmp.Qjbad6ocpi/repo$ ls -la
```

**You will see a `.git` directory and a `README.md` file. Let's check the content of the `README.md` file:**

```bash
bandit29@bandit:/tmp/tmp.Qjbad6ocpi/repo$ cat README.md
```

**The file contains a note for the next level with the username and a password placeholder:**

```
- username: bandit30
- password: <no passwords in production!>
```

### Step 3: Check the available branches

**List the available Git branches:**

```bash
bandit29@bandit:/tmp/tmp.Qjbad6ocpi/repo$ git branch -a
```

**You will see a list of branches, including:**

- **`remotes/origin/master`**
- **`remotes/origin/dev`**
- **`remotes/origin/sploits-dev`**

**The note in the `README.md` file suggests there might be more information in the development branch (`dev`).**

### Step 4: Switch to the dev branch

**Switch to the `dev` branch:**

```bash
bandit29@bandit:/tmp/tmp.Qjbad6ocpi/repo$ git checkout dev
```

**List the contents of the repository again:**

```bash
bandit29@bandit:/tmp/tmp.Qjbad6ocpi/repo$ ls -la
```

**You will see a new directory `code` along with the `.git` directory and `README.md` file.**

### Step 5: Check the content of the `README.md` file

**Read the `README.md` file in the `dev` branch:**

```bash
bandit29@bandit:/tmp/tmp.Qjbad6ocpi/repo$ cat README.md
```

**You will find the password for Bandit Level 30:**

```
- username: bandit30
- password: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
```

**---**

## Official Level Link

**[Bandit Level 30](https://overthewire.org/wargames/bandit/bandit30.html)**