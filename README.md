# Learning-History-of-CFF-bug-in-iphone
Dive into CFF font and coincidently learn about a bof in cff parsing from some jailbreak. just for fun


So.... wtf is CFF ? So from my knowledge it's a file format , but let's try to understad what wikipedia says:"CFF acts as a container to store multiple fonts together in a single unit known as a FontSet. "(https://docs.fileformat.com/font/cff/) so basically we can store fonts together . cool so now what ? well since it's a file format it has to have some spec, and yes it does have one and no it's not nice cause it's 60 pages and i am not willing to learn from it's format. But we can use the exploit from star-master github repo(source code from jailbreak 2.0 i think) in order to learn about it's format.

So... We start by using cff.py script provided by the author and we also open out.cff(standard simple .cff file) file in hxd editor.![1](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/713e5655-1f55-4280-a439-e53a5de09d64) 

We can see that when we feed the file to the parse we get the folllowing
![1](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/29521729-462b-42a5-9ba9-f07381011c04)

So we have already done some progress as we can already come with a definition for cff as we can come to the conclusion that we have some metadata about it's header which indicates the tff versions i presume, the headersize,and absoffsize whatever that might be.

So until now 
|major version(1 byte)|minor version(1 byte)|header size(1 byte) | header absoffsize(1 bytes) |

We than go further and have a function which get's what fonts are being used in this .cff file. As seen

![Screenshot 2023-12-31 090700](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/7a30480c-f4c5-4b6c-8d77-d125a1fa6155)

So from where do we know that it's purpose is to read what fonts are being used ? Well upon running the tool we got the following result in cmd

![Screenshot 2023-12-31 090837](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/b18f2ba8-f87e-4705-8886-3b6d6ebde683)

which we see that are the next bytes after the metadata of the file

![Screenshot 2023-12-31 090921](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/a162524f-79d0-4160-8b4c-f38870d56248)

We will come back later to the analysis of this function later but for now we can deduce that the file format is
|major version(1 byte)|minor version(1 byte)|header size(1 byte) | header absoffsize(1 bytes) | ABCDEF+fonts in pack|

Further we see that we search for what is called string in the script:

![1](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/469a7826-f09d-40e0-9dc5-c72e3b7ebb79)

Why is that ? Because i presume that they want to gether info about the font used in the pack . From the docs, they say:"All the strings, with the exception of the FontName and CIDFontName strings which appear in the Name INDEX, used by different fonts within the FontSet are collected together into an INDEX structure and are referenced by a 2-byte unsigned number called a string identifier or SID.These strings, known as the standard strings, describe all the names used in the ISOAdobe and Expert character sets" . Anyways one interesting thing to note here is that we skipped roughly 41 bytes in order to get to the string.

![1](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/79b606ab-c340-44c7-aae6-e504ed205af3)

![1](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/6833be3b-a1ed-415a-ba4c-4fea1b28808b)

next we get information about the fonts present in the .cff file

![1](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/14f720e7-a8cb-4e6a-8d31-344b950997e3) 

How tf do we do that ? well we gather what's called top dict data . wtf is that ? well from what i was able to understand from the docs, is a python dict with certain information in it , encoded in a certain way.

![1](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/46814a81-f56f-42fc-a8e6-492380d588fb)

Coincidently, if we follow the decoding algo:

![1](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/ce1dba7c-3d0a-4c2d-8108-618db8f47bbb)

we dereference the strings data type to get info about the font, so we conclude that the topdicts simply has some indexes which later get used in strings data type to get info about font
