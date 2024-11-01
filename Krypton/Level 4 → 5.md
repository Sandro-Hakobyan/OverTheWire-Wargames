## Introduction

This is a walkthrough for the [OverTheWire Wargame Krypton](https://overthewire.org/wargames/krypton/). Krypton is a cryptography-based wargame, and in this level, we will break a cipher using a known key length. The goal is to decrypt the intercepted ciphertext and uncover the password for the next level.

## Login

SSH: `ssh krypton4@krypton.labs.overthewire.org -p 2231`  
Password: `BRUTE`

## Task

In this task, we need to decrypt a message encrypted with the Vigenère Cipher. We know that the key length is 6, which gives us a clue about how the cipher is structured. We are provided with two ciphertexts: one in the `found1` file and another in the `krypton5` file. The key used to encrypt both is the same, and we need to find it to decrypt the messages.

## A little bit of Theory

The **Vigenère Cipher** is a method of encrypting alphabetic text by using a simple form of polyalphabetic substitution. A key (usually a word or phrase) is used to determine the shifts in the alphabet for each letter in the plaintext.

In our case:

- We know the key length is 6, meaning the key repeats every 6 characters.
- The Vigenère Cipher uses the key to shift each letter of the plaintext by a corresponding value in the key.

### Steps to Decrypt:

1. **Analyze the ciphertext** to determine which letter corresponds to which shift in the key.
2. **Break the ciphertext** into segments corresponding to the key length (6 characters in this case).
3. **Use frequency analysis** to identify the most likely shifts for the key.
4. **Decrypt the ciphertext** using the identified key.

## Solution

### Step 1: Break the Ciphertext into Segments

We are provided with the `found1` file, which contains a ciphertext encrypted with a Vigenère cipher. Since we know the key length is 6, we can divide the ciphertext into 6 columns based on the key length.

### Step 2: Use an Online Vigenère Cipher Breaker

We can use an online tool to automate the decryption process. A simple search for "Vigenère cipher breaker" leads us to [dcode.fr's Vigenère Cipher tool](https://www.dcode.fr/vigenere-cipher). Here are the steps:

1. **Go to the Vigenère Cipher tool** and paste the ciphertext from the `found1` file into the "VIGENERE CIPHERTEXT" field.
2. **Select "KNOWING THE KEY-LENGTH/SIZE, NUMBER OF LETTERS"** and set the key length to 6.
3. Click the "Decrypt" button to retrieve the key.

The tool will output the key, which is `FREKEY`.

### Step 3: Decrypt the Second Message

Now that we have the key, we can use it to decrypt the second ciphertext in the `krypton5` file.

1. Go to the same Vigenère Cipher tool.
2. Paste the ciphertext from the `krypton5` file into the "VIGENERE CIPHERTEXT" field.
3. This time, choose the option "KNOWING THE KEY/PASSWORD", and enter the key we just found (`FREKEY`).
4. Click "Decrypt" to see the decrypted message.

The decrypted message is `CLEARTEXT`.

---

### Official Level Link

[Krypton Level 4](https://overthewire.org/wargames/krypton/krypton4.html)
