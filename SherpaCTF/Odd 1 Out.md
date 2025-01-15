Name: Odd 1 Out

Description: There’s an imposter among the rest of the vroom vrooms, what is it trying to say?

Difficulty: Easy
Creator: PLZ ENTER TEXT

In this challenge, we're given a vcan log file. We can take a closer look at the contents of the file to see what its about.

![image](https://github.com/user-attachments/assets/07624615-1b99-447e-983b-607055332d21)

This vcan log seems like its a bunch of hexdump of some sort. Vcan is simple a virtual CAN interface, and CAN (Bus) is a message-based protocol that allows devices to communicate. Something about these numbers hold data about the flag and its been setup in a virtual CAN environment.

Before I get to my solution, let's talk about what I've tried:

Unique responses

![image](https://github.com/user-attachments/assets/d05e8e48-68fb-48a6-9818-130aba9e0046)

I wanted to see if there were any unique data in the hexdump. Perhaps an encoded string about the flag. But of course, my script doesn't seem to work.

![image](https://github.com/user-attachments/assets/262da0cf-d1ee-49d8-87ea-43cd00313751)

I then used awk to get the unique logs

![image](https://github.com/user-attachments/assets/70bc2870-776d-409a-bc71-2a91b4aa8dec)

Still nothing interesting here.


Replay VCAN Interface

I also attempted to mount vcan0 to my machine in hopes of replaying the data that was send out. I thought if I could replay the data the way the interface had received it, maybe I could get an idea of what was being transmitted.
Here I just used these simple commands to setup:
```
cansend vcan0
lsmod | grep vcan
```


**The real solution:**

I pasted the hexdump of the vcan log into cyberchef to see if any of the cipher options could decode it.

![image](https://github.com/user-attachments/assets/1604bb0f-6b1e-4042-bc8b-218a07c2a45c)

Aha! IHDR header. We found something here. I've only applied the hexadecimal to ascii option thus far.

![image](https://github.com/user-attachments/assets/6cd3e103-e42c-42ea-b4e3-7e88e7b2907d)

Cleaning up my data input a bit to get a clearer picture, I then exported the ascii output into a txt file for faster searching.

![image](https://github.com/user-attachments/assets/a9559835-e94a-4886-b57a-641bd89a6513)

And here I found a PNG header. I knew a PNG file was in here somewhere.
My next order of business was to look where this PNG header came from in the original log. And I found the origin by searching the magic numbers for the PNG header.

![image](https://github.com/user-attachments/assets/6108660f-ac3a-4715-b374-1a27588ac7c6)

![image](https://github.com/user-attachments/assets/f7789ab5-bed1-47bb-a7cb-ca4411ea7461)

It seems that every PNG-related header was marked by #800. So that's my bet, #800.

![image](https://github.com/user-attachments/assets/57fc1dae-1d67-46f7-9c7e-bc3df4bad1cc)

I made a script that extracts only the #800 hexdumps and wrote it into a file. 
Viewing it in hexed.it, we can see its a complete image.

![image](https://github.com/user-attachments/assets/bb8a0fab-0b8e-40cf-95ab-77f4f8c2bb30)

And here we have found our flag!

![image](https://github.com/user-attachments/assets/4d817688-049f-4494-866f-d5ddc2c68b31)

