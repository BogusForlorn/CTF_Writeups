![image](https://github.com/user-attachments/assets/86d0ffba-2b78-4c9f-afd2-2940afb63ab3)Challenge Description: Partial traffic packet captured from hacked machine, can you analyze the provided pcap file to extract the message from the packet perhaps by reading the packet data?
Author: Ap0k4L1p5

In this challenge, we're given traffic.pcap. First things first when dealing with a pcap file is checking for the protocol hierarchy to get a better picture of what we have here.

![image](https://github.com/user-attachments/assets/70a2c4a2-65a1-4dc2-8c8c-e40b41ce17d6)

It seems like there's a lot of ICMP packets here. ICMP packets can carry data bits which are segmented. And we can see exactly that. The ICMP packets are holding a character value.

![image](https://github.com/user-attachments/assets/457a66d8-c6ee-4bfc-a919-b33522bedbfa)
![image](https://github.com/user-attachments/assets/2154658b-4bee-4412-8e2e-65d9fc83810e)
![image](https://github.com/user-attachments/assets/8090c85c-7159-4e42-9517-a241731a3270)
![image](https://github.com/user-attachments/assets/73388bb3-094a-4ed7-a599-d3b5abd2f7ca)

From this alone we can see the flag format "WGMY". So I used wireshark to print our flag. The exact command I used was:
```
tshark -r traffic.pcap -Y "icmp" -T fields -e data | xxd -r -p
```
This prints specifically the data segment from all the ICMP packets, and because the output of that would be in hexadecimal, I ran it by xxd to decode it into ascii characters:

![image](https://github.com/user-attachments/assets/44b89bf2-a408-4e56-8091-e89ec04f9eb4)
