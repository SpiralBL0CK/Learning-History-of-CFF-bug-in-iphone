# Learning-History-of-CFF-bug-in-iphone
Dive into CFF font and coincidently learn about a bof in cff parsing from some jailbreak. just for fun


So.... wtf is CFF ? So from my knowledge it's a file format , but let's try to understad what wikipedia says:"CFF acts as a container to store multiple fonts together in a single unit known as a FontSet. "(https://docs.fileformat.com/font/cff/) so basically we can store fonts together . cool so now what ? well since it's a file format it has to have some spec, and yes it does have one and no it's not nice cause it's 60 pages and i am not willing to learn from it's format. But we can use the exploit from star-master github repo(source code from jailbreak 2.0 i think) in order to learn about it's format.

So... We start by using cff.py script provided by the author and we also open out.cff(standard simple .cff file) file in hxd editor.![1](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/713e5655-1f55-4280-a439-e53a5de09d64) 

We can see that when we feed the file to the parse we get the folllowing
![1](https://github.com/SpiralBL0CK/Learning-History-of-CFF-bug-in-iphone/assets/25670930/29521729-462b-42a5-9ba9-f07381011c04)

So we have already done some progress as we can already come with a definition for cff as we can come to the conclusion that we have some metadata about it's header which indicates the tff versions i presume, the headersize,and absoffsize whatever that might be.

So until now 
|major version(1 byte)|minor version(1 byte)|header size(1 byte) | header absoffsize(1 bytes) |
