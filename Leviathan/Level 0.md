## Introduction

This is gonna be a series of walkthroughs for the [OverTheWire Wargame Leviathan](https://overthewire.org/wargames/leviathan/). This wargame deals with simple reverse engineering. We will mainly be looking at some easy techniques to ‘crack’ binaries and get the passwords to the next level. It can be played as a beginner and with no programming knowledge. However, it is recommended to first play the [Bandit Wargame](https://overthewire.org/wargames/bandit/) to cover Linux fundamentals. I have already done a full walkthrough for the Bandit wargame. Some of the theory will be explained there and I will assume that it is know to you already.

## Login

SSH: `ssh leviathan0@leviathan.labs.overthewire.org -p 2223`

Password: `leviathan0`

## A little bit of Theory

This war game starts in the same way as Bandit Level 0, by using **SSH** to log into the machine.

## Solution

From the website, we get we can log in **leviathan.labs.overthewire.org** through ssh on port **2223**. With the following credentials:

Username: `leviathan0`

Password: `leviathan0`

```bash
$ ssh leviathan0@leviathan.labs.overthewire.org -p 2223
```

Now we are in the machine and can start with getting access to level 1.

---
### Official Level Link

[Leviathan Level 0](https://overthewire.org/wargames/leviathan/leviathan0.html)
