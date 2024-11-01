## Introduction

This is a walkthrough for the [OverTheWire Wargame Krypton](https://overthewire.org/wargames/krypton/). Krypton is a cryptography-based wargame, and in this level, we will decrypt a message encrypted with the Caesar Cipher. The task involves finding the key to decrypt the ciphertext and retrieve the password for the next level.

## Login

SSH: `ssh krypton2@krypton.labs.overthewire.org -p 2231`  
Password: `ROTTEN`

## Task

This level involves decrypting a message encrypted using the Caesar Cipher. The `encrypt` binary will look for the keyfile in your current working directory. You need to create a working directory in `/tmp` and link the keyfile there. Since the `encrypt` binary runs with setuid `krypton3`, you will also need to give `krypton3` access to your working directory.

## A little bit of Theory

The **Caesar Cipher** is a simple substitution cipher where each letter is shifted by a certain number, known as the key. For example, with key `k=1`, 'A' becomes 'B', 'B' becomes 'C', and so on. To decrypt the message, you need to reverse the process.

The **decryption key** can be calculated by subtracting the encryption key from 26 (the total number of letters in the alphabet). For example, if the encryption key is `12`, the decryption key will be `26 - 12 = 14`.

## Solution

1. Start by creating a temporary working directory in `/tmp` to store files and link the `keyfile.dat`:
    
    ```bash
    krypton2@krypton:/krypton/krypton2$ mktemp -d
    /tmp/tmp.1RfnWl0zk4
    krypton2@krypton:/krypton/krypton2$ cd /tmp/tmp.1RfnWl0zk4
    krypton2@krypton:/tmp/tmp.1RfnWl0zk4$ ln -s /krypton/krypton2/keyfile.dat
    krypton2@krypton:/tmp/tmp.1RfnWl0zk4$ chmod 777 .
    ```
    
2. Create a test file `encrypt.txt` with simple text "AAAAA" to use for encryption:
    
    ```bash
    krypton2@krypton:/tmp/tmp.1RfnWl0zk4$ echo "AAAAA" > encrypt.txt
    ```
    
3. Run the `encrypt` binary to encrypt the file and generate the `ciphertext`:
    
    ```bash
    krypton2@krypton:/tmp/tmp.1RfnWl0zk4$ /krypton/krypton2/encrypt encrypt.txt
    krypton2@krypton:/tmp/tmp.1RfnWl0zk4$ ls -la
    total 32
    drwxrwxrwx  2 krypton2 root      4096 Jul  7 14:14 .
    drwxrws-wt 99 root     root     20480 Jul  7 14:14 ..
    -rw-r--r--  1 krypton3 krypton2     4 Jul  7 14:14 ciphertext
    -rw-r--r--  1 krypton2 krypton2     5 Jul  7 14:13 encrypt.txt
    lrwxrwxrwx  1 krypton2 root        29 Jul  7 14:13 keyfile.dat -> /krypton/krypton2/keyfile.dat
    ```
    
4. Check the contents of the `ciphertext` file:
    
    ```bash
    krypton2@krypton:/tmp/tmp.1RfnWl0zk4$ cat ciphertext
    MMMMM
    ```
    
5. Based on the example, we know the encryption key is the difference between 'A' (1) and 'M' (13). This gives us the encryption key of `12`. To decrypt the message, we calculate the decryption key as `26 - 12 = 14`.
    
6. Use the `tr` command to decrypt the message with the calculated decryption key:
    
    ```bash
    krypton2@krypton:/krypton/krypton2$ cat krypton3 | tr 'A-Za-z' 'O-ZA-No-za-n'
    CAESARISEASY
    ```
    

The password for level 3 is now **"`CAESARISEASY`"**.

---

### Official Level Link

[Krypton Level 2](https://overthewire.org/wargames/krypton/krypton2.html)