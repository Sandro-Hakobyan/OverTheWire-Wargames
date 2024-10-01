
## Overview

In this level, the goal is to find a line in the `data.txt` file that appears only once. The tricky part is that the file contains lots of repeated lines, so we need to use a combination of commands to locate the unique line.

---
## Challenge

We’re given a file, `data.txt`, and the password is hidden in a line that only appears once. There are repeated lines in the file, so the task is to filter out the duplicates and find the one that appears only once.

#### Understanding the Problem

We can use the `uniq` command to filter out duplicate lines. However, to ensure we’re getting the correct results, we need to first sort the file (because `uniq` works best when the input is sorted). Then, we can use the `-u` flag with `uniq` to display only the unique lines.

---
## Solution

Here’s the step-by-step approach:

1. **Sort the file** to make sure the lines are ordered (this is needed for `uniq` to work correctly):

```bash
sort data.txt
```

1. **Filter out the unique line** using `uniq -u`. This will give us the line that appears only once:

```bash
sort data.txt | uniq -u
```

1. The output will show us the unique line, which is the password:

```bash
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```

That’s the password for the next level!

---
### Official Level Link

[Bandit Level 9](https://overthewire.org/wargames/bandit/bandit9.html)
