Name: Something Missing?

First order of business, recon.

![image](https://github.com/user-attachments/assets/a39d4e7f-5afc-4ece-a0e6-4f84e0f7f4d6)

The result of the file command says its just 'data', and exiftool says its an 'Unknown file type'. 
Let's see what else we can find about this file.

![image](https://github.com/user-attachments/assets/62225abc-2714-48ad-9ae1-b2681146d9bc)

Using pngcheck, we can see that it is actually a PNG file; just that it has been so mangled that even pngchecker says its not a PNG file. 
But we know its a PNG file because all the other relevant PNG headers are still there such as IHDR, pHYs, iTxt, IDAT, and IEND. 

![image](https://github.com/user-attachments/assets/d20f34c4-f07a-4df2-866d-40b1a1ab3041)

Opening the file in a hexeditor, we can change the bits and reflect the changes by exporting it.
My hexeditor of choice is hexed.it. Though pngchecker says the IHDR header is detected, from my analysis, it is also corrupted. Let's fix the PNG and IHDR header.

![image](https://github.com/user-attachments/assets/1b76bebf-f05f-4468-93ee-81aa8c52355c)

![image](https://github.com/user-attachments/assets/a731c1cb-0691-44bd-93f5-46b40cda5b5f)

At the very bottom of the image we can get the flag.
