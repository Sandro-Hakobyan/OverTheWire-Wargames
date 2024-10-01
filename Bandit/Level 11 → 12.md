
## Overview

In this level, the password for the next level is stored in a file, `data.txt`, where all letters (both lowercase and uppercase) have been rotated by 13 positions using the ROT13 cipher. Our task is to decode it.

---
## Challenge

The content of `data.txt` is encoded using the **ROT13 cipher**, which shifts each letter by 13 positions. We need to decode this using a Linux tool to find the password.

#### Understanding the Problem

ROT13 is a simple letter substitution cipher where each letter is replaced by the letter 13 positions ahead of it in the alphabet (e.g., A → N, B → O, etc.). It’s a symmetrical cipher, meaning that applying ROT13 twice returns the original text.

---
## Solution

Linux does not have a built-in ROT13 command, but we can use the `tr` (translate) command to perform the substitution.

1. **Check the content of `data.txt`** to view the ROT13-encoded string:

```bash
cat data.txt
```

The output will look something like this (it will be ROT13 encoded):

```
The password is 7k16JNvHIi5XjvWsfQIqbbtaHGTw9D4
```

1. **Decode the ROT13 string** using the `tr` command:

```bash
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

This will shift the letters back by 13 positions and decode the password.

1. The output will be the password for the next level:

```
The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
```

The password is `7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`.

---
### Optional: Create an Alias for ROT13

If you plan on using ROT13 frequently, you can create an alias for it to make future use easier. Add the following to your `.bashrc` or `.bash_aliases`:

```bash
alias rot13="tr 'A-Za-z' 'N-ZA-Mn-za-m'"
alias rot5="tr '0-9' '5-90-4'"
```

This way, you can simply use `rot13` in the terminal to decode text instead of typing the full command each time.

---
### Official Level Link

[Bandit Level 12](https://overthewire.org/wargames/bandit/bandit12.html)

