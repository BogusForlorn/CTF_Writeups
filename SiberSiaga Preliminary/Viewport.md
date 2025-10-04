Deleted from desktop. Seeing the $I30 index in /desktop, we can tell there was a png (TCMPJP~1.PNG/TCMPNP~1.PNG) no longer here.

I thought to parse the index in hopes of recovering the flag but that can only recover the metadata of the file, not the actual file itself where it could be an image containing the flag.


Still, you can't recover the flag if its an image so I tried something else. Since the challenge name is viewport, maybe it could be a thumbnail cache
Went to the /Users/chaib/App Data/Local/Microsoft/Windows/Explorer and found a lot of iconcache and thumbnail cache

Can simply parse them using NirSoft tools to view the images. Skeptical at first cuz it looked like default windows images but found the flag in segments eventually in the bitmaps

Flag: SIBER25{V3RY_sMA1L_thUm8n411S}
