## Introduction

This is gonna be a series of walkthroughs for the [OverTheWire Wargame Krypton](https://overthewire.org/wargames/krypton/). This wargame deals with cryptography. We will mainly be looking at some easy encodings and ciphers. It can be played as a beginner and either public tools can be used, or you can program your own.

## Login

SSH: `not needed`

## Task

Welcome to Krypton! The first level is easy. The following string encodes the password using Base64:

```bash
	S1JZUFRPTklTR1JFQVQ=
```
## A little bit of Theory

**Base64** is a binary-to-text encoding scheme. Very often recognizable by ‘=’ signs at the end. Six bits are encoded into one of 63 different characters, with the equal sign being used for padding ([Base64 Table](https://en.wikipedia.org/wiki/Base64#Base64_table_from_RFC_4648)). There are different tools that allow you to easily encode and decode Base64, such as [Online Tool](https://www.base64decode.org/), [CyberChef](https://gchq.github.io/CyberChef/) or the command `base64`.

## Solution

I chose the command-line option to decode the password. The command `base64` can encode and decode base64. It either takes a file or encodes/decodes the standard input. I used this command to decode the string.

```bash
$ echo S1JZUFRPTklTR1JFQVQ= | base64 -d
KRYPTONISGREAT
```
---
### Official Level Link

[Krypton Level 0](https://overthewire.org/wargames/krypton/krypton0.html)
