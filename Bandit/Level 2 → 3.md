
## Overview

In this level, the task is to find the password for the next level, which is stored in a file named `spaces in this filename` located in the home directory. The file name contains spaces, so it requires special handling to access it properly.

---
## Challenge

We are tasked with reading a file that has spaces in its name. Normally, spaces in file names cause issues with commands as they separate arguments. In this case, we need to properly handle the spaces in the filename to read the file’s contents.

### Understanding the Problem

When using commands like `cat`, spaces are interpreted as separating arguments for the command. This can cause errors if the filename itself contains spaces. To handle such filenames, we can either use quotes (single or double) around the filename or escape the spaces.

---
## Key Commands

1. **Attempting to read the file without handling spaces correctly results in errors:**

```bash
cat spaces in this filename
```

This command tries to open four files: `spaces`, `in`, `this`, and `filename`, and fails because none of them exist.

1. **To read the file with spaces in the name, we need to surround the filename with quotes:**

```bash
cat "spaces in this filename"
```

This correctly handles the filename and reveals the password:

```bash
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```

---
### Official Level Link

[Bandit Level 3](https://overthewire.org/wargames/bandit/bandit3.html)
