## Overview

In this level, we are given two files: `passwords.old` and `passwords.new`. The goal is to find the password for the next level by identifying the one line that has changed between these two files.

## A little bit of Theory

The `diff` command helps compare two files line by line and highlights the differences. In our case, it will point out the single line that differs between `passwords.old` and `passwords.new`.

## Solution

To find the different line, we use:

```bash
bandit17@bandit:~$ diff passwords.old passwords.new
```

### Output:

```
42c42
< ktfgBvpMzWKR5ENj26IbLGSblgUG9CzB
---
> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

Since `passwords.new` is listed second in our `diff` command, the new password is in the second part of the output:

**Password for Level 18:**  
`x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`
___
## Alternative Approach

Another way to find the difference is by using `sort` and `uniq -u`. However, this method doesn't preserve the file order, making it less reliable.

```bash
bandit17@bandit:~$ sort passwords.old passwords.new | uniq -u
```

### Output:

```
kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
w0Yfolrc5bwjS4qw5mq1nnQi6mF03bii
```

Since this output isn't very helpful, we can confirm the correct password using `grep`:

```bash
bandit17@bandit:~$ cat passwords.new | grep x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

___
## Official Level Link

[Bandit Level 18](https://overthewire.org/wargames/bandit/bandit18.html)
