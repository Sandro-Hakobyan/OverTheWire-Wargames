## Introduction

This is a walkthrough for the [OverTheWire Wargame Krypton](https://overthewire.org/wargames/krypton/). Krypton is a cryptography-based wargame, and in this level, we will break a cipher using frequency analysis. The goal is to use intercepted ciphertexts from multiple files and analyze the frequencies of letters to uncover the password for the next level.

## Login

SSH: `ssh krypton3@krypton.labs.overthewire.org -p 2231`  
Password: `CAESARISEASY`

## Task

In this task, we need to decrypt the password for level 4, which is hidden in the file `krypton4`. Alongside this file, we have three additional files (`found1`, `found2`, and `found3`) that contain ciphertext encrypted with the same key. We will use frequency analysis to break the cipher.

## A little bit of Theory

In classical ciphers, a common attack method is **frequency analysis**. In English, some letters appear more frequently than others, with 'E' being the most common. By analyzing the frequencies of letters in the ciphertext, we can make educated guesses about which letters in the ciphertext correspond to common letters in the English language. Once we find the mapping, we can apply it to decrypt the message.

### Steps for Frequency Analysis:

1. **Find the most common letters** in the ciphertext.
2. **Assume that the most frequent letter** corresponds to 'E' (the most frequent letter in English).
3. **Adjust the mapping** for other letters based on their frequency.
4. **Apply the mapping** to decrypt the ciphertext.

## Solution

We begin by examining the files in the current directory:

```bash
krypton3@krypton:/krypton/krypton3$ ls -la
```

We can see the following files:  
`found1`, `found2`, `found3`, `HINT1`, `HINT2`, `krypton4`, and `README`.

### Frequency Analysis

Next, we perform frequency analysis by counting the occurrences of each letter in the three files (`found1`, `found2`, and `found3`).

```bash
krypton3@krypton:/krypton/krypton3$ for i in {A..Z}; do printf $i; cat found1 found2 found3 | tr -cd $i | wc -c; done
```

This will give us the count of each letter in the ciphertext. The output looks like this:

```
A55
B246
C227
D210
E64
F28
G227
H4
I19
J301
K67
L60
M86
N240
O12
P2
Q340
R4
S456
T75
U257
V130
W129
X71
Y84
Z132
```

From this, we see that the letter 'S' appears the most frequently, with 456 occurrences. We will assume that 'S' corresponds to 'E', the most common letter in English.

### Mapping Letters

Now, we map the ciphertext alphabet (`SQJUBNGCDZVWMYTXKELAFIORHP`) to the frequency order of English letters (`ETAOINSRHDLUCMFYWGPBVKXQJZ`).

We can try this substitution using the `tr` command:

```bash
krypton3@krypton:/krypton/krypton3$ cat krypton4 | tr 'SQJUBNGCDZVWMYTXKELAFIORHP' 'EATSORNIHCLDUPYFWGMBKVXQJZ'
```

The output is:

```
WELLD ONETH ELEVE LFOUR PASSW ORDIS BRUTE
```

The decrypted text is now readable, and we can clearly see the password:

**"WELL DONE THE LEVEL FOUR PASSWORD IS BRUTE"**

The password for level 4 is `BRUTE`.

---

### Official Level Link

[Krypton Level 3](https://overthewire.org/wargames/krypton/krypton3.html)
