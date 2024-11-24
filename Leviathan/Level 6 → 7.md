## Login

SSH: `ssh leviathan6@leviathan.labs.overthewire.org -p 2223`

Password: `szo7HDB88w`

## A little bit of Theory

This level includes a lot of new things, that will be too much, so I will stick to explaining the very basics needed. And you should look more into Assembly and gdb if you are interested - I will link some resources that were helpful for me.

**Assembly language** is a low-level programming language, it is between machine code (hard to read for humans) and high-level programming languages (’easy’ to read for humans). There are different types of assembly, namely [Intel and AT&T Syntax](https://imada.sdu.dk/u/kslarsen/dm546/Material/IntelnATT.htm). In this walkthrough, you will see the AT&T Syntax. Check out [TryHackMe Win64 Assembly](https://tryhackme.com/room/win64assembly) for the basics of reverse engineering and assembly.

Let’s quickly look over the structure of a line of assembly code: `instr source,dest`. A line starts with an instruction. Followed by a source and destination (for the AT&T syntax). There is a certain set of instructions (based on the machine code), that can be chosen from. Very common ones are for example jumps (‘je’=‘jump if equal’) or the compare (‘cmp’) instruction. The source and destination can - depending on the instruction - be a hard-coded value, a register or an address.

_Examples:_ `add $0x4,%eax`: here the instruction is ‘add’, the source is the value ‘0x4’ and the destination is the ’eax’ register. `mov (%eax),%eax`: here the instruction is ‘mov’ (move), the source is the value saved at the ‘address’ saved in eax and the destination is again the ’eax’ register.

I highly recommend that you understand and do research on concepts like memory, address and the different types of registers etc. to understand this walkthrough. Maybe check out [Tutorialspoint Assembly Programming Tutorial](https://www.tutorialspoint.com/assembly_programming/index.htm) or other resources.

**GDB** ([GNU Debugger](https://www.gnu.org/savannah-checkouts/gnu/gdb/index.html)) is a debugger for Linux. It can be used for reverse engineering. There are however also programs, like [Ghidra](https://ghidra-sre.org/), which are designed specifically for reverse engineering. There are also plugins for GDB, which make debugging and/or reverse engineering easier like [PWNDBG](https://github.com/pwndbg/pwndbg).

GDB has a lot of commands, let’s take a look at the most important ones:

- To run GDB with a program to debug and parameter input use: `gdb --args program_name params`.
- Use `disassemble func_name`, to get the assembly code of a specific function.
- `break *hex_address` to set a breakpoint at a specific part of the code
- Use `c` to continue after a breakpoint
- `run` starts the program
- `info registers` prints the current values of all registers
- `x hex_address` allows you to print the value saved at the address _If you google ‘gdb cheat sheet’ you will find even more helpful commands._

## Solution

The home folder contains a file called ’leviathan6’, which takes a 4-digit code as input.

```bash
leviathan6@leviathan:~$ ls -la
total 28
drwxr-xr-x  2 root       root       4096 Aug 26  2019 .
drwxr-xr-x 10 root       root       4096 Aug 26  2019 ..
-rw-r--r--  1 root       root        220 May 15  2017 .bash_logout
-rw-r--r--  1 root       root       3526 May 15  2017 .bashrc
-r-sr-x---  1 leviathan7 leviathan6 7452 Aug 26  2019 leviathan6
-rw-r--r--  1 root       root        675 May 15  2017 .profile
leviathan6@leviathan:~$ ./leviathan6 
usage: ./leviathan6 <4 digit code>
leviathan6@leviathan:~$ ./leviathan6 0000
Wrong
```

Using the `strings` or `ltrace` command from previous levels does not show us the solution. One way to solve this is to try brute-force to get the correct 4-digit code - this could be done manually or with a simple script. However, that would be rather boring (it could look something like this: `for i in {0000..9999}; do ./leviathan6 $i; done`).

Instead, we can get a little more into reverse engineering and look into the code with gdb.

First, we start it:

```bash
gdb --args leviathan6 0000
```

Then we look at the assembly code of the main function. We need to find the line where the digits are compared:

```bash
(gdb) disassemble main
Dump of assembler code for function main:
   0x080491d6 <+0>:     lea    0x4(%esp),%ecx
   0x080491da <+4>:     and    $0xfffffff0,%esp
   0x080491dd <+7>:     push   -0x4(%ecx)
   0x080491e0 <+10>:    push   %ebp
   0x080491e1 <+11>:    mov    %esp,%ebp
   0x080491e3 <+13>:    push   %ebx
   0x080491e4 <+14>:    push   %ecx
   0x080491e5 <+15>:    sub    $0x10,%esp
   0x080491e8 <+18>:    mov    %ecx,%eax
   0x080491ea <+20>:    movl   $0x1bd3,-0xc(%ebp)
   0x080491f1 <+27>:    cmpl   $0x2,(%eax)
   0x080491f4 <+30>:    je     0x8049216 <main+64>
   0x080491f6 <+32>:    mov    0x4(%eax),%eax
   0x080491f9 <+35>:    mov    (%eax),%eax
   0x080491fb <+37>:    sub    $0x8,%esp
   0x080491fe <+40>:    push   %eax
   0x080491ff <+41>:    push   $0x804a008
   0x08049204 <+46>:    call   0x8049050 <printf@plt>
   0x08049209 <+51>:    add    $0x10,%esp
   0x0804920c <+54>:    sub    $0xc,%esp
   0x0804920f <+57>:    push   $0xffffffff
   0x08049211 <+59>:    call   0x8049090 <exit@plt>
   0x08049216 <+64>:    mov    0x4(%eax),%eax
   0x08049219 <+67>:    add    $0x4,%eax
   0x0804921c <+70>:    mov    (%eax),%eax
   0x0804921e <+72>:    sub    $0xc,%esp
   0x08049221 <+75>:    push   %eax
   0x08049222 <+76>:    call   0x80490b0 <atoi@plt>
   0x08049227 <+81>:    add    $0x10,%esp
   0x0804922a <+84>:    cmp    %eax,-0xc(%ebp)
   0x0804922d <+87>:    jne    0x804925a <main+132>
   0x0804922f <+89>:    call   0x8049060 <geteuid@plt>
   0x08049234 <+94>:    mov    %eax,%ebx
   0x08049236 <+96>:    call   0x8049060 <geteuid@plt>
   0x0804923b <+101>:   sub    $0x8,%esp
   0x0804923e <+104>:   push   %ebx
   0x0804923f <+105>:   push   %eax
   0x08049240 <+106>:   call   0x80490a0 <setreuid@plt>
   0x08049245 <+111>:   add    $0x10,%esp
   0x08049248 <+114>:   sub    $0xc,%esp
   0x0804924b <+117>:   push   $0x804a022
   0x08049250 <+122>:   call   0x8049080 <system@plt>
   0x08049255 <+127>:   add    $0x10,%esp
   0x08049258 <+130>:   jmp    0x804926a <main+148>
   0x0804925a <+132>:   sub    $0xc,%esp
   0x0804925d <+135>:   push   $0x804a02a
   0x08049262 <+140>:   call   0x8049070 <puts@plt>
   0x08049267 <+145>:   add    $0x10,%esp
   0x0804926a <+148>:   mov    $0x0,%eax
   0x0804926f <+153>:   lea    -0x8(%ebp),%esp
   0x08049272 <+156>:   pop    %ecx
   0x08049273 <+157>:   pop    %ebx
--Type <RET> for more, q to quit, c to continue without paging-- c
   0x08049274 <+158>:   pop    %ebp
   0x08049275 <+159>:   lea    -0x4(%ecx),%esp
   0x08049278 <+162>:   ret    
End of assembler dump.
```

Looking at the code, we can see line ‘+106’ calls ‘setreuid@plt’ and line ‘122’ calls ‘system@plt’. The names sound, like the user id, is changed and a shell command is executed. We can check this assumption, by looking into the [Linux Manual Page](https://man7.org/linux/man-pages/man2/setreuid.2.html). We find this is exactly, what is happening. So now we just need to follow the program flow, to see, how we get to this part of the code. We will find that the jump in line ‘+87’ would lead to skipping that part because it jumps to line ‘+132’. This must be, where the code is checked and if it is not equal, it skips the for us important part. another way to see this is the line ‘+76’, where the ‘atoi’ function is called (converts string to integer - assume it’s our input). So we need to check what is compared (line ‘+84’).

```bash
(gdb) break *0x0804922a
Breakpoint 1 at 0x804922a
(gdb) run
Starting program: /home/leviathan6/leviathan6 0000
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, 0x0804922a in main ()
(gdb) info registers
eax            0x0                 0
ecx            0x0                 0
edx            0xffffd71e          -10466
ebx            0xf7fab000          -134565888
esp            0xffffd4c0          0xffffd4c0
ebp            0xffffd4d8          0xffffd4d8
esi            0xffffd5a4          -10844
edi            0xf7ffcb80          -134231168
eip            0x804922a           0x804922a <main+84>
eflags         0x286               [ PF SF IF ]
cs             0x23                35
ss             0x2b                43
ds             0x2b                43
es             0x2b                43
fs             0x0                 0
gs             0x63                99
k0             0x0                 0
k1             0x0                 0
k2             0x0                 0
k3             0x0                 0
k4             0x0                 0
k5             0x0                 0
k6             0x0                 0
k7             0x0                 0
```

So looking at the line `0x0804922a <+84>: cmp %eax,-0xc(%ebp)`, we compare the value in the register ’eax’, which is `0`, so our initial input, with what is stored on the address in register ’ebp’ minus `0xc`.

First, we calculate the address, and then we look at what is saved at that address in decimal:

```bash
(gdb) print $ebp-0xc
$1 = (void *) 0xffffd4cc
(gdb) x 0xffffd4cc
0xffffd4cc:     0x00001bd3
0xffffd4cc:     0x00001bd3
(gdb) print/d 0x00001bd3
$3 = 7123
```

And we got a 4-digit number, now we just need to test, if it is the correct one.

```bash
leviathan6@leviathan:~$ ./leviathan6 7123
$ whoami
leviathan7
$ cat /etc/leviathan_pass/leviathan7
qEs5Io5yM8
```

We got the right code and a shell, as leviathan7. So we can read the password file.

## Addition

Since this is the last level (as of this writing), we should test the last password:

```bash
$ ssh leviathan7@leviathan.labs.overthewire.org -p 2223
leviathan7@leviathan:~$ ls -la
total 24
drwxr-xr-x  2 root       root       4096 Aug 26  2019 .
drwxr-xr-x 10 root       root       4096 Aug 26  2019 ..
-rw-r--r--  1 root       root        220 May 15  2017 .bash_logout
-rw-r--r--  1 root       root       3526 May 15  2017 .bashrc
-r--r-----  1 leviathan7 leviathan7  178 Aug 26  2019 CONGRATULATIONS
-rw-r--r--  1 root       root        675 May 15  2017 .profile
leviathan7@leviathan:~$ cat CONGRATULATIONS 
...
```
---
### Official Level Link

[Leviathan Level 7](https://overthewire.org/wargames/leviathan/leviathan7.html)