## Overview

In this level, a cron job (automated task) runs a script every minute. Our task is to investigate this script and extract the password for the next level.

---
## A little bit of Theory

**Cron jobs** are scheduled tasks that run at specific time intervals. They are managed in different directories, including:

- `/etc/cron.d/` – Stores system-wide cron job configurations.
- `/etc/cron.daily/`, `/etc/cron.hourly/`, `/etc/cron.weekly/`, `/etc/cron.monthly/` – Contain scripts that run at their respective intervals.
- User-specific crontabs can be edited using the `crontab -e` command.

Each cron job entry follows this format:

```bash
* * * * * user command
```

The five `*` symbols represent time settings (minute, hour, day, month, day of the week).  
For example, `* * * * *` means **every minute**.

---
## Solution

### Step 1: Check the cron jobs

We list the cron jobs inside `/etc/cron.d/` and find a relevant one named `cronjob_bandit22`.

```bash
bandit21@bandit:~$ ls -la /etc/cron.d
```

### Output:

```
total 36
drwxr-xr-x  2 root root 4096 Jul 11  2020 .
drwxr-xr-x 87 root root 4096 May 14  2020 ..
-rw-r--r--  1 root root  120 May  7  2020 cronjob_bandit22
-rw-r--r--  1 root root  122 May  7  2020 cronjob_bandit23
```

### Step 2: Examine the cron job

We check the contents of `cronjob_bandit22`:

```bash
bandit21@bandit:~$ cat /etc/cron.d/cronjob_bandit22
```

### Output:

```
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

This means that `/usr/bin/cronjob_bandit22.sh` runs **every minute** as the user `bandit22`.

### Step 3: Examine the script

We inspect `/usr/bin/cronjob_bandit22.sh` to see what it does:

```bash
bandit21@bandit:~$ cat /usr/bin/cronjob_bandit22.sh
```

### Output:

```bash
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

This script:

1. **Creates a file in `/tmp/`** and changes its permissions to allow everyone to read it (`chmod 644`).
2. **Copies the password for `bandit22`** from `/etc/bandit_pass/bandit22` into this temporary file.

### Step 4: Retrieve the password

Since the script runs **every minute**, we simply read the generated file:

```bash
bandit21@bandit:~$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

### Output:

```
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```

The password for **Level 22** is:

`tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`

---

## Official Level Link

[Bandit Level 22](https://overthewire.org/wargames/bandit/bandit22.html)