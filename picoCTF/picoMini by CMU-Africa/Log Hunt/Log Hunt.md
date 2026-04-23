CTF Name: picoCTF

Challenge Name: Log Hunt

Category: General Skills

Difficulty: Easy

Challenge Description:

"Our server seems to be leaking pieces of a secret flag in its logs. The parts are scattered and sometimes repeated. Can you reconstruct the original flag? Download the logs and figure out the full flag from the fragments.

Hints:

You can use grep to filter only matching lines from the log.

Some lines are duplicates; ignore extra occurrences.

Disclaimer: I don't read the hints during my first attempt. The process below is done without knowledge of the hints unless specified.

TOOLS USED:
    
    wget
    
    file
    
    cat 
    
    grep

SOLVING PROCESS:

This problem is a very simple problem regarding ASCII text. The file given to you are just server logs, so this is something that has a looot of texts.

You'll want to approach this with filtering in  mind, specifically using [grep](https://en.wikipedia.org/wiki/Grep) so that you conserve time.

Let's begin by downloading the file. Copy the link address and use the [wget](https://www.gnu.org/software/wget/) command.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ wget https://challenge-files.picoctf.net/c_amiable_citadel/49cec6157142f24a599f4164d5b63322c2494f801390d6f22eb91b3aa592bc66/server.log


As a first instinct, let's check the file type of this file.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ file server.log                         
    server.log: ASCII text

It says ASCII text, so just as we suspected, it's just a long line of logs that we'll likely need to filter through to find the flag.

However, take note that the flag isn't in piece as per the description, it's all cut up and scattered throughout the logs.

The approach I used for this was to take note of the key characters that are necessary for the flag's anatomy.

What I mean by this is we use the flag format first when grep-ing. For example, picoCTF's format is "picoCTF{?_?_?}"

So, by instinct, we'll grep the words "pico", "CTF", the curly braces, and also the underscores.

Let's [pipe]{https://en.wikipedia.org/wiki/Pipeline_(Unix)} the contents of server.logs into the grep.

        ┌──(hellopo㉿rald)-[~/Documents/CTF]
        └─$ cat server.log | grep -i "picoCTF"                         
        [1990-08-09 10:00:10] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 11:04:27] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 11:04:29] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 11:04:37] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 12:19:23] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 12:19:29] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 12:19:32] INFO FLAGPART: picoCTF{us3_

Now, we've found the first part of the flag, but it's not complete. To find the next part, we can look for underscores.

        ┌──(hellopo㉿rald)-[~/Documents/CTF]
        └─$ cat server.log | grep -i "_"      
        [1990-08-09 10:00:10] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 10:02:55] INFO FLAGPART: y0urlinux_
        [1990-08-09 10:05:54] INFO FLAGPART: sk1lls_
        [1990-08-09 10:05:55] INFO FLAGPART: sk1lls_
        [1990-08-09 11:04:27] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 11:04:29] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 11:04:37] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 11:09:16] INFO FLAGPART: y0urlinux_
        [1990-08-09 11:09:19] INFO FLAGPART: y0urlinux_
        [1990-08-09 11:12:40] INFO FLAGPART: sk1lls_
        [1990-08-09 11:12:45] INFO FLAGPART: sk1lls_
        [1990-08-09 12:19:23] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 12:19:29] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 12:19:32] INFO FLAGPART: picoCTF{us3_
        [1990-08-09 12:23:43] INFO FLAGPART: y0urlinux_
        [1990-08-09 12:23:45] INFO FLAGPART: y0urlinux_
        [1990-08-09 12:23:53] INFO FLAGPART: y0urlinux_
        [1990-08-09 12:25:32] INFO FLAGPART: sk1lls_

We've found the second part, we can vaguely make out the sentence "use your linux skills).

However, the last part is missing. Let's look for an ending curly brace to search for the final part.

        ┌──(hellopo㉿rald)-[~/Documents/CTF]
        └─$ cat server.log | grep -i "}"
        [1990-08-09 10:10:54] INFO FLAGPART: cedfa5fb}
        [1990-08-09 10:10:58] INFO FLAGPART: cedfa5fb}
        [1990-08-09 10:11:06] INFO FLAGPART: cedfa5fb}
        [1990-08-09 11:16:58] INFO FLAGPART: cedfa5fb}
        [1990-08-09 11:16:59] INFO FLAGPART: cedfa5fb}
        [1990-08-09 11:17:00] INFO FLAGPART: cedfa5fb}
        [1990-08-09 12:28:45] INFO FLAGPART: cedfa5fb}
        [1990-08-09 12:28:49] INFO FLAGPART: cedfa5fb}
        [1990-08-09 12:28:52] INFO FLAGPART: cedfa5fb}

And now we can piece it all together and get the flag.


Flag:

picoCTF{us3_y0urlinux_sk1lls_cedfa5fb}

