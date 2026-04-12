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
