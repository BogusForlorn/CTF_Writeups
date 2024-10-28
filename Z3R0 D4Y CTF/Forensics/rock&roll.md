Name: rock&roll

Description: Don't be scared. It's Halloween guys, a friendly match between You and Wanda Maximoff. Fight till the END. Good Luck.

First order of business, before we even conduct our usual reconnaissance. This "JPEG" file is way too big. Why is this file 17 MB big?
There's definitely some files hidden here as a part of binwalk so let's extract them.

![image](https://github.com/user-attachments/assets/b62d18e0-0bb0-4a0f-9f52-17f3a1ebfff6)

Taking a look at the zip that's been embedded in the binwalk, we can definitely see why it is 17MB

![image](https://github.com/user-attachments/assets/021b0124-fcf5-4862-bebb-c867a5b0cba6)

Within the zip is another zip titled htq.zip and a text file. This seems to be the case for every zip hidden inside; there's another zip and a text file. And this seemingly continues until it reaches a.zip.
Yikes.
At first I thought maybe the flag was just at the end of a.zip. So using my initial python script, I unzipped everything until I reached a.zip. Unfortunately, in a.zip, there wasn't a flag.txt as I was hoping but rather a text file 'a.zip'. And the contents of the file isn't the flag format either, but a hexadecimal number. We all know where this is going...
So then I started over. I changed my python script to extract the zip and concatenate the text file as it goes.

```
import zipfile
import os

def extract_nested_zip(zip_file_path, extract_to_dir):
    current_zip_path = zip_file_path

    if not os.path.exists(extract_to_dir):
        os.makedirs(extract_to_dir)

    while True:
        with zipfile.ZipFile(current_zip_path, 'r') as zip_ref:
            zip_ref.extractall(extract_to_dir)
            extracted_files = os.listdir(extract_to_dir)
            zip_files = [f for f in extracted_files if f.endswith('.zip')]
            if not zip_files:
                print('Reached the last level. No more zip files to extract.')
                break

            current_zip_path = os.path.join(extract_to_dir, zip_files[0])
            os.remove(current_zip_path)
    for file in extracted_files:
        if file.lower().startswith("flag") and file.endswith(".txt"):
            with open(os.path.join(extract_to_dir, file), 'r') as flag_file:
                flag_content = flag_file.read()
                print(f"Flag found: {flag_content}")

zip_file_path = 'htq.zip'
extract_to_dir = 'extracted_files_dir'
extract_nested_zip(zip_file_path, extract_to_dir)
```

And the result of that? We got a hex dump. I tried to see what this hex dump is by decoding it from hexadecimal into ASCII.

![image](https://github.com/user-attachments/assets/b8501c47-72aa-42af-a99e-38adebc17312)

Going to the bottom, we see that its actually a hex dump for a PNG image... in reverse. 

![image](https://github.com/user-attachments/assets/d0b9d7d9-5a02-4c76-a8c2-b3bf22284405)

No worries! Just reverse it, turn it back into a hex dump, and paste it into hexed.it, then export it.

![Untitled](https://github.com/user-attachments/assets/804b6c33-2753-475f-8de4-41e6d5a41197)

And that's our flag.

Author's note: This challenge wasn't particularly hard. Just very, very, very annoying.
