#  Race Condition

<details>
  <summary>Lab: Limit overrun race conditions</summary>

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



> 1. stop the request using ``burpsuite``
> 2. send to ``repeater``
> 3. click ``ctrl`` **+** ``R`` to repeat this tap alot of times (here i do it 30 times)
> 4. from ``...`` on right click ``create tap group``
> 5. select all of them
> 6. instead send one request click **``send group in parallel``**



![image](https://github.com/user-attachments/assets/a84ddf42-bea5-4faf-b4cc-794d45463341)

 
</details>









































































