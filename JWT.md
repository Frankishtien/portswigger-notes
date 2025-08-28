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


















