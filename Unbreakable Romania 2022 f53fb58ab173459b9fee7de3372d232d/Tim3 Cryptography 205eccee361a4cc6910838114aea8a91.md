# Tim3 : Cryptography

## Flag proof:

- `CTF{7edc257442d67b8fe4219d5eeb79d3952d462dc78f8ec8baaea5789fe42884af}`

## Summary

- After looking at the title of the challenge i knew that it has to be something related to a side-channel attack , timing attack as `picoCTF` had a similar challenge on this year’s event and the fact that we only get an input form for a password  made it the only way possible (it isn’t in pwn category).
- So i’have create a python script to do the job for me

```python
#!/usr/bin/python
from pwn import *
import time
import string
context.log_level = 'error'

wordlist = string.ascii_lowercase + string.ascii_uppercase + string.digits

passw = ''

print(wordlist)
flag = ''
max_time  = 0
mxf = 0
mx = 0
part = ''
output = b''
ok = 1
while ok:
    for c in wordlist:
        print(passw+c,end='\r')
        mx = 0
        p = remote("34.107.45.139", 31202)
        p.recv()

        before = time.time()
        #print(passw+c)
        p.sendline((passw+c).encode())
        output = p.recv()
        #print(p.recv().decode())
        p.close()
        if(b'CTF' in output):
            ok = 0
            flag = output
        after = time.time()
        mx = after-before
        if(mx > mxf):
            mxf = mx
            part = c
    passw = passw + part

print(b'Flag: ' + flag)

print('Pass: ' + passw)
```

- It iterates through all the characters that match the regex presented to us
- For each character it uses the time library to get the current time, then sends the password and calculates the time it takes for the program to send back the result to us. Based on that, for each character it takes the char whose process time take the longest and adds it to our password. When we get `CTF` returned from the server, we know it got our password correctly and also our flag.

![Untitled](Tim3%20Cryptography%20205eccee361a4cc6910838114aea8a91/Untitled.png)