# medical-instrument: Forensics

1. **What is the secret code that is hidden inside the disk image file? (Points: 140)**

Flag Proof: `BAHIMNJDJNMNMNMNMNMF`

## Summary:

We are given an `vmdk` disk image

- Needs to be converted to raw for putting it in autopsy or for mounting it
    - `qemu-img convert unr-1.vmdk -m 16 -p -O raw converted.raw`
- Running `binwalk -e` on it and extracting the `linux` partition was the best/fastest way for me to do it
    - `binwalk -e ~/ctfs/unbrk2022/medical-instrument/converted.raw --run-as=root`
- Also tried with `dd` with the addresses autopsy showed for the `linux` partition but for some reason it didn’t work)
    - `dd if=converted.raw of=extracted bs=1 skip=1052672 count=82831359`
- After running the `binwalk` and stopping the process after the partition was extracted we can mount it
    - `sudo mount 20200000.ext /mnt/`
- Now we have the filesystem and can access it
- In the `desktop` folder we find some interesting files

![Untitled](medical-instrument%20Forensics%2025ee1de05cb346709663776730177a36/Untitled.png)

- Opening `SecretCode.png` we find the flag for the first question

![Untitled](medical-instrument%20Forensics%2025ee1de05cb346709663776730177a36/Untitled%201.png)