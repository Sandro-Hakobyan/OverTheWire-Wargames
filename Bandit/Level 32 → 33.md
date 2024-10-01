## Overview

In this level, we are tasked with escaping an "UPPERCASE SHELL" environment in order to access the password for Bandit Level 33. The goal is to use environment variables and shell manipulation to break out of the uppercase shell, where we are unable to use lowercase commands.

---

## A little bit of Theory

Linux systems have different types of variables:

- **Local variables**: Valid only in the current shell session.
- **Shell variables**: Set up by the shell and valid within the shell.
- **Environment variables**: Valid system-wide and often in uppercase.

Some common environment variables include:

- `TERM`: The terminal type.
- `HOME`: The path to the home directory of the currently logged-in user.
- `LANG`: The locale settings.
- `PATH`: The directories the shell will search for commands.
- `PWD`: The current working directory.
- `SHELL/0`: The current shell path.
- `USER`: The currently logged-in user.

To check environment variables, you can use `echo $VAR_NAME` or `printenv` to display them all.

---

## Solution

### Step 1: Log in to Bandit Level 32

Log in to Bandit Level 32 using SSH:

```bash
ssh bandit32@bandit.labs.overthewire.org -p 2220
```

Password: `3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K`

### Step 2: Encounter the uppercase shell

Once logged in, we see that we are in an environment where commands are automatically converted to uppercase. For example:

```bash
bandit32@bandit:~$ ls
sh: 1: LS: not found
```

This means all commands need to be typed in uppercase, but we are unable to execute normal lowercase commands.

### Step 3: Use the `$0` variable to escape

In Linux, `$0` is a special variable that refers to the name of the current shell. We can use this to escape the uppercase shell and execute commands again in the normal shell:

```bash
bandit32@bandit:~$ $0
```

After executing `$0`, we are able to use commands again. For example:

```bash
bandit32@bandit:~$ ls -la
```

### Step 4: Identify the `uppershell` program

We notice that there is an executable file called `uppershell`:

```bash
bandit32@bandit:~$ ls -la
-rwsr-x--- 1 bandit33 bandit32 7556 May  7  2020 uppershell
```

The `uppershell` file is owned by `bandit33` and has the SUID (Set User ID) bit set, meaning it will execute with the privileges of the user `bandit33`.

### Step 5: Switch to the `bandit33` user

We can now use the `whoami` command to confirm that we are executing as `bandit33`:

```bash
bandit32@bandit:~$ whoami
bandit33
```

Since we are running as `bandit33`, we can read the password file for Bandit Level 33:

```bash
bandit32@bandit:~$ cat /etc/bandit_pass/bandit33
```

The password for **Bandit Level 33** is:

```
tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
```

### Step 6: Confirm the password

You can now log in to Bandit Level 33 to verify the password:

```bash
ssh bandit33@bandit.labs.overthewire.org -p 2220
```

Password: `tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0`

### Step 7: Final verification

Once logged in to Bandit Level 33, you will see the following message in the `README.txt` file:

```bash
bandit33@bandit:~$ cat README.txt
Congratulations on solving the last level of this game!

At this moment, there are no more levels to play in this game. However, we are constantly working
on new levels and will most likely expand this game with more levels soon.
Keep an eye out for an announcement on our usual communication channels!
In the meantime, you could play some of our other wargames.

If you have an idea for an awesome new level, please let us know!
```

---

## Official Level Link

[Bandit Level 33](https://overthewire.org/wargames/bandit/bandit33.html)
