# medical-instrument:(2-3)

### 2. How many .rpm headers are found inside the disk image file? (Points: 420)

Flag Proof: `66706`

## Summary:

- We have to find out how many packages are installed in the `vmdk` disk file
- The title hints us to the tool we have to use and that is `scalpel`
- After installing it, we have to uncomment  from the `/etc/scalpel/scalpel.conf` the line searching for rpm packages
- Running `scalpel unr-1.vmdk` gives us the number we need

![Untitled](medical-instrument%20(2-3)%206fe55b0e10b74c3ca0f34122b7ed6fc0/Untitled.png)

## 3. What is the secret hidden in the .pdf files? (Points: 220)

Flag Proof: `OH-godzz-FLAgZZZZZss-%%#$2&`

## Summary:

- Running `exiftool` on `sample.pdf` file from the disk image `/home/bitsentinel/desktop/sample.pdf` gives us the flag

![Untitled](medical-instrument%20(2-3)%206fe55b0e10b74c3ca0f34122b7ed6fc0/Untitled%201.png)