# Description 
I wrote the flag in the notepad and accidentally closed it. Will I lose the flag? 


# Solution 
Similar to the previous challenge, a text editor was closed without saving it. But unlike Microsoft Words, which saves the file temporarily to be recovered, notepad is too simple and doesn’t contain this feature. So, we had to do some digging and research. 
From a superuser forum, I found a thread of possible directories to look for. 
<img width="468" height="186" alt="image" src="https://github.com/user-attachments/assets/d250c632-b207-40d7-ac53-cf188796e821" />
So I went to look for the 
AppData\Local\Packages\Microsoft.WindowsNotepad_8wekyb3d8bbwe\LocalState and sure enough, found something of interest. 
<img width="468" height="242" alt="image" src="https://github.com/user-attachments/assets/b8be1504-50c2-4813-ba76-aa9619dfe8e0" />
Opening the file, we can see the flag.
<img width="468" height="335" alt="image" src="https://github.com/user-attachments/assets/a00cad76-afe0-4c0b-9c65-e115b62630a6" />
