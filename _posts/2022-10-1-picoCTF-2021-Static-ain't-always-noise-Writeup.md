---
layout: post
---

"Static ain't always noise" is a picoCTF 2021 General Skills puzzle worth 20 points.

## Description

Can you look at the data in this binary: static? This BASH script might help!

## Solving

The binary that they provided is a 64-bit ELF

```
──(kali㉿kali)-[~/Documents/picoctf/staticnoise]
└─$ file static
static: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=7eb9ee1907cc878f15f9949988893b1f0ab1ebdf, not stripped
```

This is the BASH script that they gave. 

```
#!/bin/bash



echo "Attempting disassembly of $1 ..."


#This usage of "objdump" disassembles all (-D) of the first file given by 
#invoker, but only prints out the ".text" section (-j .text) (only section
#that matters in almost any compiled program...

objdump -Dj .text $1 > $1.ltdis.x86_64.txt


#Check that $1.ltdis.x86_64.txt is non-empty
#Continue if it is, otherwise print error and eject

if [ -s "$1.ltdis.x86_64.txt" ]
then
        echo "Disassembly successful! Available at: $1.ltdis.x86_64.txt"

        echo "Ripping strings from binary with file offsets..."
        strings -a -t x $1 > $1.ltdis.strings.txt
        echo "Any strings found in $1 have been written to $1.ltdis.strings.txt with file offset"



else
        echo "Disassembly failed!"
        echo "Usage: ltdis.sh <program-file>"
        echo "Bye!"
fi
```

Looking at this script it looks like I can put the static binary through ltdis.sh to get the strings inside. It output a txt file that contained a big list of strings and I was able to scroll to find the flag

```
238 /lib64/ld-linux-x86-64.so.2
361 libc.so.6
36b puts
370 __cxa_finalize
37f __libc_start_main
391 GLIBC_2.2.5
39d _ITM_deregisterTMCloneTable
3b9 __gmon_start__
3c8 _ITM_registerTMCloneTable
660 AWAVI
667 AUATL
6ba []A\A]A^A_
6e8 Oh hai! Wait what? A flag? Yes, it's around here somewhere!
7c7 ;*3$"
1020 picoCTF{d15a5m_t34s3r_ae0b3ef2}            <-------------------
1040 GCC: (Ubuntu 7.5.0-3ubuntu1~18.04) 7.5.0
1671 crtstuff.c
167c deregister_tm_clones
1691 __do_global_dtors_aux
16a7 completed.7698
16b6 __do_global_dtors_aux_fini_array_entry
16dd frame_dummy
16e9 __frame_dummy_init_array_entry
1708 static.c
1711 __FRAME_END__
171f __init_array_end
1730 _DYNAMIC
```