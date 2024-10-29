Name: photo-clue

Description: A seemingly innocuous old photo file has emerged, but Mary Morse suspects it may hold the key to deciphering The Consortium's next move. Your task is to analyze the metadata, uncover the true nature of the file, and submit the flag before The Consortium acts. Time is running out—can you crack the mystery behind the photo? (Note: If you choose to use volatility2.6, use profile Win10x64_19041)

This challenge calls for the use of volatility since the question hints so by saying ```If you choose to use volatility2.6, use profile Win10x64_19041)```, and the challenge file we've been given is a .raw windows event trace file.
Using volatility, we can find some basic general information.

![image](https://github.com/user-attachments/assets/aa12489f-a5d1-47cf-a8fa-97fa2a143b30)

The challenge description hints at photo files having emerged. Given that we have a Windows file to analyze, we can use the ```windows.handles``` option to look for any handles that contain extensions belonging to images.
Since the output of this command was too big, I redirected it into a text file so I could search easier.

![image](https://github.com/user-attachments/assets/029af395-013e-48b8-ae73-c1ca72b7a31d)

The handles didn't have any .png files but it did contain some .jpg files. There are recursions of ```REDACTED.jpg``` found in the handles.

![image](https://github.com/user-attachments/assets/485008a5-0184-4e64-8da9-7f48643c5daf)

We can then try to extract these ```REDACTED.jpg``` files from the .raw file.

![image](https://github.com/user-attachments/assets/eab89d67-ef06-4113-9a98-bc939fccfcb0)

This is the extracted ```REDACTED.jpg```.

![image](https://github.com/user-attachments/assets/ec7492f3-fa2a-420c-8934-67abb57d0451)

The rest is just normal forensics and steganography CTF stuff. I ran file and exiftool but found nothing interesting, so I assumed steganography was at play. I initially used Aperi'solve to solve it but steghide with no passphrase worked just as well.

![image](https://github.com/user-attachments/assets/dcc75cbb-6991-4afb-aea0-79e3f3cee93a)

A protected 1.4 - 1.6 version PDF was extracted. I couldn't find any password lying around either so I moved to bruteforcing it.

![image](https://github.com/user-attachments/assets/b8efbdcf-8668-4758-9156-d887becb9b49)

Once I found the password and unlocked the PDF, I found some binary strings inside. Decoding it on Cyberchef, I got the flag.

![image](https://github.com/user-attachments/assets/a106a353-2ddf-4b52-9416-5476bf0afdaf)

