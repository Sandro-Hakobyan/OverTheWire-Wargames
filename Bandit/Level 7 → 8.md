
## Overview

In this level, the task is to get the password from a file (`data.txt`) by searching for the word "millionth" in the file.

---
#### Challenge

We are given a large file, `data.txt`, and the task is to find the password associated with the word "millionth." The file is too large to open and read manually, so we need to use a more efficient way to search for the word.

#### Understanding the Problem

Instead of opening the file and scanning through it line by line, we can use the `grep` command to search for the word "millionth" in the file. The password is located in the same line as the word "millionth," so using `grep` will directly give us the relevant line.

We can use the `cat` command to output the content of the file and pipe it to `grep` to search for the word "millionth."

---
## Solution

1. First, check the size of the file to confirm its large size:

```bash
du -b data.txt
```

This will show that the file is over 4 million bytes, confirming it's too large to read manually.

1. Use the `grep` command to search for the word "millionth" in the file:

```bash
cat data.txt | grep millionth
```

1. The output will give us the line containing the word "millionth" along with the password:

```bash
millionth       dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```

The password for the next level is `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`.

---
### Official Level Link

[Bandit Level 8](https://overthewire.org/wargames/bandit/bandit8.html)

