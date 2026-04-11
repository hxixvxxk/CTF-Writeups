This is the first challenge I'm solving for 2026.

CTF Name: picoCTF

Challenge Name: Binary Digits

Category: Forensics

Difficulty: Easy

Challenge Description: 
"This file doesn't look like much... just a bunch of 1s and 0s. But maybe it's not just random noise. Can you recover anything meaningful from this?
Download the file here."

SOLVING PROCESS:

The description tells us that the file consists of a bunch of 1s and 0s, so it must be a .bin file.

A .bin file (binary file) is a non-text file format containing machine-readable data, commonly used for CD/DVD disk images, firmware updates, or application data. It stores raw binary code, often requiring specialized software like ISO Buster, UltraISO, or PowerISO to mount or extract, especially when accompanied by a .cue metadata file. 

It hints that the file contains a bunch of noise, which tells me that we're gonna have to look at a lot of code, and find out which lines have the flag.

The first step is to hop on a Linux environment. I'm using Ubuntu because it's easy to use for a beginner like me. 

I open my Ubuntu software. (22.04.5 LTS)

I right click the file link, and hit "Copy link address"

![Image](../../assets/binaryDigits1.png)

Now, the link is in my clipboard and I get use the wget command to download it into my Linux environment.

![Image](../../assets/binaryDigits2.png)

After successfully downloading, digits.bin can be seen in my current directory after using the ls command.

![Image](../../assets/binaryDigits3.png)

In a CTF, a .bin file is rarely just raw binary data. It is often a disguise for other file types like an executable or a disk partition image. 

To unmask its true identity, I used the file command to inspect its Magic Bytes (file signatures), rather than relying on the extension.

![Image](../../assets/binaryDigits4.png)

We find out that its ASCII text, 
