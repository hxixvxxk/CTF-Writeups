CTF Name: picoCTF

Challenge Name: Flag in Flame.

Category: Forensics

Difficulty: Easy

Challenge Description: "The SOC team discovered a suspiciously large log file after a recent breach. When they opened it, they found an enormous block of encoded text instead of typical logs. Could there be something hidden within? Your mission is to inspect the resulting file and reveal the real purpose of it. The team is relying on your skills to uncover any concealed information within this unusual log.
Download the encoded data here: Logs Data. Be prepared—the file is large, and examining it thoroughly is crucial ."

Hints: Use base64 to decode the data and generate the image file.

Disclaimer: I don't read the hints during my first attempt. The process below is done without knowledge of the hints unless specified.


SOLVING PROCESS:

There isn't much useful information in the description, but we can infer that the log file will definitely be decoded in some way to uncover the flag.

Let's begin by downloading the file with wget 

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ wget https://challenge-files.picoctf.net/c_amiable_citadel/929daf6ef01bba32b165e0a7c649ff4c953f2af21c28b024e8af5276b7716de5/logs.txt
    --2026-04-13 09:07:19--  https://challenge-files.picoctf.net/c_amiable_citadel/929daf6ef01bba32b165e0a7c649ff4c953f2af21c28b024e8af5276b7716de5/logs.txt
    Resolving challenge-files.picoctf.net (challenge-files.picoctf.net)... 3.165.63.115, 3.165.63.36, 3.165.63.56, ...
    Connecting to challenge-files.picoctf.net (challenge-files.picoctf.net)|3.165.63.115|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 1592256 (1.5M) [application/octet-stream]
    Saving to: ‘logs.txt’
    
    logs.txt.1               100%[=================================>]   1.52M  22.6KB/s    in 1m 53s  
    
    2026-04-13 09:09:18 (13.7 KB/s) - ‘logs.txt.1’ saved [1592256/1592256]

Now, "file" to discover what kind of file it really is.

It's obviously a text file based on the extension but I just do this as part of my routine to be really sure because sometimes, things aren't what they seem.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ file logs.txt
    logs.txt: ASCII text, with very long lines (65536), with no line terminators


And we discover it's a long bunch of ASCII text, but pay attention to the line count

65536 is a number you'll want to keep in your mind. It is a perfect square and is exactly 2^16.

This usually means that if it's some sort of binary or encypted text, it could potentially be an image.

Let's cat the file and see what's inside.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ cat logs.txt
    iVBORw0KGgoAAAANSUhEUgAAA4AAAASACAIAAAAh8bSOAAEAAElEQVR4nOz919MsyZUniP3OcY+IFJ+6ouqWBqoautDYRmN6emfaZmlc0mi2+7xPNJJ/GJ/4RvJtH5dmQ1vbHu4Mp9kzj

This is just a snippet of the very very long ASCII text after cat-ing it.

It looks a lot like base64, so I'm going to try to echo this into base64 -d and output as a file

The reason for this is so that I can use the "file" command to check what kind of file it is once decrypted.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ base64 -d logs.txt > logsfile
                                                                                                       
    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ file logsfile
    logsfile: PNG image data, 896 x 1152, 8-bit/color RGB, non-interlaced

This is a combo I've discovered recently and it's useful for me.

Well, now we know it's actually a PNG; we can now output it with the .png extension and then view the image.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ base64 -d logs.txt > logsfile.png
                                                                                                       
    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ xdg-open logsfile.png

We are met with this image:

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f0fcd86d-89fd-40e6-b45d-f4d9b07b978c" />

This is a highly zoomed in version of the image, because there is a line of text that we can try to decode.


I will use an image to text website to quickly extract the text and avoid wasting time using:

https://www.imagetotext.info/


<img width="1474" height="362" alt="image" src="https://github.com/user-attachments/assets/ed0da289-66a0-469a-b9b8-fe140a93d64e" />


(I had to crop the image because it failed to catch the text at first, but here we get the text to copy and paste)

In hindsight, you could proably just type out the text if you're confident in your typing skillz.

    7069636F4354467B666F72656E736963735F616E616C797369735F69735F616D617A696E675F65633139383466637D

My pattern recognition says this is hex code, so let's decode the hex in the terminal used the "xxd" command.

The xxd command is a powerful Linux utility used to create hexadecimal dumps of binary files or standard input. Unlike many other dump tools, it is bidirectional, meaning it can also convert those hex dumps back into their original binary form.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ echo "7069636F4354467B666F72656E736963735F616E616C797369735F69735F616D617A696E675F65633139383466637D" | xxd -r
                                                                                                       
Now this is something to watch out for: the "-r" flag reverses the default function of "xxd", meaning it will take hex and decode. 

However, by default, it expects to see the proper hex formatting which includes the address/offset, hex, and the ASCII tail.

That will look something like this:

00000000: 7069 636f ...

Since what we echoed is just in plaintext hex, "xxd" won't see the address and stuff that it expects to, and won't output anything.

Thus, we need the "-p" flag to tell the command that it can take in just plain hex.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ echo "7069636F4354467B666F72656E736963735F616E616C797369735F69735F616D617A696E675F65633139383466637D" | xxd -r -p
    picoCTF{forensics_analysis_is_amazing_ec1984fc}

There we have it.

Flag:

picoCTF{forensics_analysis_is_amazing_ec1984fc}




