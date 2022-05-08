# ray3 : reverse

Flag Proof: `CTF{62745be9d3888d69b35b0f80ec06c91e4d30ac5c42a2e18b0ee467108018f45e}`

## Summary:

- Using ghidra to see the code we find these hardcoded values:

```c
local_38 = 0x507c65776a7d7e66;
local_30 = 0x647c6f6c697c516c;
local_28 = 0x44;
```

- Putting them in the order results in the string: `f~}jwe|PlQ|ilo|dD`

```c
for (local_70 = 0; sVar1 = strlen(param_1), (ulong)(long)local_70 < sVar1;
        local_70 = local_70 + 1) {
      local_6c = local_6c + param_1[local_70];
    }
```

- This does the sum of the chars of the word `yeyy` stored in local_68

```c
for (; local_68 != 0x11; local_68 = local_68 + 1) {
      if ((local_68 & 1) == 0) {
        local_58[(int)local_68] = param_2[(int)local_68] + 4;
      }
      else {
        local_58[(int)local_68] = param_2[(int)local_68] - 4;
      }
      local_58[(int)local_68] = (byte)(local_6c / 30) ^ local_58[(int)local_68];
    }
```

- Now, the script iterates over the string, and if the value is odd, the character is the that char +4, else the char is that char -4, and then is doing an xor with  the ascii sum of `yeyy` divided by 30 which is 15.

```c
encoded = "f~}jwe|PlQ|ilo|dD"[::-1]
for i in range(len(encoded)):
    for char in range(0, 0xff): # go for all the chars
        if (i & 1) != 0:
            a = char - 4;
        else:
            a = char + 4;
        a ^= 15;
        if a == ord([i]):
            print(chr(char),end='')
```

![Untitled](ray3%20reverse%20df10c5cd320f471f876eb18c6fb1a8d2/Untitled.png)