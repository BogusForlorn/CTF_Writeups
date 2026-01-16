# Challenge

Title: 

`BankVaults`


Description: 

`Money money money!!`


# Solution

<img width="975" height="463" alt="image" src="https://github.com/user-attachments/assets/cf90cc0e-ba1c-40c6-9dad-044c3fcabb47" />

A blockchain launcher was given. This makes our lives a lot easier already. Additionally, a Setup.sol and BankVault.sol. These are our contracts which the challenge uses. Analyzing the BankVault.sol contract, we can see a glaring vulnerability in the flashLoan function. 

<img width="975" height="119" alt="image" src="https://github.com/user-attachments/assets/6ded52a2-7c70-4ed1-afda-603fba2c580e" />

It only checks how big the loan is. It records the balance before the transaction, but it doesn't check if the same ETH is sent. GPT explains this logic better:

<img width="863" height="660" alt="image" src="https://github.com/user-attachments/assets/59a8a6b0-2e05-4c85-800a-87fe4ea1b4dd" />
<img width="859" height="154" alt="image" src="https://github.com/user-attachments/assets/e4c4f260-ce61-4b14-82c7-d9bba79f7523" />

Basically, we can just take the fashloaned funds, stake to raise our balance, and not consume any of our moolah. Talk about heist. So just make a script that exploits that function (or ask GPT for it), then trigger it using the credentials we got from the blockchain launcher earlier.

<img width="975" height="446" alt="image" src="https://github.com/user-attachments/assets/e87085aa-e14f-466f-a649-42b84fb74fee" />

It has successfully taken the money without using any of our own. Now we just simply return to our launcher and hit the flag button:

<img width="975" height="469" alt="image" src="https://github.com/user-attachments/assets/c024d3f5-b612-4933-9803-01d548dff64d" />


# Flag

`umcs{1346770b6250d7f36e4ea86a42816b3c}`
