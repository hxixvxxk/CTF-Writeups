┌──(hellopo㉿rald)-[~/Downloads]
└─$ gdb file

Reading symbols from file...

(gdb) p (int)number

$1 = 0

(gdb) break *do_guess+64

Breakpoint 1 at 0x1289: file program9.c, line 27.

(gdb) run

Starting program: /home/hellopo/Downloads/file 

[Thread debugging using libthread_db enabled]

Using host libthread_db library "/usr/lib/x86_64-linux-gnu/libthread_db.so.1".


Breakpoint 1, do_guess (N=1000) at program9.c:27

⚠️ warning: 27   program9.c: No such file or directory

(gdb) p (int)number

$2 = 189

(gdb) c

Continuing.

Guess a number between 1 and 1000

189

You guessed the number in 1 attempts!

uCTF{9_KDA#FOAAfVcct1n8}

[Inferior 1 (process 36535) exited normally]

(gdb) 


Flag:

uCTF{9_KDA#FOAAfVcct1n8}
