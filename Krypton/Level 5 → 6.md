## Introduction

This is a walkthrough for the [OverTheWire Wargame Krypton](https://overthewire.org/wargames/krypton/). Krypton is a cryptography-based wargame, and in this level, we will break a cipher where the key length is unknown. We will use frequency analysis, specifically the Kasiski examination, to determine the key length and decrypt the message.

## Login

SSH: `ssh krypton5@krypton.labs.overthewire.org -p 2231`  
Password: `CLEARTEXT`

## Task

In this task, we are provided with a ciphertext encrypted using a Vigenère cipher, but the key length is unknown. We need to use frequency analysis to determine the key length and then decrypt the message to obtain the password for the next level.

## A little bit of Theory

The **Kasiski examination** is a method used to break polyalphabetic ciphers, like the Vigenère cipher, when the key length is unknown. It works by finding repeated sequences in the ciphertext and calculating the distances between them. These distances often share common divisors, and the greatest common divisor (GCD) of these distances is likely the key length.

### Steps for Kasiski Examination:

1. **Find repeated sequences** in the ciphertext and note the distances between their occurrences.
2. **Calculate the GCD** of these distances.
3. **Use the GCD** to hypothesize the key length.
4. **Use the Vigenère Cipher breaker** with the guessed key length to decrypt the ciphertext.

## Solution

### Step 1: Analyze the Ciphertext

We are provided with the `krypton5` file containing the ciphertext. The first step is to use the Kasiski examination to find repeated sequences and their distances.

### Step 2: Use an Online Tool for the Kasiski Examination

We can use an online tool to automate the Kasiski examination. One such tool is available on [dcode.fr](https://www.dcode.fr/vigenere-cipher), which offers an option to perform the Kasiski examination.

1. **Go to the Vigenère Cipher tool** and paste the ciphertext from the `krypton5` file into the "VIGENERE CIPHERTEXT" field.
2. Select the **"Kasiski Examination"** method to let the tool identify repeated sequences and suggest possible key lengths.
3. Based on the examination results, the tool suggests that the key length is most likely 3, 6, or 9. We can proceed with the key length of 9.

### Step 3: Decrypt the Message

Now that we have narrowed down the key length to 9, we can use the Vigenère Cipher tool to decrypt the message.

1. Go back to the same tool and paste the ciphertext again into the "VIGENERE CIPHERTEXT" field.
2. This time, choose the **"KNOWING THE KEY-LENGTH/SIZE"** option and set the key length to 9.
3. Click the "Decrypt" button to see the decrypted message.

The decrypted message is `RANDOM`

---

### Official Level Link

[Krypton Level 5](https://overthewire.org/wargames/krypton/krypton5.html)
