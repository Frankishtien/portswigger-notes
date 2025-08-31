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

----


