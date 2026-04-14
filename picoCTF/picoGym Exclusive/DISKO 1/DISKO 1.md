CTF Name: picoCTF

Challenge Name: DISKO 1

Category: Forensics

Difficulty: Easy

Challenge Description: "Can you find the flag in this disk image? Download the disk image here."

Hints: Maybe Strings could help? If only there was a way to do that?

Disclaimer: I don't read the hints during my first attempt. The process below is done without knowledge of the hints unless specified.

SOLVING PROCESS:

Prior to this solve, I didn't know much about disks in terms of forensics. How to read them, look through files, etc.

However, I did already have a decent idea about them.

So I had to rely on a lot of Google searches to understand what everything meant, especially the types of tools to use and keep in mind when solving these.

Let's begin by downloading the file using wget.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ wget https://artifacts.picoctf.net/c/537/disko-1.dd.gz
    --2026-04-14 03:10:21--  https://artifacts.picoctf.net/c/537/disko-1.dd.gz
    Resolving artifacts.picoctf.net (artifacts.picoctf.net)... 18.172.21.43, 18.172.21.12, 18.172.21.26, ...
    Connecting to artifacts.picoctf.net (artifacts.picoctf.net)|18.172.21.43|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 20484478 (20M) [application/octet-stream]
    Saving to: ‘disko-1.dd.gz’
    
    disko-1.dd.gz            100%[================================>]  19.54M  5.81MB/s    in 3.4s    
    
    2026-04-14 03:10:25 (5.81 MB/s) - ‘disko-1.dd.gz’ saved [20484478/20484478]
Based on the file extension, ".gz" it must be a gunzip file.

A .gz file is a compressed archive created using the gzip (GNU zip) algorithm, commonly used in Unix/Linux systems to reduce file size for storage or transfer.

Double check with file to confirm

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ file disko-1.dd.gz    
    disko-1.dd.gz: gzip compressed data, was "disko-1.dd", last modified: Thu May 15 18:48:20 2025, from Unix, original size modulo 2^32 52428800
It's compressed, so we should decompress it with these commands

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ gzip -d disko-1.dd.gz 
This leaves us with "disko-1.dd"

Let's check what kind of file it is.

    ┌──(hellopo㉿rald)-[~/Documents/CTF]
    └─$ file disko-1.dd   
    disko-1.dd: DOS/MBR boot sector, code offset 0x58+2, OEM-ID "mkfs.fat", Media descriptor 0xf8, sectors/track 32, heads 8, sectors 102400 (volumes > 32 MB), FAT (32 bit), sectors/FAT           788, serial number 0x241a4420, unlabeled
A .dd file is a raw disk image file created by the dd (Dataset Definition) command-line utility, acting as a bit-for-bit copy of a drive, partition, or medium

First thought here is to search through this file, and see what files we can find.

So, it's straightforward to assume that tools like "mmls" from Sleuth Kit can help search through it.

However, mmls searches through
