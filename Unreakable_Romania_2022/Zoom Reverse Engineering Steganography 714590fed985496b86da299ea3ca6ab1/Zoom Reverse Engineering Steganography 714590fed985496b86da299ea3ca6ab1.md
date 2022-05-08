# Zoom:Reverse Engineering / Steganography

Flag Proof: `CTF{58129455d17a904e2a5ace4aa245c2bf53118b76b57b68b8cacf076e9bc81b29}`

## Summary:

- We are given 2 files, and zoom.exe apparently has nothing to do with solving this challenge
- Going to `asm_image.inc` , reading it out and zooming out it reveals the message `12504`

![Untitled](Zoom%20Reverse%20Engineering%20Steganography%20714590fed985496b86da299ea3ca6ab1/Untitled.png)

- Hashing it using `sha256` gives us the flag we need.

![Untitled](Zoom%20Reverse%20Engineering%20Steganography%20714590fed985496b86da299ea3ca6ab1/Untitled%201.png)