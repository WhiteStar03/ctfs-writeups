# is-this-a-doc-file: Forensics

Flag Proof: `<35fsdeGHR>`

## Summary:

- There is a `docx` file give
- Running `binwalk` on it tells us that it is in fact an `7z` archive

![Untitled](is-this-a-doc-file%20Forensics%207fffb1e4d22245bcbacb2dc690c5ef21/Untitled.png)

- Extracting it gives us the `xml` behind the `docx` and some other stuff but also a  `pptx`

![Untitled](is-this-a-doc-file%20Forensics%207fffb1e4d22245bcbacb2dc690c5ef21/Untitled%201.png)

- Opening it reveals the flag

![Untitled](is-this-a-doc-file%20Forensics%207fffb1e4d22245bcbacb2dc690c5ef21/Untitled%202.png)