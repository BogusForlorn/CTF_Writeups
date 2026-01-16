Title: `Shortcut to Flag`
Description:

`One of our analysts noticed suspicious activity originating from a workstation after an employee clicked what appeared to be a password files. Upon investigation, we found this mysterious .LNK file in their Downloads folder.`


# Solution
First things first, we’re given an LNK file, which is a shortcut by reference for the Windows system. The thing about this challenge is we’re not given anything else like an AD1 file or a memdump, so we can’t actually see what the shortcut is point to. The only thing we can do is try to inspect the LNK file itself. So first I viewed the contents with cat.

<img width="975" height="435" alt="image" src="https://github.com/user-attachments/assets/033ebc13-9bb6-4e90-a2d9-81cfb6a1bd39" />

Seems like its really a windows shortcut file. We can see some strings here and there, but it's really hard to read like this. So, a quick Google to find tools to parse, and the first thing we find is lnk-parser by Eric Zimmerman. Download the git, compile the tool, and parse the LNK. Doing so, we find this:

<img width="975" height="473" alt="image" src="https://github.com/user-attachments/assets/789c7537-86e5-465e-a29a-9da98e3d6b43" />

From the output of the lnk-parser tool, we can actually see something interesting. We see that the shortcut actually is a malicious script that first opens notepad, then executes a powershell script. Analyzing the powershell script, we see what the script does, which is XOR encoding a LNK file in the directory. It does this specifically by truncating or skipping the first 3044 bytes.
So, the solution is to XOR decode. The issue here is: 1) we only have the LNK file and nothing else, so the XORing is probably onto this provided LNK itself. 2) The XOR key isn’t given, so the solution here is to brute-force XOR decode. So our solution is to make a script that bruteforces XOR from key length 0x00 to 0xff (255 values) onto the LNK file itself. To find which of the 255 output binaries holds the right file we’re looking for, in the same script that decodes, I added a function to read the file signature. The specific signature I’m looking for is the MZ signature which belongs to windows executables. From that, I found a few keys which has the MZ signature and outputed those specific binaries. Then from the binaries, I had them scanned for other signatures. In the end, my script narrowed it down to one specific key and binary.

<img width="975" height="377" alt="image" src="https://github.com/user-attachments/assets/bb1bad4d-0255-4c84-91d1-e2f49a59d5a8" />

Saving the binary as an exe, I then inspected it using a text editor, but saw nothing. So the next thing I had to do is decompile the binary. The specific file is a dotNet file, so we have to use a specific decompiler. I used ILSpy to decompile. 

<img width="975" height="523" alt="image" src="https://github.com/user-attachments/assets/e693b053-6a05-43f6-a061-dc8d40e9a358" />

You can simply write a script that takes the functions above to get the flag:
# Flag
`UMCS{281e271947b31e6dad08629bbcb53462}`
