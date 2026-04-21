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

As a first instinct, let's check the file type of this file.

It says ASCII text, so just as we suspected, it's just a long line of logs that we'll likely need to filter through to find the flag.

However, take note that the flag isn't in piece as per the description, it's all cut up and scattered throughout the logs.

The approach I used for this was to take note of the key characters that are necessary for the flag's anatomy.

What I mean by this is we use the flag format first when grep-ing. For example, picoCTF's format is "picoCTF{?_?_?}"

So, by instinct, we'll grep the words "pico", "CTF", the curly braces, and also the underscores.

Let's [pipe]{https://en.wikipedia.org/wiki/Pipeline_(Unix)} the contents of server.logs into the grep.

Now, we've found the first part of the flag, but it's not complete. To find the next part, we can look for underscores.

We've found the second part, but the last part is missing. Let's look for and ending curly brace to search for the final part.

And now we can piece it all together and get the flag:


Flag:



