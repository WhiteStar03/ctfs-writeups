# noise-document: Misc

1. Flag Proof : `46ww9433432034253`

## Summary:

- Running `binwalk -e` on the given file returns a zip archive `0.zip`
- Running `zip2john` and cracking it with `john` gives us the password for the zip : `987654321`

![Untitled](noise-document%20Misc%20fae0fe29f2d54fb8946666c07ca3b853/Untitled.png)

- Running `binwalk -e` on the extracted pdf reveals an apk file:
    
    ![Untitled](noise-document%20Misc%20fae0fe29f2d54fb8946666c07ca3b853/Untitled%201.png)
    

- Used an online [decompiler](http://www.javadecompilers.com)
- The first flag is in a common apk file `strings.xml` which is in the downloaded file from the decompiler in `unr-release_source_from_JADX/resources/res/values`:
    
    ![Untitled](noise-document%20Misc%20fae0fe29f2d54fb8946666c07ca3b853/Untitled%202.png)
    

1. Flag Proof: `THISizYourseconfss4456flagzz3edfgks`
- For the second question one i used [this](https://mobsf.live/static_analyzer/?name=unr-release.apk&checksum=70b782dc57cb34d505fe5bed2d1b37da&type=apk) which does a lot of stuff by itself
- It shows 2 activities :
    - `unr.unr.MainActivity`
    - `unr.unr.LoggedInActivit`
    
    ![Untitled](noise-document%20Misc%20fae0fe29f2d54fb8946666c07ca3b853/Untitled%203.png)
    
- Using `find . -name "*logged*"` on the root folder of the decompilation gives us the file for that view.

![Untitled](noise-document%20Misc%20fae0fe29f2d54fb8946666c07ca3b853/Untitled%204.png)

- The flag is inside

![Untitled](noise-document%20Misc%20fae0fe29f2d54fb8946666c07ca3b853/Untitled%205.png)

- Using an [affine](https://www.dcode.fr/affine-cipher) cipher decrypter gives us the flag

![Untitled](noise-document%20Misc%20fae0fe29f2d54fb8946666c07ca3b853/Untitled%206.png)