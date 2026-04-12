CTF Name: picoCTF

Challenge Name: Corrupted file

Category: Forensics

Difficulty: Easy

Challenge Description: "This file seems broken... or is it? Maybe a couple of bytes could make all the difference. Can you figure out how to bring it back to life?
Download the file here."

Hints: 

Try checking the file’s header. 

JPEG. 

Tools like xxd or hexdump can help you inspect and edit file bytes.

Disclaimer: I don't read the hints during my first attempt. The process below is done without knowledge of the hints unless specified.

SOLVING PROCESS:

Based on the descriptions, we should put our focus on the first few bytes of the file called the magic numbers/bytes. These help determine what file type a certain file is, and it seems like this part of the file is broken.

Let's start.

Download the file by using the "wget" command followed by the copied file link (from the picoCTF site).

    ┌──(hellopo㉿rald)-[~]
    └─$ wget https://challenge-files.picoctf.net/c_amiable_citadel/d5cf66acaae23a2634256d69988d9a77ff0dade995dc28432dc35e788699ea69/file
    --2026-04-12 11:17:09--  https://challenge-files.picoctf.net/c_amiable_citadel/d5cf66acaae23a2634256d69988d9a77ff0dade995dc28432dc35e788699ea69/file
    Resolving challenge-files.picoctf.net (challenge-files.picoctf.net)... 3.165.63.36, 3.165.63.115, 3.165.63.56, ...
    Connecting to challenge-files.picoctf.net (challenge-files.picoctf.net)|3.165.63.36|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 8745 (8.5K) [application/octet-stream]
    Saving to: ‘file’
    
    file                     100%[=================================>]   8.54K  --.-KB/s    in 0s      
    
    2026-04-12 11:17:11 (128 MB/s) - ‘file’ saved [8745/8745]


The file is called 'file'. Let's find out what kind of file it is.

    
    ┌──(hellopo㉿rald)-[~]
    └─$ file file
    file: data

It only says "data" as the result. Based on my knowledge, the best assumption here is that the magic bytes of this file are corrupted and need to be edited.

In any case, let's see what other stuff we can find out about this file and just experiment.

Let's run strings to see what human-readable stuff we can find.

    ┌──(hellopo㉿rald)-[~]
    └─$ strings file 
    JFIF
     $.' ",#
    (7),01444
    '9=82<.342
    !22222222222222222222222222222222222222222222222222
    $3br
    %&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
            #3R
    &'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
    <Sqaa{w&
    neS=
    T ^f
    (ajI
    toQ\
    so%z
    J#5-
    )`*=
    OFT'(>h
    *)rG
    Y+9?
    BW8b
    }uqu
    ow-{
    knwy
    efw]
    om,2;I{<
    iQG${
    
The first line says JFIF, which is a massive clue that it's a JPEG image.

A JFIF (JPEG File Interchange Format) file is a image format based on JPEG compression, often encountered when downloading images from web browsers.

So that means we need to edit the hex of this file and set it to the correct magic numbers.

There are different magic numbers per each file type. 

For example, a PDF file starts with "25 50 44 46 2D", which is "%PDF-" in ASCII.

Relevantly, a JPEG starts with "FF D8 FF E0" or it could also end in "E1" as the fourth byte, and ends in "FF D9"

To tamper with the hex of the file, we use the hexeditor command.

    ┌──(hellopo㉿rald)-[~]
    └─$ hexeditor file

This is what it looks like after hitting enter:

![Image](../../assets/corruptedFile1.png)

Focus on the highlighted byte.

It starts off as "5C 78 FF E0"

We edit the first two bytes to "FF D8" to match the proper convention for a JPEG file.

![Image](../../assets/corruptedFile2.png)

Let's check the bottom bytes if it's correct also, and it is correctly set to "FF D9"

![Image](../../assets/corruptedFile3.png)

Let's now save it with Ctrl+X. It will allow you to specify the file name. No need, just hit enter again and it will send you back to the terminal.

Let's check the file type again, now that we've successfully edited its magic numbers.

    ┌──(hellopo㉿rald)-[~]
    └─$ file file
    file: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 800x500, components 3

Now it's a JPEG file, we can use xdg-open to view the image.

    ┌──(hellopo㉿rald)-[~]
    └─$ xdg-open file

We found the flag.

![Image](../../assets/corruptedFile4.png)

Flag:

picoCTF{r3st0r1ng_th3_by73s_1512b52a}



