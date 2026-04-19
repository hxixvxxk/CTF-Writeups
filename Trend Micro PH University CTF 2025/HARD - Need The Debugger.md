<img width="1193" height="743" alt="image" src="https://github.com/user-attachments/assets/33177fb6-57ae-4193-880f-6de8db80c280" />


CTF Name: picoCTF

Challenge Name: DISKO 1

Category: Reverse Engineering

Difficulty: Hard

Challenge Description:

This is a 64-bit Linux commandline program of "Guess The Number". The user needs to guess the correct number after 1 try to see the flag. Debug information has not been stripped, but the C source code is provided. It should run on Debian based systems. MD5 of the file is 2c27201454b4f036435f8f71756166d0"

Hints: (I don't know, I did not reveal the hint for this challenge.)


Tools Used:

    file

    chmod
    
    gdb
    
SOLVING PROCESS:

The description says it is a commandline program, meaning it is an executable that we will have to [chmod](https://www.geeksforgeeks.org/linux-unix/chmod-command-linux/) in order to execute it.

In a nutshell, chmod is a command that modifies the read-write-execute permissions of a file or directory.

It's commonly used to run executable files.

It also gives a hint as to what the game is gonna be about. It'll be your usual guess the number type of game where you give it a number and it'll say whether your guess must be higher or lower.

However, those "higher or lower" hints won't matter because you have to guess it in the first try.

Debugging information has not been stripped, which is great, because we can peek into the file and try to look at the variable used for the correct number immediately after it is assigned.

We can use the gdb command for this, and we can use breakpoints to set kind of "checkpoints" in a specific line of code so we can sort of time-travel, and pause time, right at that exact line.

However,the C source code is not provided. This will make it harder but it's still possible to snoop around and see what we can get.

To quickly learn about the concept of breakpoints and gdb., click [me.](https://www.youtube.com/watch?v=QAIEqITdP5g)

You may find other resources online about this topic, there are plenty. But I find that video to be a decent and quick way to understand what we'll be doing here.

The description also says it will run on [Debian](https://www.debian.org/) which likely means it was written on a Debian-based system.

Let's start

Let's use the file command to look at what we're dealing with.

        ┌──(hellopo㉿rald)-[~/Downloads]
        └─$ file file
        file: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=ee7b29c07ab22bc8fef79833b5bb4beb8af3009f, for GNU/Linux 3.2.0, with debug_info, not stripped

This confirms our suspicion that it is an executable, so let's chmod it so we can play Guess The Number.                                                                                                        

        ┌──(hellopo㉿rald)-[~/Downloads]
        └─$ chmod +x file

Now let's run and play.

        ┌──(hellopo㉿rald)-[~/Downloads]
        └─$ ./file
        Guess a number between 1 and 1000
        500
        Lower number please!
        250
        Higher number please!
        375 
        Higher number please!
        437.5 
        Lower number please!
        Lower number please!
        Lower number please!
        Lower number please!
        Lower number please!
        Lower number please!
        Lower number please!
        Lower number please!
        Lower number please!
        Lower number please!
        Lower number please!
        Lower number please!
        Lower number please!
        
        Limit Reached - You Loose!
        
Okay, I guess we lost the game. Anyway, we failed to guess the number on the first try, so let's look into the info we can get with gdb and try to figure out which breakpoints to set.

                                                                                                    
        ┌──(hellopo㉿rald)-[~/Downloads]
        └─$ gdb ./file
        GNU gdb (Debian 17.1-4) 17.1
        Copyright (C) 2025 Free Software Foundation, Inc.
        License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
        This is free software: you are free to change and redistribute it.
        There is NO WARRANTY, to the extent permitted by law.
        Type "show copying" and "show warranty" for details.
        This GDB was configured as "x86_64-linux-gnu".
        Type "show configuration" for configuration details.
        For bug reporting instructions, please see:
        <https://www.gnu.org/software/gdb/bugs/>.
        Find the GDB manual and other documentation resources online at:
            <http://www.gnu.org/software/gdb/documentation/>.
        
        For help, type "help".
        Type "apropos word" to search for commands related to "word"...
        Reading symbols from ./file...
        (gdb) 



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
