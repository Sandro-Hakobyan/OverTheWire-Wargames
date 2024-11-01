## Introduction

This writeup focuses on how to break the encryption in Krypton Level 6, where we are tasked with decrypting a ciphertext using a weak stream cipher. We will perform a known-plaintext attack and use a decryption method to obtain the password for the next level (Krypton7).

---

## Login

SSH: `ssh krypton6@krypton.labs.overthewire.org -p 2231`  
Password: `RANDOM`

---

## Task

In this level, we are given a file encrypted with a weak stream cipher, which uses a key derived from the book _A Tale of Two Cities_. Our goal is to decrypt the ciphertext and retrieve the password for Krypton7.

### Hints Provided:

- The ciphertext is encrypted using the `encrypt6` utility.
- The key for the encryption is based on the book _A Tale of Two Cities_.
- The task suggests performing a known-plaintext attack.
- The password for the next level is encrypted with `encrypt6`.

---

## Solution

### Step 1: Analyze the Ciphertext

We begin by creating a known plaintext, a string of repeated "A"s, and encrypting it with the `encrypt6` utility. This is a typical strategy for a known-plaintext attack, where we attempt to identify patterns in the ciphertext.

First, create a file with 40 "A"s:

```bash
$ python -c 'print("A" * 40)' > plain
```

Next, encrypt the file using the `encrypt6` utility:

```bash
$ /krypton/krypton6/encrypt6 plain cipher
```

The result of this encryption is a ciphertext:

```bash
EICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZ
```

### Step 2: Identify Patterns in the Ciphertext

By observing the ciphertext, we can see that the same letter ("A") in the plaintext is mapped to different letters in the ciphertext depending on its position. This pattern suggests that the encryption might be using a Vigenère cipher, where the key is repeated cyclically across the plaintext.

Ciphertext:

```bash
EICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZ
```

This pattern suggests a repeating key structure.

### Step 3: Determine the Key Length

From the structure of the ciphertext, we can deduce that the key is most likely 26 characters long, as we observe repeating patterns every 26 characters. This is typical of a Vigenère cipher with a key length of 26.

### Step 4: Apply Vigenère Decryption

Now that we have an idea of the key structure, we can proceed to decrypt the ciphertext for Krypton7.

The ciphertext for the password of level 7 is:

```bash
PNUKLYLWRQKGKBE
```

The key derived from the analysis of the ciphertext is:

```bash
EICTDGYIYZKTHNSIRFXYCPFUEOCKRN
```

We apply the Vigenère decryption formula to the ciphertext using this key:

```python
ciphertext = 'PNUKLYLWRQKGKBE'
key = 'EICTDGYIYZKTHNSIRFXYCPFUEOCKRN'

for c, k in zip(ciphertext, key):
    p = ord('A') + ((ord(c) - ord(k)) % 26)
    print(chr(p), end='')

print()
```

The decrypted result is:

```bash
LSFRISNOTRANDOM
```

### Step 5: Obtain the Password

The password for level 7 is:

```bash
LSFRISNOTRANDOM
```

---

## Official Level Link

[Krypton Level 6](https://overthewire.org/wargames/krypton/krypton6.html)
