## Login

SSH: `ssh leviathan3@leviathan.labs.overthewire.org -p 2223`

Password: `f0n8h2iWLP`

## A little bit of Theory

Nothing new. Theory from [Level 2]().

## Solution

We start the same way as always, by looking at the home directory. We find a SUID binary called ’level3’ that asks for a password.

```bash
leviathan3@leviathan:~$ ls -la
total 32
drwxr-xr-x  2 root       root        4096 Aug 26  2019 .
drwxr-xr-x 10 root       root        4096 Aug 26  2019 ..
-rw-r--r--  1 root       root         220 May 15  2017 .bash_logout
-rw-r--r--  1 root       root        3526 May 15  2017 .bashrc
-r-sr-x---  1 leviathan4 leviathan3 10288 Aug 26  2019 level3
-rw-r--r--  1 root       root         675 May 15  2017 .profile
leviathan3@leviathan:~$ ./level3 
Enter the password> Ahdiemoo1j
bzzzzzzzzap. WRONG
```

We can start analysing the binary the same way as we have done in previous levels. So let’s try `ltrace` again:

```bash
leviathan3@leviathan:~$ ltrace ./level3 
__libc_start_main(0x8048618, 1, 0xffffd784, 0x80486d0 <unfinished ...>
strcmp("h0no33", "kakaka")                                             = -1
printf("Enter the password> ")                                         = 20
fgets(Enter the password> test
"test\n", 256, 0xf7fc55a0)                                       = 0xffffd590
strcmp("test\n", "snlprintf\n")                                        = 1
puts("bzzzzzzzzap. WRONG"bzzzzzzzzap. WRONG
)                                             = 19
+++ exited (status 0) +++
leviathan3@leviathan:~$ ./level3 
Enter the password> snlprintf
[You've got shell]!
$ whoami
leviathan4
```

Like in a previous Level, we can see that the binary uses ‘strcmp’, a library function, to compare our input with the correct password. Running the binary with the password ‘snlprintf’ gives us a shell as ’leviathan4’. So now we just need to read the password file.

```bash
$ cat /etc/leviathan_pass/leviathan4
WG1egElCvO
```

---
### Official Level Link

[Leviathan Level 4](https://overthewire.org/wargames/leviathan/leviathan4.html)