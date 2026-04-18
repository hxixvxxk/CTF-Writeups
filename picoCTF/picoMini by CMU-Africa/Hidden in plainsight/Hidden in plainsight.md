CTF Name: picoCTF

Challenge Name: Hidden in plainsight

Category: Forensics

Difficulty: Easy

Challenge Description:

"You’re given a seemingly ordinary JPG image. Something is tucked away out of sight inside the file. Your task is to discover the hidden payload and extract the flag. Download the jpg image here."

Hints:

Download the jpg image and read its metadata

Disclaimer: I don't read the hints during my first attempt. The process below is done without knowledge of the hints unless specified.

TOOLS USED:

SOLVING PROCESS:

The challenge description seems to hint at looking at the file's metadata, which is information about the file which is not directly shown to the viewer. 

Let's start by downloading the file using wget. 

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ wget https://challenge-files.picoctf.net/c_amiable_citadel/f4d19218cb65d748b6a9b2bfc75caf5ac578f71e284ade9ee300e7367a5df648/img.jpg
    --2026-04-18 09:58:59--  https://challenge-files.picoctf.net/c_amiable_citadel/f4d19218cb65d748b6a9b2bfc75caf5ac578f71e284ade9ee300e7367a5df648/img.jpg
    Resolving challenge-files.picoctf.net (challenge-files.picoctf.net)... 3.165.63.115, 3.165.63.76, 3.165.63.36, ...
    Connecting to challenge-files.picoctf.net (challenge-files.picoctf.net)|3.165.63.115|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 73497 (72K) [application/octet-stream]
    Saving to: ‘img.jpg’
    
    img.jpg.1                100%[================================>]  71.77K   208KB/s    in 0.3s    
    
    2026-04-18 09:59:01 (208 KB/s) - ‘img.jpg’ saved [73497/73497]

Then, as a first instinct when solving CTFs, you should use the file command to double check what type of file you're working with based on its magic bytes.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ file img.jpg              
    img.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, comment: "c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9", baseline, precision 8, 640x640, components 3

After running the file command, we see a comment encoded in base64. Let's decode it using base64 -d.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ echo "c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9" | base64 -d 
    steghide:cEF6endvcmQ=

Steghide is a steganography program that is able to hide data in various kinds of image and audio files.

With steghide, you may need a passphrase to be able to extract information from the image.

Additionally, we find a string that's written in base64. Let's decode it using base64 -d

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ echo "cEF6endvcmQ=" | base64 -d           
    pAzzword

It gives us "pAzzword". This is very likely the passphrase we'll need to find the hidden flag.

The way to extract hidden data using steghide is using this command:

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ steghide extract -sf img.jpg
    Enter passphrase: 
    wrote extracted data to "flag.txt".

Now it will prompt you to type in the password, which is "pAzzword".

When you type it, it won't show any characters for privacy reasons. Just type in the password or paste it and hit enter.

It will extract it to a file, so let's just cat "flag.txt"

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ cat flag.txt 
    picoCTF{h1dd3n_1n_1m4g3_5d4cba73}

There you go! 

Flag:

picoCTF{h1dd3n_1n_1m4g3_5d4cba73}
