Name: What type of image

Description: What is the data of the image.

Some recon to see what we're working with.

![image](https://github.com/user-attachments/assets/fa60c080-f3da-4cd2-8f91-ac5212d90347)

The challenge file with a .png extension isn't a PNG file at all, but rather a text file.
I used the cat command to read what values are actually inside the PNG file.

![image](https://github.com/user-attachments/assets/20fd0568-358b-4aea-b3c0-a03833b7b085)

These numbers look very familiar especially, after finishing the rock&roll challenge. These are the magic numbers for a PNG file, but their order is odd. Every 4 bytes are flipped.
Using a python script to reverse the bytes:

```
def invert_hex_bytes(input_file, output_file):
    with open(input_file, 'r') as f:
        hex_data = f.read().strip()

    hex_data = hex_data.replace(" ", "").replace("\n", "")

    if len(hex_data) % 8 != 0:
        print("Error: Hex data length is not a multiple of 4 bytes (8 hex characters).")
        return

    inverted_hex = ""
    for i in range(0, len(hex_data), 8):
        chunk = hex_data[i:i+8]
        inverted_chunk = chunk[6:8] + chunk[4:6] + chunk[2:4] + chunk[0:2]
        inverted_hex += inverted_chunk

    with open(output_file, 'w') as f:
        for i in range(0, len(inverted_hex), 8):
            f.write(inverted_hex[i:i+8] + "\n")

    print(f"Inverted hex dump written to {output_file}.")

input_file = 'hex_dump.txt'
output_file = 'inverted_hex_dump.txt'

invert_hex_bytes(input_file, output_file)
```

We get the corrected output of the text file as a hex dump. Now we turn the hex dump back into a PNG by using hexed.it

![image](https://github.com/user-attachments/assets/43107683-78d3-4f23-a80e-364b4b34fa41)

And we get the flag.
