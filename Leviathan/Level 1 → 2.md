## Login

SSH: `ssh leviathan1@leviathan.labs.overthewire.org -p 2223`

Password: `3QJ3TgzHDq`

## A little bit of Theory

`ltrace` is used to see what library calls are made during binary execution. Library calls are, when a program calls a function from a file that is shared by multiple programs. So, for example, checking if the input is the correct password can be done with a library function. It will show the function and its input parameters - which then include the password. Such a function to compare strings is `strcmp`.

**Revision:** `strings` explained in [Bandit Level 10]() `SUID` explained in [Bandit Level 20]()

## Solution

Same start as before, ssh into the machine and check out the home directory:

```bash
leviathan1@leviathan:~$ ls -la
total 28
drwxr-xr-x  2 root       root       4096 Aug 26  2019 .
drwxr-xr-x 10 root       root       4096 Aug 26  2019 ..
-rw-r--r--  1 root       root        220 May 15  2017 .bash_logout
-rw-r--r--  1 root       root       3526 May 15  2017 .bashrc
-r-sr-x---  1 leviathan2 leviathan1 7452 Aug 26  2019 check
-rw-r--r--  1 root       root        675 May 15  2017 .profile
```

This time the ‘check’ file, which is an executable, looks promising since it belongs to user leviathan2. Also, we have ‘read’ and ’execute’ permissions and it has the SUID bit set. So let’s see what it does.

```bash
leviathan1@leviathan:~$ ./check
password: test
Wrong password, Good Bye ...
```

So it seems to check if we type in the correct password for the leviathan2 user. Now, depending on how the password is checked, we might find the password as a string in the binary. So let’s use this command:

```bash
leviathan1@leviathan:~$ strings check
```

This gives back all strings in the binary. There are some suspicious-looking strings, however, nothing concrete that I would try. So instead, we can try `ltrace`.

```bash
leviathan1@leviathan:~$ ltrace ./check 
__libc_start_main(0x804853b, 1, 0xffffd794, 0x8048610 <unfinished ...>
printf("password: ")                                                    = 10
getchar(1, 0, 0x65766f6c, 0x646f6700password: test_password
)                                   = 116
getchar(1, 0, 0x65766f6c, 0x646f6700)                                   = 101
getchar(1, 0, 0x65766f6c, 0x646f6700)                                   = 115
strcmp("tes", "sex")                                                    = 1
puts("Wrong password, Good Bye ..."Wrong password, Good Bye ...
)                                    = 29
+++ exited (status 0) +++
```

So I just chose a random password and it seems that ‘strcmp’ was in fact called: `strcmp("tes", "sex")`, the first three letters were compared to the password. So the password seems to be `sex`. Let’s check if that is correct by running the binary again.

```bash
leviathan1@leviathan:~$ ./check
password: sex
$ 
```

This seems to work and give us a shell in which we are user ’leviathan2’, because it was a SUID binary.

```bash
$ whoami
leviathan2
```

This means we can now look for the actual password for the ’leviathan2’ user. Based on the description on the website, all passwords are stored under ’etc/leviathan_pass’.

```bash
$ cat /etc/leviathan_pass/leviathan2
NsN1HwFoyN
```
---
### Official Level Link

[Leviathan Level 2](https://overthewire.org/wargames/leviathan/leviathan2.html)
