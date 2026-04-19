<img width="1193" height="743" alt="image" src="https://github.com/user-attachments/assets/33177fb6-57ae-4193-880f-6de8db80c280" />


CTF Name: picoCTF

Challenge Name: DISKO 1

Category: Reverse Engineering

Difficulty: Hard

Challenge Description:

This is a 64-bit Linux commandline program of "Guess The Number". The user needs to guess the correct number after 1 try to see the flag. Debug information has not been stripped, but the C source code is provided. It should run on Debian based systems. MD5 of the file is 2c27201454b4f036435f8f71756166d0"

Hints: (I don't know, I did not reveal the hint for this challenge.)


Tools Used:

    chmod
    
    gdb
    
SOLVING PROCESS:



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
