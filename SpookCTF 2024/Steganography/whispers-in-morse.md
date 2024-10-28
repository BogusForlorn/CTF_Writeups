Name: whispers-in-morse

Description: Mary got a letter from Maya talking about seeing a cryptid sighting but doesn't want other people to know, the only thing attached is this picture? She wonders if there could be a secret message hidden inside.

The challenge has given us a JPEG image. First order of business is to collect more information about the file.
In steganography and forensics, we would do this using the 'file' and 'exiftool' commands.
Seeing as the output of these commands gave fairly normal results, we should try to dig for more info.
Here I used the hex editor, xxd to see if there were anything written in the hex values of the file, and sure enough there was.

![image](https://github.com/user-attachments/assets/be793bb9-c932-4466-a41b-fd0c76fb7566)

There is a password here 'M.A.__.R.Y'
With a password, this could allow us to use steghide.

![image](https://github.com/user-attachments/assets/86f45bfe-c75d-44cf-ad9c-2c7737501b6c)

And the flag is there.
