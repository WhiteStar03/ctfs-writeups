# hidden-easy: Steganography

Flag Proof: `CTF{F5FC0A9B8D5550AE82436A19A003DD53F3C76932F501071264A98AB454A1E22D}`

## Summary:

- We are given a broken image but with good headers.
- Contains the words :
    - dog
    - lazy
    - the
    - over
    - jumps
    - fox
    - brown
    - quick
- After trying a lot of steganography tools nothing seemed to be working
- Thought that maybe the number of appearances of each word would mean something after decoding but found nothing
- The solution was to delete all occurrences of the words and that would turn into a valid png

```python
words = [b'dog',b'lazy',b'the',b'over',b'jumps',b'fox',b'brown',b'quick',b'The']

f = open('hidden.png','rb')
file = f.read()

for word in words:
        file = file.replace(word,b"")

out = open('result.png','wb')
out.write(file)
```

![Untitled](hidden-easy%20Steganography%20bc099e30580840c699813564de057392/Untitled.png)

- The flag seems to be there but it is not complete
- After running some tools, `zsteg` gives us the flag extracting it via `lsb`

![Untitled](hidden-easy%20Steganography%20bc099e30580840c699813564de057392/Untitled%201.png)