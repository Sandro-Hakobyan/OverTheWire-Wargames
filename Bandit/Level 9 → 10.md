
## Overview

In this level, the goal is to extract the password for the next level, which is hidden in a file, `data.txt`. The password is a human-readable string that is preceded by several '=' characters. We need to filter through the file and find this password.

---
## Challenge

We need to find the password hidden within `data.txt`. The task specifies that the password is a human-readable string preceded by several `=` characters. To extract it, we need to use a combination of commands that help us isolate the relevant string.

#### Understanding the Problem

To solve this, we can use the following steps:

1. **Use the `strings` command**: This command extracts human-readable strings from files. It’s helpful when you have a file with mostly non-printable data, as it can find readable sequences.
    
2. **Filter the output** using `grep` to find lines with `=` characters, since the password is preceded by several equal signs.
    

---
## Solution

Here’s the step-by-step solution:

1. **Use the `strings` command** to extract human-readable strings from `data.txt`:

```bash
strings data.txt
```

1. **Filter the output with `grep`** to look for lines containing `=` characters. Since we know the password is preceded by several `=`, we use `grep` to match these lines:

```bash
strings data.txt | grep ===
```

1. The output will show lines with `=` characters. The one that contains the password looks like this:

```bash
==========  FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```

The password is `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`.

---
### Official Level Link

[Bandit Level 10](https://overthewire.org/wargames/bandit/bandit10.html)

