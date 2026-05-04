#  Race Condition


* <details>

    
    # 🧨 Race Conditions in Web Applications
    
    ## 📌 What is a Race Condition?
    
    A **Race Condition** occurs when two or more concurrent requests interact with the same data at the same time, and the application fails to handle them properly. This causes **unexpected behavior**, allowing attackers to exploit it.
    
    ---
    
    ## 🎁 Example: Redeeming a Gift Card Multiple Times
    
    - A user sends **two requests at the same moment** to redeem a **single-use gift card**.
    - Both requests are processed **before the server marks the gift card as used**.
    - The attacker uses the same gift card **multiple times** → **logic broken**.
    
    ---
    
    ## 🕓 What is a Race Window?
    
    - The **Race Window** is the **small time gap** between:
      1. The server checking the data (e.g., gift card is unused)
      2. And the server updating the data (e.g., marking it as used)
    
    ---
    
    ## 🧠 Why is it a Logic Flaw?
    
    It's not just a technical bug — it's a **business logic vulnerability**.  
    The application **fails to lock access** to a critical resource during concurrent processing.
    
    ---
    
    ## 🔍 How to Detect and Exploit Race Conditions
    
    1. Identify sensitive features (e.g., coupons, balance transfers, password resets).
    2. Intercept the request using Burp Suite.
    3. Use:
       - **Burp Suite Repeater** → "Send in Parallel"
       - **Burp Turbo Intruder** → high-speed request fuzzing
    4. Analyze for unusual behaviors (e.g., duplicated discounts, double purchases).
    
    ---
    
    ## 🧪 PortSwigger Labs & Research
    
    - Labs provided by [PortSwigger](https://portswigger.net) to **practice safely**.
    - Inspired by real-world bugs and research from **Black Hat USA 2023**.
    - Advanced topics include:
      - Multi-step logic abuse
      - Race conditions in state machines
    
    ---
    
    ## 🧠 Summary Table
    
    | Term              | Meaning                                                       |
    |-------------------|---------------------------------------------------------------|
    | Race Condition     | Two or more actions on shared data collide                   |
    | Race Window        | Small time gap where the collision occurs                    |
    | Logic Flaw         | The code logic allows misuse (e.g., redeeming twice)         |
    | Exploitation Tools | Burp Suite Repeater, Turbo Intruder                         |
    
    ---
    
    ## ✅ Mitigations (for developers)
    
    - Use locking mechanisms (`mutex`, transactions).
    - Validate actions **on the server-side only**.
    - Implement **atomic operations** and **idempotent endpoints**.
    - Rate-limit and monitor critical requests.
    


    # 🧨 Race Conditions - Limit Overrun Exploitation
    
    ## 🔍 What is a Race Condition?
    
    A **race condition** occurs when multiple requests are processed **concurrently** and access shared data **without proper locking**, leading to unexpected or malicious behavior.
    
    ---
    
    ## 💣 Limit Overrun Race Conditions
    
    This specific type of race condition allows attackers to **exceed business logic limits**, such as:
    
    - Reusing a one-time **promo code**
    - Redeeming a **gift card** multiple times
    - Rating a product repeatedly
    - Bypassing **CAPTCHA**
    - Bypassing **rate limits** (e.g. login attempts)
    - Transferring funds exceeding your balance
    
    ---
    
    ## 🧠 Example: One-Time Discount Bypass
    
    ### Normal Flow:
    1. Check if user used the promo code before.
    2. Apply discount.
    3. Update database to mark it used.
    
    ### Race Condition Flow:
    Two requests sent **simultaneously** can both pass step 1 before the database updates → **Discount applied twice**.
    
    🕒 This small time gap is known as the **Race Window**.
    
    ---
    
    ## ⛓️ TOCTOU: Time-of-Check to Time-of-Use
    
    This is a subset of logic flaws where data is validated, but its state changes before it is used.
    
    ---
    
    ## 🛠️ Detecting and Exploiting
    
    ### ✅ Method:
    1. Find an endpoint with **single-use or rate-limited** functionality.
    2. Use **Burp Suite** (Intruder or Repeater) to send multiple **parallel** requests rapidly.
    3. Observe whether the limit is bypassed.
    
    ---
    
    ## 🚀 Burp Suite 2023.9+ Enhancements
    
    ### For HTTP/1:
    - **Last-byte synchronization**: Delays final byte to send all requests together.
    
    ### For HTTP/2:
    - **Single-packet attack** (⚡ Black Hat 2023): All requests fit in one TCP packet → removes network jitter issues.
    
    ### Why Many Requests?
    - Increases collision chances.
    - Useful in early discovery phase (testing timing).
    
    ---
    
    ## 📚 Further Reading
    - [Burp Repeater - Sending Requests in Parallel](https://portswigger.net/burp/documentation/desktop/tools/repeater/parallel-requests)
    - [Whitepaper - Smashing the State Machine](https://portswigger.net/research/smashing-the-state-machine)
    
    ---
    
    ## 🎯 Key Takeaways
    
    - Race conditions = time-based logic flaws.
    - Limit overruns = most common and dangerous.
    - Use Burp’s parallel features to exploit them.
    - Timing is everything 🕓
    
    



  </details>







<details>
    <summary>Hidden multi-step sequences</summary>



🔹 يعني إيه Hidden multi-step sequences؟
----------------------------------------

ببساطة:

فيه Request واحدة إنت بتبعتها للسيرفر...\
بس السيرفر من جواه بيعمل **أكتر من خطوة ورا بعض** (من غير ما تشوفهم).

يعني بدل ما الموضوع يكون:

```
Request → Response
```

بيبقى:

```
Request → Step1 → Step2 → Step3 → Response
```

الـ steps دي اسمها **sub-states** (حالات مؤقتة).

* * * * *

🔥 المشكلة فين؟
---------------

المشكلة إن أحيانًا في النص بين الخطوات دي بيبقى فيه **حالة مؤقتة مش مؤمنة كويس**.

زي المثال ده:

```
session['userid'] = user.useridif user.mfa_enabled:    session['enforce_mfa'] = True
```

💥 اللي بيحصل هنا:

1.  السيرفر بيعمل login → ويديك session ✅
2.  بعد كده يقرر يفعّل MFA
3.  في اللحظة اللي في النص → إنت **already logged in بس لسه MFA مش مفروض عليك**

* * * * *

😈 هنا بقى الهجوم (Race Condition)
----------------------------------

المهاجم يعمل إيه؟

يبعت **requestين في نفس اللحظة**:

1.  Login request
2.  Request لصفحة حساسة (زي dashboard)

لو الطلب التاني وصل في اللحظة دي 👇\
👉 قبل ما MFA يتفعّل

يبقى دخل من غير MFA 🔥

* * * * *

🧠 الفكرة العامة
----------------

إنت بتستغل:

> "لحظة صغيرة جدًا التطبيق فيها مش consistent"

* * * * *

🪜 Methodology (إزاي تلاقي وتستغل)
----------------------------------

### 1️⃣ Predict (تتوقع فين المشكلة)

اسأل نفسك:

-   هل endpoint ده مهم؟ (login -- payment -- reset password)
-   هل فيه أكتر من request ممكن تأثر على نفس الحاجة؟

مثال:

-   reset password لنفس اليوزر → ممكن collide
-   reset ليوزرين مختلفين → غالبًا لا

* * * * *

### 2️⃣ Probe (تجرب وتشوف)

تعمل خطوتين:

#### ✔️ الأول (baseline)

تبعت requests ورا بعض عادي (sequence)

#### ✔️ التاني (attack)

تبعتهم **في نفس الوقت (parallel)**

بـ:

-   Burp Repeater → Send in parallel
-   أو Turbo Intruder

* * * * *

### 👀 تدور على إيه؟

أي اختلاف:

-   response مختلف
-   status code غريب
-   behavior اتغير
-   email مختلف
-   أو حتى access غير متوقع

* * * * *

### 3️⃣ Prove (تثبت الهجوم)

-   شيل requests ملهاش لازمة
-   ركّز على أقل عدد requests ينجح الهجوم
-   كرر الهجوم وتتأكد إنه stable

* * * * *

🔥 مثال مهم (Multi-endpoint race)
---------------------------------

### سيناريو متجر 🛒

1.  تضيف item للسلة
2.  تدفع
3.  السيرفر:
    -   يتحقق من الدفع ✅
    -   بعد كده يؤكد الطلب

💥 لو لعبت في النص:

-   تدفع
-   وفي نفس اللحظة تضيف items تانية

👉 ممكن تاخد items **من غير ما تتحاسب عليها**

* * * * *

⚠️ مشكلة بتقابلك (Timing)
-------------------------

حتى لو بعت requests مع بعض، ممكن ميحصلش race ليه؟

### بسبب:

1.  تأخير الشبكة
2.  endpoint بطيء عن التاني

* * * * *

💡 الحل: Connection warming
---------------------------

تبعت request خفيف الأول (زي homepage)

ليه؟

👉 علشان تفتح connection\
👉 وتخلي باقي requests أسرع ومتقاربة في التوقيت

* * * * *

🧠 الخلاصة
----------

-   السيرفر أحيانًا بيعدي بحالات مؤقتة مش مؤمنة
-   لو لحقت الحالة دي → تكسب
-   ده بيحتاج:
    -   timing مضبوط
    -   requests متزامنة
    -   فهم للـ logic



    
</details>



<details>
    <summary>Abuse rate limits VS Connection Warming</summary>


🧠 أول نقطة: ليه أحيانًا الـ race condition بيفشل؟
==================================================

حتى لو بتستخدم:\
👉 single-packet attack (أقوى حاجة)

ممكن تلاقي:

> response times لسه مش ثابتة 😵

### 💥 ده معناه إيه؟

إن فيه **تأخير من السيرفر نفسه (back-end delay)**\
مش من الشبكة عندك

يعني:

-   السيرفر بيعالج requests بسرعات مختلفة
-   فـ requests مش بتوصل لنفس النقطة في نفس الوقت

👉 وبالتالي السباق بيبوظ ❌

* * * * *

🔥 الحل الأول: Connection Warming
=================================

(إنت شوفته قبل كده)

تبعت request خفيف الأول\
علشان:

-   تفتح connection
-   تقلل latency

✔️ لو اشتغل → تمام\
❌ لو لأ → نروح لحل أذكى

* * * * *

😈 الحل الأقوى: Abuse rate limits
=================================

💡 الفكرة ببساطة:
-----------------

السيرفر بيقول:

> "إنت بتبعت requests بسرعة... ههديك شوية 😏"

👉 فيعمل delay بنفسه

* * * * *

🧠 وإنت تستغل ده إزاي؟
----------------------

بدل ما تحاول تظبط timing من عندك\
👉 تخلي السيرفر هو اللي يعمل delay

* * * * *

⚔️ السيناريو:
-------------

1.  تبعت requests كتير جدًا (dummy requests)
2.  السيرفر يبطأ (rate limiting)
3.  requests الأساسية بتاعتك تيجي:\
    👉 وكلها تتعالج في نفس الوقت تقريبًا

💥 كده عملت:

> server-side synchronization

* * * * *

🎯 ليه ده أقوى من client delay؟
-------------------------------

### ❌ client delay:

-   بيعتمد على الشبكة
-   jitter عالي = فشل

### ✅ server delay:

-   السيرفر نفسه اللي بيظبط التوقيت
-   أدق بكتير

* * * * *








    
</details>




<details>
    <summary>Single-endpoint race condition</summary>



🔥 نقطة جامدة: Single-endpoint race condition
=============================================

بدل ما يكون عندك:

-   `/cart` + `/checkout`

لأ... هنا:

👉 نفس endpoint\
بس بقيم مختلفة

* * * * *

🧪 مثال: Password Reset
-----------------------

endpoint:

```
POST /reset-password
```

بيستخدم:

-   session

* * * * *

😈 الهجوم:
----------

تبعت requestين في نفس اللحظة:

### Request 1:

```
username = attacker
```

### Request 2:

```
username = victim
```

* * * * *

💥 اللي بيحصل جوه السيرفر:
--------------------------

### Thread 1:

```
session['reset-user'] = attacker
```

### Thread 2:

```
session['reset-user'] = victim
```

* * * * *

لكن التوكن؟

```
session['reset-token'] = 1234
```

ممكن يتبعت للـ attacker 😈

* * * * *

😱 النتيجة الخطيرة:
-------------------

```
session['reset-user'] = victimsession['reset-token'] = 1234
```

👉 التوكن بتاع الضحية\
👉 عندك إنت

🎉 تقدر تغيّر باسورده

* * * * *

⚠️ ليه الهجوم ده صعب؟
=====================

لازم يحصل ترتيب معين:

1.  user يتغير للـ victim
2.  التوكن يتبعت للـ attacker

👉 timing دقيق جدًا\
👉 محتاج محاولات كتير

* * * * *

💌 ليه الإيميلات target ممتاز؟
==============================

لأن:

👉 غالبًا بتتبعت في background thread

يعني:

```
1\. السيرفر يرد HTTP response2\. بعد كده يبعت الإيميل
```

💥 ده بيخلق race window كبير

    
</details>







































-------------------------

<details>
  <summary>Lab: Limit overrun race conditions</summary>





> 1. stop the request using ``burpsuite``
> 2. send to ``repeater``
> 3. click ``ctrl`` **+** ``R`` to repeat this tap alot of times (here i do it 30 times)
> 4. from ``...`` on right click ``create tap group``
> 5. select all of them
> 6. instead send one request click **``send group in parallel``**



![image](https://github.com/user-attachments/assets/a84ddf42-bea5-4faf-b4cc-794d45463341)

 
</details>






<details>
    <summary>Lab: Bypassing rate limits via race conditions</summary>

<img width="1414" height="858" alt="image" src="https://github.com/user-attachments/assets/02a3653f-ca20-4657-9b9b-eab50570d559" />





## select password and send it to turbo intruder and select **`examples/race single packet attack.py`**

<img width="763" height="162" alt="image" src="https://github.com/user-attachments/assets/9fee64ab-4e82-4b62-9948-8b336f9ffe10" />



## edit it 

```python

passwords = ['']

# the 'gate' argument withholds the final byte of each request until openGate is invoked
for password in passwords:
    engine.queue(target.req, password, gate='1')

# once every 'race1' tagged request has been queued
# invoke engine.openGate() to send them in sync
engine.openGate('1')
```



## in console write :

```javascript
`123123
abc123
football
monkey
letmein
shadow
master
666666
qwertyuiop
123321
mustang
123456
password
12345678
qwerty
123456789
12345
1234
111111
1234567
dragon
1234567890
michael
x654321
superman
1qaz2wsx
baseball
7777777
121212
000000`.split("\n")
```

### **`output`** `right click` -> `copy object`

```ruby
['123123', 'abc123', 'football', 'monkey', 'letmein', 'shadow', 'master', '666666', 'qwertyuiop', '123321', 'mustang', '123456', 'password', '12345678', 'qwerty', '123456789', '12345', '1234', '111111', '1234567', 'dragon', '1234567890', 'michael', 'x654321', 'superman', '1qaz2wsx', 'baseball', '7777777', '121212', '000000']
```

## or U can just take passwords form clipboard

```python
def queueRequests(target, wordlists):

    # if the target supports HTTP/2, use engine=Engine.BURP2 to trigger the single-packet attack
    # if they only support HTTP/1, use Engine.THREADED or Engine.BURP instead
    # for more information, check out https://portswigger.net/research/smashing-the-state-machine
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           engine=Engine.BURP2
                           )

    # the 'gate' argument withholds part of each request until openGate is invoked
    # if you see a negative timestamp, the server responded before the request was complete
    for password in wordlists.clipboard :
        engine.queue(target.req, password, gate='1')

    # once every '1' tagged request has been queued
    # invoke engine.openGate() to send them in sync
    engine.openGate('1')


def handleResponse(req, interesting):
    table.add(req)

```

<img width="1558" height="397" alt="image" src="https://github.com/user-attachments/assets/c9409b22-fadb-44e0-9c37-8343955a3cf7" />

<img width="1653" height="391" alt="image" src="https://github.com/user-attachments/assets/0f10610a-4dea-400d-a116-ba0982e68863" />




    
</details>




<details>
    <summary>Lab: Multi-endpoint race conditions</summary>


1. login as **`weiner`**
2. add gift card to cart (send request to repeater)
3. click checkout (save request in repeater)
4. now use the copon of card in home (now you still have 100$)
5. add jacket to cart (save request in repeater)
6. remove jacket form cart
7. add copon card to cart
8. send two requests in parrallel (add jacket & checkout)


<img width="1439" height="479" alt="image" src="https://github.com/user-attachments/assets/a227880b-620b-4367-98d3-0a61a048128a" />


## find that i can buy jackt just with 100$ only 

<img width="1625" height="632" alt="image" src="https://github.com/user-attachments/assets/39fff226-f4d6-4fc4-906b-a4cd09deb32c" />








```
Request A (checkout) starts
    ↓
The server sees: You only have a gift card → The money is enough ✅
    ↓
(I still haven't confirmed the order)
    ↓
Request B (add jacket) is delivered
    ↓
The jacket is added to the basket 😈
    ↓
The server completes the checkout → confirms the request
```





<details>
    <summary>explain</summary>


🧠 الفكرة العامة للاب
=====================

إنت عندك store فيه:

-   Gift card (فلوس)
-   Jacket غالي

💥 المطلوب:

> تشتري الجاكيت **من غير ما يكون عندك فلوس كفاية**

إزاي؟\
عن طريق **race condition بين add to cart و checkout**

* * * * *

🔍 أول مرحلة: Predict (نتوقع فين المشكلة)
=========================================

إنت لاحظت endpoints مهمة:

-   `POST /cart` → بيضيف حاجة للسلة
-   `POST /cart/checkout` → بيعمل checkout

### 💡 نقطة مهمة:

السلة مربوطة بـ:

-   session بتاعتك

يعني:\
👉 أي request بيأثر على نفس السلة\
👉 يبقى فيه **collision potential**

* * * * *

🔥 الفكرة الخطيرة هنا
=====================

السيرفر لما تعمل checkout:

غالبًا بيعمل كده:

```
1\. يتحقق من الفلوس (gift card)2\. لو كفاية → يكمل3\. يأكد الطلب
```

💥 المشكلة:

فيه وقت صغير بين:

-   validation (الفلوس كفاية؟)
-   confirmation (تم الشراء)

* * * * *

⚠️ الهدف بتاعك
==============

تدخل request تاني في النص:

👉 تزود item (الجاكيت)\
👉 بعد ما السيرفر check الفلوس\
👉 وقبل ما يأكد الطلب

* * * * *

🧪 المرحلة التانية: Benchmark
=============================

### بتعمل إيه؟

بتبعت:

-   `POST /cart`
-   `POST /cart/checkout`

### في الأول:

تبعتهم **sequence (ورا بعض)**

لاحظت:

👉 request الأول بياخد وقت أطول

* * * * *

💡 ليه عملنا connection warming؟
================================

لما ضفت:

-   `GET /` الأول

لقيت:

-   باقي requests بقوا قريبين في الوقت

### الاستنتاج:

👉 التأخير مش من processing\
👉 ده من network (connection setup)

✔️ يبقى safe نكمل attack

* * * * *

🧪 تجربة عادية (بدون race)
==========================

حطيت:

-   jacket في `/cart`

وبعدين checkout

💥 النتيجة:

> ❌ insufficient funds

وده طبيعي 👍

* * * * *

😈 المرحلة المهمة: Prove (الهجوم)
=================================

دلوقتي بقى نلعب صح:

🎯 الإعداد:
-----------

-   خليك حاطط **gift card بس** في السلة

* * * * *

⚔️ الهجوم:
----------

في Repeater:

### عندك requestين:

1.  `POST /cart`\
    👉 تضيف الجاكيت (productId=1)
2.  `POST /cart/checkout`\
    👉 تعمل checkout

* * * * *

🔥 بدل ما تبعتهم ورا بعض:
-------------------------

ابعتهم **parallel (في نفس اللحظة)**

* * * * *

💥 إيه اللي بيحصل فعليًا؟
=========================

السيناريو الناجح:

```
Request A (checkout) يبدأ
    ↓
السيرفر يشوف: عندك gift card بس → الفلوس كفاية ✅
    ↓
(لسه ما أكدش الطلب)
    ↓
Request B (add jacket) يوصل
    ↓
الجاكيت يتضاف للسلة 😈
    ↓
السيرفر يكمل checkout → يأكد الطلب
```

🎉 النتيجة:

> اشتريت الجاكيت من غير ما تدفع تمنه

* * * * *

😵 ليه ساعات بيفشل؟
===================

عشان race condition:

-   محتاج timing مظبوط جدًا
-   مش كل مرة هتظبط

👉 عادي تعيد المحاولة كذا مرة

    
</details>




    
</details>



<details>
    <summary>Lab: Single-endpoint race conditions</summary>

1. login as wiener
2. change email to **`carlos@ginandjuice.shop`** (save request  in repeater)
3. change email again to **`wiener`** email (save request in repeater)
4. now i will try to send these two request in parallel and see if carlos email will sent to weiner emial

> ## when send request first time it send to me wiener email i send it again and booom 

<img width="1597" height="474" alt="image" src="https://github.com/user-attachments/assets/8fc75672-2bb9-478b-8fe5-02a5cb26a5f0" />

## we get carols link

<img width="1715" height="538" alt="image" src="https://github.com/user-attachments/assets/24449d39-0eb3-46ae-a6aa-67c3a28f4edf" />


---

<img width="1497" height="397" alt="image" src="https://github.com/user-attachments/assets/880170d3-ad8a-4b36-bfc2-a822a35ed0e4" />

<img width="1417" height="338" alt="image" src="https://github.com/user-attachments/assets/2a389141-ba40-46b1-8994-eda6f78c3064" />



<details>
    <summary>explain</summary>



🧠 الفكرة العامة للاب
=====================

إنت عندك feature:

> تغيير الإيميل

والسيستم بيعمل:

1.  تحفظ الإيميل الجديد (pending)
2.  يبعت confirmation link على الإيميل
3.  لما تضغط اللينك → يتغير الإيميل

* * * * *

🔍 أول مرحلة: Predict (نتوقع المشكلة)
=====================================

💡 تجربة مهمة:
--------------

غيّرت الإيميل مرتين ورا بعض:

-   test1@exploit...
-   test2@exploit...

لاحظت:\
👉 اللينك الأول بقى invalid

* * * * *

🧠 الاستنتاج:
-------------

> السيستم بيخزن **pending email واحد بس**

يعني:

```
pending_email = last_value
```

💥 ده معناه:\
أي request جديدة → **بتعدل نفس القيمة**

👉 يبقى فيه **collision potential** 🔥

* * * * *

🧪 المرحلة التانية: Benchmark
=============================

عملت إيه؟

-   بعت 20 request (كل واحد بإيميل مختلف)
-   sequence (ورا بعض)

### النتيجة:

✔️ كل request جاله email لوحده

👉 كله طبيعي 👍

* * * * *

😈 المرحلة المهمة: Probe (parallel)
===================================

بعت نفس الـ 20 request:

👉 لكن **في نفس اللحظة (parallel)**

* * * * *

👀 لاحظت حاجة غريبة:
--------------------

الإيميلات اللي وصلت:

❌ مش دايمًا:

-   To = نفس الإيميل اللي طلبته

💥 فيه mismatch!

* * * * *

🔥 ده حصل ليه؟
==============

خلّينا نشوف السيرفر بيعمل إيه:

```
1\. يخزن pending_email2\. يبدأ process إرسال الإيميل (async)3\. يقرأ من database عشان يعمل email template4\. يبعت الإيميل
```

* * * * *

😈 المشكلة (race window)
------------------------

بين:

-   step 2 (بدأ الإرسال)
-   step 3 (قرأ من database)

👉 إنت بتغير pending_email

* * * * *

💥 النتيجة:
-----------

```
الإيميل اتبعت لـ Aلكن المحتوى جاي من B
```

يعني:

-   To: attacker
-   Body: victim email

🔥🔥🔥

* * * * *

⚔️ المرحلة الأخيرة: Prove (الهجوم الحقيقي)
==========================================

🎯 هدفك:
--------

تخلي confirmation link بتاع **carlos**\
يوصلك إنت

* * * * *

🧪 تعمل إيه؟
------------

في Repeater:

### Request 1:

```
email = anything@exploit-server
```

### Request 2:

```
email = carlos@ginandjuice.shop
```

* * * * *

⚡ تبعتهم parallel
-----------------

* * * * *

💥 السيناريو الناجح:
====================

```
1\. السيرفر يبدأ يبعت إيميل لـ attacker2\. في النص pending_email يتغير لـ carlos3\. السيرفر يقرأ data → يلاقي carlos4\. يبعت إيميل:   To: attacker   Body: carlos confirmation link
```

* * * * *

🎉 النتيجة:
===========

إنت معاك:\
👉 confirmation link بتاع carlos

* * * * *

😈 تعمل إيه بعد كده؟
====================

1.  تضغط اللينك
2.  الإيميل بتاعك يتحول لـ:

    ```
    carlos@ginandjuice.shop
    ```

* * * * *

🔓 ليه ده مهم؟
==============

لأن:\
👉 carlos = admin


    
</details>


    
</details>





<details>
    <summary>Lab: Exploiting time-sensitive vulnerabilities</summary>


1. when login as wiener click forget passowrd
2. in http history send **`GET`** and **`POST`** requests to `forget-password` and doblcate the **`post`**

> ### now if you try to send two requests in same time you will found that in inbox of wiener two mails arrive at same time but diffrante token

## if php found two requests with same cookie at same time server will run them sequential so let's bypass it

1. send **`GET`** request to `forget-passowrd` but without **`cookie`**

<img width="1465" height="333" alt="image" src="https://github.com/user-attachments/assets/46b6983d-d4a5-410b-97f3-f229ace07264" />

## now we get new cookie and also new **`csrf_token`**

```
4seXxNHQ92uhB60yPXc78BXimomPV1pt
```


## let's take them and put them in one of two **`POST`** requests then send them 

> ## found that token send twice with same value

<img width="1760" height="716" alt="image" src="https://github.com/user-attachments/assets/4e948039-4884-48d9-af5e-e6790cc3a517" />

## now just change one request of them username to carlos


<img width="1786" height="646" alt="image" src="https://github.com/user-attachments/assets/2605f257-bc51-4b8c-87a9-0b85e82f61ee" />

## now let's logiin as calros

<img width="1547" height="294" alt="image" src="https://github.com/user-attachments/assets/75ecfe89-3849-4a2e-9be1-ba65a3cc559b" />



</details>































































