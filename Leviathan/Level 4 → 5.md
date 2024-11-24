## Login

SSH: `ssh leviathan4@leviathan.labs.overthewire.org -p 2223`

Password: `WG1egElCvO`

## A little bit of Theory

**Binary** code is the most basic representation of data for a computer. This is what the computer uses internally. It comes from the binary number system, which only includes ‘0’ and ‘1’ as digits, also called ‘bits’.

In computer science, there are different encodings to represent human-readable text. The most basic and common one is the ‘American Standard Code for Information Interchange’ (**ASCII**). ASCII uses 7 bits to represent one character. Generally, if you were to transform binary to ASCII per hand, you would first transform binary to our decimal system and look up the corresponding letter in an ASCII table. _Example: ‘01000001’ -> ‘65’ -> ‘A’._

## Solution

In the home directory, we find a directory called ‘.trash’ that is new and belongs to the ’leviathan4’ group. So we check it out.

```bash
leviathan4@leviathan:~$ ls -la
total 24
drwxr-xr-x  3 root root       4096 Aug 26  2019 .
drwxr-xr-x 10 root root       4096 Aug 26  2019 ..
-rw-r--r--  1 root root        220 May 15  2017 .bash_logout
-rw-r--r--  1 root root       3526 May 15  2017 .bashrc
-rw-r--r--  1 root root        675 May 15  2017 .profile
dr-xr-x---  2 root leviathan4 4096 Aug 26  2019 .trash
leviathan4@leviathan:~$ cd .trash/
leviathan4@leviathan:~/.trash$ ls -la
total 16
dr-xr-x--- 2 root       leviathan4 4096 Aug 26  2019 .
drwxr-xr-x 3 root       root       4096 Aug 26  2019 ..
-r-sr-x--- 1 leviathan5 leviathan4 7352 Aug 26  2019 bin
```

There is a SUID binary belonging to user ’leviathan5’. So as always, we try and figure out what it does.

```bash
leviathan4@leviathan:~/.trash$ ./bin 
01010100 01101001 01110100 01101000 00110100 01100011 01101111 01101011 01100101 01101001 00001010
```

It returns a string of zeros and ones. This is binary. - Using ltrace, it seems like it opens the password file for user ’leviathan5’ and most likely transforms it into this binary string. So our goal is to reverse the binary into ASCII format and hopefully get the password.

There are different ways to go about this transformation. We could do it by hand, use a website or with the command line. We could write a short program in python or another language. I wanted a short and easy solution for the command line. Quick googling shows me the following example with Perl: `bash echo 0100000101000010 | perl -lpe '$_=pack"B*",$_'`. So let’s try it. _(Hint: Make sure to not leave any space between the bits.)_

```bash
leviathan4@leviathan:~/.trash$ echo 0101010001101001011101000110100000110100011000110110111101101011011001010110100100001010 | perl -lpe '$_=pack"B*",$_'
0dyxT7F4QD
```

And it worked. Testing the resulting string shows that it is indeed the correct password for the next level.

---
### Official Level Link

[Leviathan Level 5](https://overthewire.org/wargames/leviathan/leviathan5.html)