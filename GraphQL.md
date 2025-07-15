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

