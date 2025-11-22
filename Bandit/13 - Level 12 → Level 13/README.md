# Bandit - Write-Up
# Level 12 → Level 13

## Overview

Work with a hexdump file that has been compressed multiple times.

**Lab Link**: https://overthewire.org/wargames/bandit/bandit13.html

---

## Step 1 — Understanding the Challenge

<img width="2364" height="288" alt="image" src="https://github.com/user-attachments/assets/87294aa7-c340-4a32-995f-74d0001ea97c" />

```bash
ssh bandit12@bandit.labs.overthewire.org -p 2220
```

Password captured from the last level: 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4

---

## Step 2 — Implementation

### Check the type of data.txt file in the main directory, then check its content.
```bash
bandit12@bandit:~$ ls
data.txt
bandit12@bandit:~$ file data.txt 
data.txt: ASCII text
bandit12@bandit:~$ cat data.txt 
00000000: 1f8b 0808 2817 ee68 0203 6461 7461 322e  ....(..h..data2.
00000010: 6269 6e00 013c 02c3 fd42 5a68 3931 4159  bin..<...BZh91AY
00000020: 2653 59cc 46b5 2d00 0018 ffff da5f e6e3  &SY.F.-......_..
00000030: 9fcd f59d bc69 ddd7 f7ff a7e7 dbdd b59f  .....i..........
00000040: fff7 cfdd ffbf bbdf ffff ff5e b001 3b58  ...........^..;X
00000050: 2406 8000 00d0 6834 6234 d000 6869 9000  $.....h4b4..hi..
00000060: 1a7a 8003 40d0 01a1 a006 8188 340d 1a68  .z..@.......4..h
...
...
...
```

This is a hexdump file, let's try change its name and extension.

```bash
bandit12@bandit:~$ mv data.txt data
mv: cannot move 'data.txt' to 'data': Permission denied
```

We do not have permission, so let's create a directory inside /tmp and copy data.txt to that temporary directory without extension.

```bash
bandit12@bandit:~$ mkdir /tmp/tdir
bandit12@bandit:~$ cp data.txt /tmp/tdir/data.txt
bandit12@bandit:~$ ls /tmp/tdir
data.txt
```

We have our file on a temp directory, now its format is txt, we need to return it to binary.

We can seach for some usefull commands.

```bash
bandit12@bandit:~$ apropos hex
...
...
...
xxd (1)              - make a hex dump or do the reverse.
```

Here is our man! "xxd" command is intersted, let's check its help page.

```bash
bandit12@bandit:~$ xxd --help
Usage:
       xxd [options] [infile [outfile]]
...
...
...
    -r          reverse operation: convert (or patch) hexdump into binary.
...
...
```

-r is the required argument, let's convert the file into binary.

```bash
bandit12@bandit:~$ cd /tmp/tdir
bandit12@bandit:/tmp/tdir$ xxd -r data.txt data
bandit12@bandit:/tmp/tdir$ ls
data  data.txt
bandit12@bandit:/tmp/tdir$ file data
data: gzip compressed data, was "data2.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 572
```

Checked the file type, it's a gzip compressed file, so we need to convert the file into gz.

```bash
bandit12@bandit:/tmp/tdir$ mv data data1.gz
```

Now, check the gzip help page to get the argument of decompressing.

```bash
bandit12@bandit:/tmp/tdir$ whatis gzip
gzip (1)             - compress or expand files
bandit12@bandit:/tmp/tdir$ gzip --help | grep decompress
  -d, --decompress  decompress
```

We can decompress the file using gzip -d, let's do it then check the new file type.

```bash
bandit12@bandit:/tmp/tdir$ gzip -d data1.gz
bandit12@bandit:/tmp/tdir$ file data1
data1: bzip2 compressed data, block size = 900k
```

Now we have a type of bzip2, let's do the same.

```bash
bandit12@bandit:/tmp/tdir$ mv data1 data2.bz
bandit12@bandit:/tmp/tdir$ whatis bzip2
bzip2 (1)            - a block-sorting file compressor, v1.0.8
bandit12@bandit:/tmp/tdir$ bzip2 --help | grep decompress
   usage: bzip2 [flags and input files in any order]

   -h --help           print this message
   -d --decompress     force decompression
...
...
...
bandit12@bandit:/tmp/tdir$ bzip2 -d data2.bz
bandit12@bandit:/tmp/tdir$ ls
data2  data.txt
bandit12@bandit:/tmp/tdir$ file data2
data2: gzip compressed data, was "data4.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 20480
```

Another gzip2! Let's do it.

```bash
bandit12@bandit:/tmp/tdir$ mv data2 data3.gz
bandit12@bandit:/tmp/tdir$ gzip -d data3.gz 
bandit12@bandit:/tmp/tdir$ ls
data3  data.txt
bandit12@bandit:/tmp/tdir$ file data3
data3: POSIX tar archive (GNU)
```

It's POSIX tar, let's discover the "tar" command.
```bash
bandit12@bandit:/tmp/tdir$ apropos archiving
tar (1)              - an archiving utility
bandit12@bandit:/tmp/tdir$ tar --help | grep extract
  -x, --extract, --get       extract files from an archive
...
...
...
bandit12@bandit:/tmp/tdir$ tar --help | grep "\-f"
...
...
...
  -f, --file=ARCHIVE         use archive file or device ARCHIVE
...
...
...
bandit12@bandit:/tmp/tdir$ tar -x -f data3.tar
bandit12@bandit:/tmp/tdir$ ls
data3.tar  data5.bin  data.txt
```

Mmm, we got a data5.bin file, let's check it and do the same de-archiving and decompressing if needed.

```bash
bandit12@bandit:/tmp/tdir$ file data5.bin 
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/tdir$ mv data5.bin data5.tar
bandit12@bandit:/tmp/tdir$ tar -x -f data5.tar 
bandit12@bandit:/tmp/tdir$ ls
data3.tar  data5.tar  data6.bin  data.txt
bandit12@bandit:/tmp/tdir$ file data6.bin 
data6.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/tdir$ mv data6.bin data6.bz
bandit12@bandit:/tmp/tdir$ bzip2 -d data6.bz
bandit12@bandit:/tmp/tdir$ ls
data3.tar  data5.tar  data6  data.txt
bandit12@bandit:/tmp/tdir$ file data6
data6: POSIX tar archive (GNU)
bandit12@bandit:/tmp/tdir$ mv data6 data6.tar
bandit12@bandit:/tmp/tdir$ tar -x -f data6.tar
bandit12@bandit:/tmp/tdir$ ls
data3.tar  data5.tar  data6.tar  data8.bin  data.txt
bandit12@bandit:/tmp/tdir$ file data8.bin 
data8.bin: gzip compressed data, was "data9.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 49
bandit12@bandit:/tmp/tdir$ mv data8.bin data8.gz
bandit12@bandit:/tmp/tdir$ gzip -d data8.gz 
bandit12@bandit:/tmp/tdir$ ls
data3.tar  data5.tar  data6.tar  data8  data.txt
bandit12@bandit:/tmp/tdir$ file data8
data8: ASCII text
bandit12@bandit:/tmp/tdir$ cat data8
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```

What a long but juicy process! We finally found the password for the next level!

password: FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn

<img width="2100" height="1074" alt="image" src="https://github.com/user-attachments/assets/0b13c12e-d082-486d-b7b3-3ac090660e33" />

### Well Done! :)
