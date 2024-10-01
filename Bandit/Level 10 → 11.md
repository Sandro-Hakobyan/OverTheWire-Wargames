
## Overview

In this level, we are tasked with decoding base64-encoded data stored in a file, `data.txt`. The decoded content will reveal the password for the next level.

---
## Challenge

The file `data.txt` contains base64-encoded data. We need to decode it to retrieve the password for the next level.

#### Understanding the Problem

Base64 is an encoding method that converts binary data into text. It’s commonly used for transmitting data over text-based protocols. The `base64` command-line utility in Linux can encode and decode base64 data.

---
## Solution

To decode the base64 content of the file, we will use the `base64 -d` command, which decodes the base64 input.

1. **Display the content of `data.txt`** to see the base64-encoded string:

```bash
cat data.txt
```

The output will look something like this:

```
VGhlIHBhc3N3b3JkIGlzIElGdWt3S0dzRlc4TU9xM0lSRnFyeEUxaHhUTkViVVBSCg==
```

1. **Decode the base64 string** using the `base64 -d` command:

```bash
base64 -d data.txt
```

1. The output will be the password for the next level:

```
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```

The password is `dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`.

---
### Official Level Link

[Bandit Level 11](https://overthewire.org/wargames/bandit/bandit11.html)
