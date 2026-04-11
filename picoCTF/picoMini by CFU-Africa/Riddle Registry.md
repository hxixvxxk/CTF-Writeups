CTF Name: picoCTF

Challenge Name: Riddle Registry

Category: Forensics

Difficulty: Easy

Challenge Description: "Hi, intrepid investigator! 📄🔍 You've stumbled upon a peculiar PDF filled with what seems like nothing more than garbled nonsense. But beware! Not everything is as it appears. Amidst the chaos lies a hidden treasure—an elusive flag waiting to be uncovered. Find the PDF file here Hidden Confidential Document and uncover the flag within the metadata."

Hints: Don't be fooled by the visible text; it’s just a decoy! Look beyond the surface for hidden clues


SOLVING PROCESS:

Reading the description, my first instinct is that it will show me a pdf file which will contain either a hint or irrelevant information. I know for sure I would have to look at the metadata of the pdf or any object/image embedded in it.

With that in mind, I'm keeping a mental note of the tools I would have to use: 

"xdg-open" to view the PDF, 

"exiftool" to view its metadata, 

and possibly decryption commands like "base64 -d" if any base64 shows up.

(Btw I just installed Kali on VMWare so this is the first CTF I solved feeling like a true h4ck3r or something.)

Let's start by using the "wget" command to download the PDF file. 


    ┌──(hellopo㉿rald)-[~]
    └─$ wget https://challenge-files.picoctf.net/c_amiable_citadel/9eb01e29bada8f3c16abe23682c2df28e91a5f9904e505f007e017cc5fb24593/confidential.pdf 

After it successfully downloads, my first instinct for most CTFs with a simple file is to check its true file type because I have trust issues with these things.

    ┌──(hellopo㉿rald)-[~]
    └─$ file confidential.pdf 
    confidential.pdf: PDF document, version 1.7, 1 page(s)

Well, at least I know it's really a PDF.

Next, I want to view the contents. 

    ┌──(hellopo㉿rald)-[~]
    └─$ xdg-open confidential.pdf

It's a one-page PDF with some playful text teasing us about the flag.

![Image](../assets/riddleRegistry1.png)

It has certain words that are blacked out, but simply highlight them by selecting the words and it reveals some teasing. No flag. 

I believe it's time to dig into its metadata. 


    ┌──(hellopo㉿rald)-[~]
    └─$ exiftool confidential.pdf     
    ExifTool Version Number         : 13.50
    File Name                       : confidential.pdf
    Directory                       : .
    File Size                       : 183 kB
    File Modification Date/Time     : 2025:11:07 14:17:34-05:00
    File Access Date/Time           : 2026:04:11 13:27:40-04:00
    File Inode Change Date/Time     : 2026:04:11 13:27:16-04:00
    File Permissions                : -rw-rw-r--
    File Type                       : PDF
    File Type Extension             : pdf
    MIME Type                       : application/pdf
    PDF Version                     : 1.7
    Linearized                      : No
    Page Count                      : 1
    Producer                        : PyPDF2
    Author                          : cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV9lZTQ1NDk1MH0=

Whenever I use exiftool, I know all of the useful info is on the right side, so I mostly ignore the left and rely on my pattern recognition to see anything out of the ordinary or if I spot an encryption type that I can recognize.

I immediately spot the base64 on the author's information.

    Author                          : cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV9lZTQ1NDk1MH0=

Let's copy that (Ctrl+Shift+C) so that we can echo it into a base64 decode command.

    ┌──(hellopo㉿rald)-[~]
    └─$ echo "cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV9lZTQ1NDk1MH0=" | base64 -d

The "echo" command in Linux is a built-in utility used to display lines of text or strings passed as arguments.

Meanwhile, "base64" is a command used to encode binary data into a text-based format. the "-d" flag tells it to decode base64 instead of encoding it.

The "|" is known as a pipe and is used to take the output of one command and send it directly as the input to the next command. You can say it like "I will pipe X into Y". For our case, you can say "I will echo this string and pipe it into ... ".

The result is shown.


Flag:

picoCTF{puzzl3d_m3tadata_f0und!_ee454950}



