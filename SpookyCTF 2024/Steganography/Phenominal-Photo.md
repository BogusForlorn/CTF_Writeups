Name: Phenominal-Photo

Description: Simon was spotted dwelling under the clock-tower yet again, this time taking pictures. He seems to have captured a strange object in the far far distance going left, right, up, and down seemingly lost or out of control. There is a strange aura radiating from the photo, pulsations even, like an SOS. Can you figure out this strange phenomenon??

Business as usual, some reconnaissance.

![image](https://github.com/user-attachments/assets/912f8fef-4122-45ac-aaa6-d70f9352a249)

Both commands show some pretty standard things. Nothing too unusual about this file.
So I moved to using Aperi'Solve for ease.

![image](https://github.com/user-attachments/assets/7f7eea4b-a4fd-461f-9d15-10890908a462)

Here we can find that there's been a file hidden in the LSB using steghide. We can extract this from Aperi'Solve.

![image](https://github.com/user-attachments/assets/d9d849d0-6de0-4921-af9b-1aca2ce7ce07)

![image](https://github.com/user-attachments/assets/4980dcbf-29fc-4af6-b94b-ecc70e882e42)

Inside we find a folder titled 'Ship#1' and in it, a zipped file and a text file.
The zip file might hold our flag here.

![image](https://github.com/user-attachments/assets/43ee478e-2fdf-448e-8b4a-1ad7248d6973)

Reading from the Map.txt file, it seems to be some sort of gibberish. My first bet was ancient Egyptian hieroglyphics but a quick google search gave us the result that it was 'alien language'. Yeah idk either.
Using an online decoder, we find this:

![image](https://github.com/user-attachments/assets/83a07e03-b409-4224-bce6-405753ea23fd)

'Takes the first letter of each direction'. That could be a hint to the password. This would be 'LUDLDRRDLULRU'.
Attempting this password on the locked zip, we find another text file titled 'myrequest.txt'. Reading from it gets us the flag.

![image](https://github.com/user-attachments/assets/b97a915b-4a0b-4b76-843c-e926d2164ddb)

![image](https://github.com/user-attachments/assets/074a86d0-a590-405c-a37d-6849e17ab843)
