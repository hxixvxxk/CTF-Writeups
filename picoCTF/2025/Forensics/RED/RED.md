CTF Name: picoCTF

Challenge Name: RED

Category: Forensics

Difficulty: Easy

Challenge Description: 

"RED, RED, RED, RED
Download the image: red.png"

Hints:

The picture seems pure, but is it though?

Red?Ged?Bed?Aed?

Check whatever Facebook is called now.

Disclaimer: I don't read the hints during my first attempt. The process below is done without knowledge of the hints unless specified.

SOLVING PROCESS:

TOOLS USED: 
    
    wget

    file

    xdg-open

    exiftool
    
    strings
    
    zsteg

    echo

    base64
    

When faced with easy challenges regarding images, the most intuitive thing to check is its metadata.

All images have metadata assigned to them. This is the information that is attached to the image used to label it.

For example: file name, file size, directory, file type, image width*height, and more.

[Learn more about metadata. Click me!](https://www.techtarget.com/whatis/definition/image-metadata)

You can gain valuable information by checking it because messages can be hidden there, especially if the challenge description heavily suggests it.


Download the file with "wget"

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ wget https://challenge-files.picoctf.net/c_verbal_sleep/831307718b34193b288dde31e557484876fb84978b5818e2627e453a54aa9ba6/red.png
    --2026-04-14 13:06:19--  https://challenge-files.picoctf.net/c_verbal_sleep/831307718b34193b288dde31e557484876fb84978b5818e2627e453a54aa9ba6/red.png
    Resolving challenge-files.picoctf.net (challenge-files.picoctf.net)... 3.165.63.56, 3.165.63.36, 3.165.63.115, ...
    Connecting to challenge-files.picoctf.net (challenge-files.picoctf.net)|3.165.63.56|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 796 [application/octet-stream]
    Saving to: ‘red.png’
    
    red.png.1                 100%[====================================>]     796  --.-KB/s    in 0s      
    
    2026-04-14 13:06:21 (14.2 MB/s) - ‘red.png’ saved [796/796]

Double check the file type using the "file" command. Remember, the challenge could try to trick you with the extension. Best be sure.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ file red.png 
    red.png: PNG image data, 128 x 128, 8-bit/color RGBA, non-interlaced

Now that we've confirmed it's actually a PNG, 

Let's take a quick peek.

┌──(hellopo㉿rald)-[~/Documents/CTF]
└─$ xdg-open red.png 

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ea94648c-e8be-435a-a157-441c19d44a4d" />

Check the file's metadata using "exiftool"

ExifTool is a free, open-source command-line application and Perl library used for reading, writing, and editing meta information in a vast array of file types. 

[Learn more about exiftool.](https://medium.com/@amaltomparakkaden/exiftool-a-powerful-command-line-tool-for-manipulating-metadata-a404acc221e4)

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ exiftool red.png 
    ExifTool Version Number         : 13.50
    File Name                       : red.png
    Directory                       : .
    File Size                       : 796 bytes
    File Modification Date/Time     : 2025:03:05 22:34:15-05:00
    File Access Date/Time           : 2026:04:14 12:14:33-04:00
    File Inode Change Date/Time     : 2026:04:14 12:14:28-04:00
    File Permissions                : -rw-rw-r--
    File Type                       : PNG
    File Type Extension             : png
    MIME Type                       : image/png
    Image Width                     : 128
    Image Height                    : 128
    Bit Depth                       : 8
    Color Type                      : RGB with Alpha
    Compression                     : Deflate/Inflate
    Filter                          : Adaptive
    Interlace                       : Noninterlaced
    Poem                            : Crimson heart, vibrant and bold,.Hearts flutter at your sight..Evenings glow softly red,.Cherries burst with sweet life..Kisses linger with your warmth..Love deep as merlot..Scarlet leaves falling softly,.Bold in every stroke.
    Image Size                      : 128x128
    Megapixels                      : 0.016

Sticking out like a sore thumb, we get a poem. 

Before we continue, there's also a trick with images where you can just use the "strings" command to show raw strings embedded in the image.

This isn't something you should always do though, but it's just a niche trick I guess. 

It'll make our lives easier in this scenario because it's gonna format the poem.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ strings red.png       
    IHDR
    tEXtPoem
    Crimson heart, vibrant and bold,
    Hearts flutter at your sight.
    Evenings glow softly red,
    Cherries burst with sweet life.
    Kisses linger with your warmth.
    Love deep as merlot.
    Scarlet leaves falling softly,
    Bold in every stroke.x
    IDATx
    IEND

So we get some text revealing the internal structure of the image

IHDR is for the image's header
IDATx is the image's data chunks
IEND is the image end.

More importantly, it's now easier to see the poem.

Look closely at the first few letters of the poem.

It says "Check LSB"

The LSB here stands for the least significant bit. 

LSB (Least Significant Bit) steganography hides secret data by replacing the last bit of an image's pixel values with bits of a hidden message, 
making the change invisible to the human eye.

[Learn about bit numbering here.](https://en.wikipedia.org/wiki/Bit_numbering)

[LSB Steganography](https://www.youtube.com/watch?v=yNo58UiIMKU)


The tool we're gonna use to detect the LSB data is zsteg. Let's see how it works.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ zsteg red.png 
    meta Poem           .. text: "Crimson heart, vibrant and bold,\nHearts flutter at your sight.\nEvenings glow softly red,\nCherries burst with sweet life.\nKisses linger with your warmth.\nLove deep as merlot.\nScarlet leaves falling softly,\nBold in every stroke."                                             
    chunk:0:IHDR        .. file: Adobe Photoshop Color swatch, version 0, 128 colors; 1st RGB space (0), w 0x80, x 0x806, y 0, z 0; 2nd HSB space (1), w 0x100, x 0, y 0xff01, z 0xff                             
    b1,rgba,lsb,xy      .. text: "cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ=="                      
    b1,rgba,msb,xy      .. file: OpenPGP Public Key
    b2,g,lsb,xy         .. text: "ET@UETPETUUT@TUUTD@PDUDDDPE"
    b2,rgb,lsb,xy       .. file: OpenPGP Secret Key
    b2,bgr,msb,xy       .. file: OpenPGP Public Key
    b2,rgba,lsb,xy      .. file: OpenPGP Secret Key
    b2,rgba,msb,xy      .. text: "CIkiiiII"
    b2,abgr,lsb,xy      .. file: OpenPGP Secret Key
    b2,abgr,msb,xy      .. text: "iiiaakikk"
    b3,rgba,msb,xy      .. text: "#wb#wp#7p"
    b3,abgr,msb,xy      .. text: "7r'wb#7p"
    b4,b,lsb,xy         .. file: 0421 Alliant compact executable not stripped

This is the result we get. Look closely at the "b1,rgba,lsb,xy" line. That is clearly base64. Let's decode it using the "base64" command.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ echo "cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==" | base64 -d 
    picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}

Looks like it's just a repeating version of the flag. We'll only need one of those guys.

Flag:

picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}
