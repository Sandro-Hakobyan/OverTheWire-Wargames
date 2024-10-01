## Overview

In this level, we are tasked with retrieving the password for Bandit Level 27. After successfully breaking out of the restricted shell in Level 26, we now need to execute a command as another user to retrieve the password. We will use a file called `bandit27-do` that allows us to run commands as other users, and from there, we can simply print the password file for Bandit Level 27.

---

## A little bit of Theory

In this level, we revisit concepts from Level 20, where we use a utility that allows us to run commands as another user. The `bandit27-do` file in Bandit Level 26’s home directory can be used to execute commands as another user, which is essential for retrieving the password file for Bandit Level 27.

---

## Solution

### Step 1: Log in and access the home directory

First, log in to the system using SSH with the credentials provided for Bandit Level 26:

```bash
ssh bandit26@bandit.labs.overthewire.org -p 2220
```

Password: `s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ`

Once logged in, list the files in the home directory:

```bash
bandit26@bandit:~$ ls
```

### Output:

```
bandit27-do  text.txt
```

### Step 2: Execute the `bandit27-do` file

The file `bandit27-do` seems to be the tool we need to run commands as another user. We can execute it by running the following command:

```bash
bandit26@bandit:~$ ./bandit27-do
```

### Output:

```
Run a command as another user.
Example: ./bandit27-do id
```

This confirms that the tool runs commands as another user. Now, we can use it to access the password file for Bandit Level 27.

### Step 3: Retrieve the password

We use the `bandit27-do` command to print the contents of the password file for Bandit Level 27:

```bash
bandit26@bandit:~$ ./bandit27-do cat /etc/bandit_pass/bandit27
```

### Output:

```
upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB
```

The password for **Bandit Level 27** is:

`upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB`

---

## Official Level Link

[Bandit Level 27](https://overthewire.org/wargames/bandit/bandit27.html)
