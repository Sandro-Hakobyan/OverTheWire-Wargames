## Introduction

This is a walkthrough for the [OverTheWire Wargame Krypton](https://overthewire.org/wargames/krypton/). Krypton is a cryptography-based wargame, and in this level, we will decrypt the password using the ROT13 cipher. The game challenges you to decipher simple cryptographic methods.

## Login

SSH:  `ssh krypton1@krypton.labs.overthewire.org -p 2231`  
Password:  `KRYPTONISGREAT`

## Task

The password for level 2 is in the file ‘krypton2’. It is ‘encrypted’ using a simple rotation cipher known as ROT13. The file also keeps word boundaries from the plain text, making the ciphertext non-standard. Your task is to decrypt the password from this file.

## A little bit of Theory

**ROT13** is a substitution cipher where each letter of the alphabet is replaced by the letter 13 positions ahead. The ROT13 cipher is its own inverse, meaning that applying ROT13 twice returns the original text.

A more general cipher similar to ROT13 is the **Caesar cipher**, which shifts characters by a fixed number (the key). For example, with a Caesar cipher of key 1, 'A' becomes 'B', 'B' becomes 'C', and so on, wrapping around to 'A' after 'Z'.

The **`tr` command** in bash can be used to replace or translate characters. In this case, the command will replace letters in the ROT13 pattern. The structure of the command is `tr '<initial_chars>' '<replacement_chars>'`.

### Useful command aliases:

```bash
alias rot13="tr 'A-Za-z' 'N-ZA-Mn-za-m'"
alias rot5="tr '0-9' '5-90-4'"
alias rot="tr 'A-Za-z0-9' 'N-ZA-Mn-za-m5-90-4'"
```

## Solution

The file `krypton2` contains the encrypted text. Let's check its contents:

```bash
krypton1@krypton:/krypton/krypton1$ cat krypton2
YRIRY GJB CNFFJBEQ EBGGRA
```

We can use the `tr` command to apply ROT13 and decrypt the text. The alias `rot13` will be used to simplify the process.

```bash
krypton1@krypton:/krypton/krypton1$ cat krypton2 | rot13
LEVEL TWO PASSWORD ROTTEN
```

The password for level 2 is now decrypted as **"LEVEL TWO PASSWORD `ROTTEN`"**.

---

### Official Level Link

[Krypton Level 1](https://overthewire.org/wargames/krypton/krypton1.html)
