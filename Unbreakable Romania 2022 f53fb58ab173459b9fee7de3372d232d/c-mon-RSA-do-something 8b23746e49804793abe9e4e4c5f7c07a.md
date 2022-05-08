# c-mon-RSA-do-something

## Flag Proof:

- `CTF{n3v3r_u53_th3_54m3_m0dulu5}`

## Summary:

- This challenge gives us 2 public keys and two cipher texts.
- Based on the description `nothing unCOMMON` , it suggests us to use and `[RSA-Common-Modulus-Attack](https://github.com/HexPandaa/RSA-Common-Modulus-Attack)` , but this script has to be changed a bit, because it expects our cipher texts as bytes, and we have them as integer.
- Modifying the main function to be like this, creating a file for each cipher text we got `c1` for the first, `c2` for the second, then running the script gets us the flag.

![Untitled](c-mon-RSA-do-something%208b23746e49804793abe9e4e4c5f7c07a/Untitled.png)

![Untitled](c-mon-RSA-do-something%208b23746e49804793abe9e4e4c5f7c07a/Untitled%201.png)