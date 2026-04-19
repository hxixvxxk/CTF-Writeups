<img width="1400" height="561" alt="image" src="https://github.com/user-attachments/assets/06419083-a0db-4230-b0a5-4be5ed59a359" />


<img width="1193" height="743" alt="image" src="https://github.com/user-attachments/assets/33177fb6-57ae-4193-880f-6de8db80c280" />


CTF Name: Trend Micro Philippines University CTF

Challenge Name: Need The Debugger

Category: Reverse Engineering

Difficulty: Hard

Points: 50

Challenge Description:

This is a 64-bit Linux command line program of "Guess The Number". The user needs to guess the correct number after 1 try to see the flag. Debug information has not been stripped, but the C source code is provided. It should run on Debian based systems. MD5 of the file is 2c27201454b4f036435f8f71756166d0"

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

We will be dealing with x86-64 Assembly on Linux. You can read about it [here.](https://p403n1x87.github.io/getting-started-with-x86-64-assembly-on-linux.html)

...and [here](https://youtube.com/playlist?list=PLetF-YjXm-sCH6FrTz4AQhfH6INDQvQSn&si=RDkaLxmEtp99c7-y) if you prefer videos

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

Alright, now that gdb is activated, let's dig in.

Code in C usually starts at the main function. Let's disassemble that function to see what's going on in the assembly.

        (gdb) disass main
        Dump of assembler code for function main:
           0x00005555555553f0 <+0>:     endbr64
           0x00005555555553f4 <+4>:     push   %rbp
           0x00005555555553f5 <+5>:     mov    %rsp,%rbp
           0x00005555555553f8 <+8>:     sub    $0x10,%rsp
        => 0x00005555555553fc <+12>:    movl   $0x3e8,-0x4(%rbp)
           0x0000555555555403 <+19>:    mov    -0x4(%rbp),%eax
           0x0000555555555406 <+22>:    mov    %eax,%edi
           0x0000555555555408 <+24>:    call   0x555555555249 <do_guess>
           0x000055555555540d <+29>:    mov    $0x0,%eax
           0x0000555555555412 <+34>:    leave
           0x0000555555555413 <+35>:    ret
        End of assembler dump.

What we have here is the assembler code. 

Let's dissect the parts of this disassembly before we analyze. Let's take for example the second line.

    0x00005555555553f4 <+4>:     push   %rbp

"0x00005555555553f4" is the Absolute Virtual Address. It represents the exact location in the computer's RAM where this specific instruction is stored while the program is running.

"<+4>" is the offset from the start of the function (main). It means that this instruction is located 4 bytes after main. (since main is what we're disassembling.)

"push" is the assembly instruction. It's hard to explain it simply, but it essentially saves information like a bookmark.

"%rbp" is a register which points to the base of the current stack. 


Anyway, all of the lines follow that anatomy. However, take a look at offset +24.

           0x0000555555555408 <+24>:    call   0x555555555249 <do_guess>

This is a particularly interesting line because it is a calling a function named do_guess. 

Let's disassemble do_guess to see what's inside it. 

        (gdb) disass do_guess
        Dump of assembler code for function do_guess:
           0x0000555555555249 <+0>:     endbr64
           0x000055555555524d <+4>:     push   %rbp
           0x000055555555524e <+5>:     mov    %rsp,%rbp
           0x0000555555555251 <+8>:     sub    $0x50,%rsp
           0x0000555555555255 <+12>:    mov    %edi,-0x44(%rbp)
           0x0000555555555258 <+15>:    mov    %fs:0x28,%rax
           0x0000555555555261 <+24>:    mov    %rax,-0x8(%rbp)
           0x0000555555555265 <+28>:    xor    %eax,%eax
           0x0000555555555267 <+30>:    mov    $0x0,%edi
           0x000055555555526c <+35>:    call   0x555555555140 <time@plt>
           0x0000555555555271 <+40>:    mov    %eax,%edi
           0x0000555555555273 <+42>:    call   0x555555555120 <srand@plt>
           0x0000555555555278 <+47>:    call   0x555555555150 <rand@plt>
           0x000055555555527d <+52>:    cltd
           0x000055555555527e <+53>:    idivl  -0x44(%rbp)
           0x0000555555555281 <+56>:    mov    %edx,%eax
           0x0000555555555283 <+58>:    mov    %eax,0x2d8b(%rip)        # 0x555555558014 <number>
        --Type <RET> for more, q to quit, c to continue without paging--
           0x0000555555555289 <+64>:    mov    -0x44(%rbp),%eax

Now, we've disassembled the do_guess function.

Notably, at offset +58, we see a comment that states an address along with its name, "<number>"

This is interesting because we're looking for a certain number, and the assembly instruction for this line is "mov" which means move. 

It stores information in a variable, in this case, it's the "<number>"

This is likely the number that the program generated. We'd want to set a breakpoint here so that we can run the program from this specific line.

However, you have to be careful. If we break at <+58>, <number> wouldn't have been assigned a value yet.

We'll break the immediate line AFTER <+58>, which is <+64>

Let's first quit the disassembly line.

           0x00005555555552d5 <+140>:   mov    $0x0,%eax
        --Type <RET> for more, q to quit, c to continue without paging--q
        ❌️ Quit
        (gdb) 


And now let's set the breakpoint.

        (gdb) break *do_guess+64
        Breakpoint 2 at 0x555555555289: file program9.c, line 27.
        (gdb) 

Now, visualize that we are at the exact part of the code right after the correct number was generated.

Let's run the program, and then print the number we get.

        (gdb) run
        The program being debugged has been started already.
        Start it from the beginning? (y or n) y
        Starting program: /home/hellopo/Downloads/file 
        [Thread debugging using libthread_db enabled]
        Using host libthread_db library "/usr/lib/x86_64-linux-gnu/libthread_db.so.1".
        
        Breakpoint 2, do_guess (N=1000) at program9.c:27
        27      in program9.c

Now we are currently at line 27 of program9.c (the C code that we're reading the instructions of)
        
        (gdb) print number
        $2 = 608
        (gdb) 

The number we get is 608. Now let's continue the program so that we can plug it in.

        (gdb) print number
        $2 = 608
        (gdb) continue
        Continuing.
        Guess a number between 1 and 1000
        608
        You guessed the number in 1 attempts!
        uCTF{9_KDA#FOAAfVcct1n8}
        [Inferior 1 (process 38318) exited normally]
        (gdb) 

We correctly guessed the number in the first attempt and we are given the flag.

To the teams who managed to solve this problem last year, you guys are pretty crazy for this. Who studies assembly??

Good job!

Flag:

uCTF{9_KDA#FOAAfVcct1n8}
