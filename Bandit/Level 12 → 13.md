
## Overview

In this level, we need to retrieve the password for the next level. The password is stored in a file `data.txt`, which is a hexdump of a file that has been repeatedly compressed. Our task is to reverse the compression and decode the file to find the password.

---

## Challenge

The `data.txt` file is a hexdump of a compressed file. We need to:
1. Revert the hexdump back to its original compressed form.
2. Decompress the file multiple times (gzip, bzip2, and tar) until we reach the final file containing the password.

---
## A little bit of Theory

- `mkdir <path>` can be used to create a new directory.
- `cp <source> <destination>` copies files.
- `mv <source> <destination>` moves or renames files.

  

**Hexdumps** are often used when we want to look at data that cannot be represented in strings and therefore is not readable, so it is easier to look at the hex values. A hexdump has three main columns. The first shows the address, the second the hex representation of the data on that address and the last shows the actual data as strings (with ‘.’ being hex values that cannot be represented as a string). Many hex editors exist just pick the one you like most.

For the command line `xxd` can be used. `xxd <input_file> <output_file>` creates hexdumps. When using the `-r` flag, it reverts the hexdump.

Hexdumps can be used to figure out the type of a file. Each file type has a **magic number/file signature**. You can find [lists](https://en.wikipedia.org/wiki/List_of_file_signatures) with a collection of these different file signatures online. The `file` command, which was introduced in [Level 5](https://mayadevbe.me/posts/overthewire/bandit/level5/) also uses this method (and more beyond that). This is especially important to know because sometimes files might not have the correct or any file ending to identify its type.

  

**Compression** is a method of encoding that aims to reduce the original size of a file without losing information (or only losing as little as possible).

- `gzip` is a command to compress or decompress (`-d`) files. A ‘gzip’ file generally ends with `.gz`.
- `bzip2` is another command which allows for compressing and decompressing (`-d`) files. A ‘bzip2’ file generally ends with `.bz2`.

An **Archive File** is a file that contains one or multiple files and their metadata. This can allow easier portability.

- `tar` is a command that creates archive files (`-cf`). It also allows extracting these files again (`-xf`). A tar archive generally ends with `.tar`.

---
## Solution

I have separated the task into three sub-tasks. Setting up a directory, reverting the hexdump and finally decompressing.

### Create Directory and Move file

The first part of the task is to create a folder and copy the data to make further actions easier.

Reading through the rules given when we ssh into the server, we can also use `mktemp -d` to create a folder with a random name, instead of using `mkdir` and choosing a name. Then we copy the data.txt from the home directory (~) to the created directory in ’tmp’. Because I already moved into the directory I used ‘.’ as a relative path, meaning the file will be copied with the same name into the current working directory. Finally, I used `mv` to rename the data.

```bash
bandit12@bandit:~$ cd /tmp
bandit12@bandit:/tmp$ mktemp -d
/tmp/tmp.W5t1vua6G9
bandit12@bandit:/tmp$ cd /tmp/tmp.W5t1vua6G9
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ cp ~/data.txt .
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ ls
data.txt
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ mv data.txt hexdump_data
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ ls
hexdump_data
```
### Revert hexdump of the file

Looking at the file, we see the format of the data. As stated it is a hexdump. It looks like this:

```bash
$ cat hexdump_data | head
00000000: 1f8b 0808 0650 b45e 0203 6461 7461 322e  .....P.^..data2.
00000010: 6269 6e00 013d 02c2 fd42 5a68 3931 4159  bin..=...BZh91AY
00000020: 2653 598e 4f1c c800 001e 7fff fbf9 7fda  &SY.O...........
00000030: 9e7f 4f76 9fcf fe7d 3fff f67d abde 5e9f  ..Ov...}?..}..^.
00000040: f3fe 9fbf f6f1 feee bfdf a3ff b001 3b1b  ..............;.
00000050: 5481 a1a0 1ea0 1a34 d0d0 001a 68d3 4683  T......4....h.F.
00000060: 4680 0680 0034 1918 4c4d 190c 4000 0001  F....4..LM..@...
00000070: a000 c87a 81a3 464d a8d3 43c5 1068 0346  ...z..FM..C..h.F
00000080: 8343 40d0 3400 0340 66a6 8068 0cd4 f500  .C@.4..@f..h....
00000090: 69ea 6800 0f50 68f2 4d00 680d 06ca 0190  i.h..Ph.M.h.....
```

However, we want to operate on the actual data. Therefore, we revert the hexdump and get the actual data.

```bash
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ xxd -r hexdump_data compressed_data
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ ls
compressed_data  hexdump_data
```

The actual data looks like this when printed to the console:

```bash
$ cat compressed_data | head
P�^data2.bin=��BZh91AY&SY�O����ڞOv���}?��}��^����������ߣ��;����▒4��▒h�F�F��4▒LM
                                                                               @��z��FM��C�hF�C@�4@f��h
4hh�▒=C%�>X▒,�k���1��GY��                                                                              ��i�hPh�Mh
�J�쌑Oϊ��{RBp�Qix�Y�Z!d��j�(�搿ݳ��/��A�#�A��F��0P��v��`�"3�

                                          ��d�bX?��z��2��<��A �n}
5(3A��
      wO�R����6�XS{�
���9?L�P�yB��=z�m?�L�Nt*�7{qP���%"�w9�qm4�� N3�6���K��H䋑[��}!
                                                              d��3A4$��M~�\ɠJ�C�kUƦ\���\�FSN��&=�[��q   \)�$:��H�t&/�(����]��BB9<s ��h=
```

_Note: As mentioned in the theory part, the actual data is not readable the hexdump is way clearer to view._

### Repeatedly decompress

We now need to decompress the data. To figure out what decompression we need to use, look at the first bytes in the hexdump to find the file signature. We can search for these first bytes in a [list of file signatures](https://en.wikipedia.org/wiki/List_of_file_signatures). An alternative would be to use the `find` command.

#### GZIP

For `gzip` compressed files the header is `\x1F\x8B\x08`. Looking at the first line, we see that these are the first bytes of the file.

```bash
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ cat hexdump_data 
00000000: 1f8b 0808 0650 b45e 0203 6461 7461 322e  .....P.^..data2.
```

Now we can add the correct file ending, by renaming the file and decompress the file with `gzip -d`:

```bash
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ mv compressed_data compressed_data.gz
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ ls
compressed_data.gz  hexdump_data
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ gzip -d compressed_data.gz 
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ ls
compressed_data  hexdump_data
```

_Note: For ‘gzip’ renaming the file with the correct ending is necessary for the other commands it doesn’t have to be done._

#### BZIP2

However, the data is still not fully decompressed, so we look at the first bytes again:

```bash
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ xxd compressed_data 
00000000: 425a 6839 3141 5926 5359 8e4f 1cc8 0000  BZh91AY&SY.O....
```

This time we have a different magic number. Quick googling tells us that `BZ` (= ‘425a’) is the file signature for `bzip` and the next two bytes `h` (= ‘68’) indicate the version, in this case, it is version 2. So we can rename the file with the appropriate file ending (.bz2) and decompress it with `bzip2 -d`

```bash
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ mv compressed_data compressed_data.bz2
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ ls
compressed_data.bz2  hexdump_data
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ bzip2 -d compressed_data.bz2
```

#### GZIP

And the file is still compressed. `xxd` shows that it is ‘gzip’ compressed again. So we repeat the previous steps, renaming and decompressing.

```bash
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ mv compressed_data compressed_data.gz
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ gzip -d compressed_data.gz
```

Now the output still doesn’t look right, but we can see some string.

#### Tar archives

Using either `cat compressed_data` or `xxd compressed_data | head` (‘head’ to only get the first 10 lines), we can see the ‘data5.bin’ string, which is a filename.

```bash
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ cat compressed_data 
data5.bin0000644000000000000000000002400013655050006011240 0ustar  rootrootdata6.bin0000644000000000000000000000033613655050006011247 0ustar  rootrootBZh91AY&SY
                                          +
�A��z�<jA��j                               ���Y�@�U��Z��t!ހ��u�  �
            �@ѣ ��!�h▒iM�
 ���BȨ$fz&1*�Ԇf��zG�g}�+�Q�P(f}���@Թ��▒���Tj�1�P�EƮ��ߨ���@Ț��=�s��*���As*Y��!$r��5���Es�]��B@ 0�,
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ xxd compressed_data | head                                                      
00000000: 6461 7461 352e 6269 6e00 0000 0000 0000  data5.bin.......
00000010: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000020: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000030: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000040: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000050: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000060: 0000 0000 3030 3030 3634 3400 3030 3030  ....0000644.0000
00000070: 3030 3000 3030 3030 3030 3000 3030 3030  000.0000000.0000
00000080: 3030 3234 3030 3000 3133 3635 3530 3530  0024000.13655050
00000090: 3030 3600 3031 3132 3430 0020 3000 0000  006.011240. 0...
```

It seems like we now have an archive. So we use `tar` to extract the file:

```bash
andit12@bandit:/tmp/tmp.W5t1vua6G9$ mv compressed_data compressed_data.tar
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ tar -xf compressed_data.tar 
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ ls
compressed_data.tar  data5.bin  hexdump_data
```

Now, ‘data5.bin’ seems to be another archive with a file called ‘data6.bin’. So we extract the file again.

```bash
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ tar -xf data5.bin
```

#### BZIP2

The file ‘data6.bin’ seems to be `bzip2` compressed again.

```bash
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ xxd data6.bin 
00000000: 425a 6839 3141 5926 5359 080c 2b0b 0000  BZh91AY&SY..+...
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ bzip2 -d data6.bin
bzip2: Can't guess original name for data6.bin -- using data6.bin.out
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ ls
compressed_data.tar  data5.bin  data6.bin.out  hexdump_data
```

#### Tar Archive

‘data6.bin.out’ shows another file name ‘data8.bin’ again. So we extract this file.

```bash
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ tar -xf data6.bin.out
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ ls
compressed_data.tar  data5.bin  data6.bin.out  data8.bin  hexdump_data
```

#### GZIP

Finally, we have to do one more decompression with `gzip` and we get a readable file with the password.

```bash
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ xxd data8.bin
00000000: 1f8b 0808 0650 b45e 0203 6461 7461 392e  .....P.^..data9.
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ mv data8.bin data8.gz
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ gzip -d data8.gz
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ ls
compressed_data.tar  data5.bin  data6.bin.out  data8  hexdump_data
bandit12@bandit:/tmp/tmp.W5t1vua6G9$ cat data8
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```


---

### Official Level Link

[Bandit Level 13](https://overthewire.org/wargames/bandit/)

