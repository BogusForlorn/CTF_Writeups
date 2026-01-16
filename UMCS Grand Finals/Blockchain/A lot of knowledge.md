# Challenge

Title: 
`A lot of knowledge`

Description: 

`Whatever was learnt until now forget it, no knowledge is required.`


# Solution
For this challenge, we’re given just a link to a sepolia scroll contract. With almost no other file or info, this was pretty tough. But reading the ZKChallenge.sol contract, we can find some interesting stuff pretty quickly. First things first, we can see that there are some functions that print the flag already in the contract. Further analysis on this contract tells us that the values for G, P, and H are hardcoded, but some values such as the commitment unsigned variable and the response variable have to be obtained in real time. This made the challenge a bit more tough.

<img width="975" height="333" alt="image" src="https://github.com/user-attachments/assets/78da52c6-bc53-46a8-b2ee-24fa6b507e70" />

Adding insult to injury, no blockchain launcher was provided. Meaning it costs gas to run, and it could potentially mean that you still don’t get the flag. Bummer because my metamask wallet has no money in it.
The ZKChallenge.sol contract also tells us that all the conditions need to be met. This includes the leftSide and rightSide, and the verify Proof, which are all important to solving this challenge. I had a lot of iterations of scripts and contracts for this. But ended up solving it with 2 specific scripts. One to submit the commitment based on the not constant values like response and commitment. As seen below, the first one is to submit the commitment value. The script calculates the exact values needed and was made by analyzing the ZKChallenge.sol contract.

<img width="975" height="460" alt="image" src="https://github.com/user-attachments/assets/f74985b7-35b5-4913-8bb4-40e87adb1d1b" />

The 2nd script to run then verifies whether the proof submitted is valid. It also checks specifically for the value of the response, whether it is correct against the chain’s value. If not, then both scripts needed to be run again but with different values. This is kinda bruteforcing as part of the commitment relies on other values like the timestamp.

<img width="975" height="377" alt="image" src="https://github.com/user-attachments/assets/273bb35f-b4df-4546-b496-56385f35a549" />

After verifying my value of the response and getting the correct values dialed in, I ran both scripts with --broadcast which basically commits it onto the chain. This makes it so we can get the flag.

<img width="975" height="544" alt="image" src="https://github.com/user-attachments/assets/df595341-73b5-4f85-bdb6-a94ea2a9121e" />

From the picture above, we can basically verify whether the submitCommitment was successful or not by checking the verifyProof condition, which is true. Then, we can simply just trigger the getFlag() function and print our flag.


# Flag

`umcs{ZK_i3_s3cr3tly_3asy}`
