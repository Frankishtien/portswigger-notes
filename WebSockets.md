# `WebSockets`


---


<details>
  <summary>What are WebSockets? 🟠</summary>

🧠 What does WebSocket mean? 
===================================

💡Basic idea:
-------------------

> WebSocket = an open connection between you and the server at all times

---

⚔️ The difference between HTTP and WebSocket
--------------------------

### 🟢http:

- I send Request
- The server responds
- And that's it 🔚

example:

```
GET /profile
```

→ The server responds → End

* * * * *

### 🔴WebSocket:

- You only make **connection once**
- And after that:
    - Send and receive at any time
    - Without a traditional request/response

💥 means:

> Real-time communication (chat / notifications / trading)


---


🔌 How does the connection start? (Handshake)
----------------------------------

### 1️⃣ The browser sends:

```http
GET /chat HTTP/1.1\
Upgrade: websocket\
Connection: Upgrade\
Sec-WebSocket-Key: random_value
```

### 2️⃣ The server responds:

```http
HTTP/1.1 101 Switching Protocols\
Upgrade: websocket
```

💥 This happened:

> Convert from HTTP → WebSocket

* * * * *

⚠️ Very important :
----------------------------

- In handshake:

```
Cookie: session=XXXX
```

👉 means:

> WebSocket depends on **session exactly like HTTP**

> This is the entrance to loopholes 👀

----

📦 What happens after that?
---------------------

Contact remains open
And we start sending **messages**, not requests

* * * * *

💬 Messages format
-------------------

### Simple example:

```
ws.send("hello")
```

* * * * *

### 🔥 Real example (JSON):

```
{
  "user": "ahmed",
  "message": "hello bro"
}
```

* * * * *

🧠 Why is this important in hacking?
---------------------------------

because:

- There is no strong validation sometimes
- There is no auth check ok
- There is no rate limit
- All logic remains in the messages 👀

* * * * *

💣 From your perspective as a Hacker
----------------------

Focus here 👇

### Instead of changing:

```
GET /api/user?id=5
```

### Will change:

```
{ "action": "getUser", "id": 5 }
```

💥 The same idea as IDOR, but in WebSocket

---



</details>


<details>
  <summary>Manipulating WebSocket traffic 🟠</summary>


🎯 The first thing: Intercepting WebSocket Messages
============================================

💡 Idea:
----------

As you did in HTTP:

> Intercept → Modify → Forward

> Same thing here... but replace request/response
> herre you have **messages**


---


🧪 Practical scenario 
-----------------------------------

Imagine Chat App 👇

### Original message:

```json
{
  "action": "sendMessage",
  "to": "user2",
  "message": "hello"
}
```

### 💣 You edit it:

```json
{
  "action": "sendMessage",
  "to": "admin",
  "message": "give me flag"
}
```

💥 If you Sent = **Access Control broken**

* * * * *

⚠️ Most important point:
------------

In WebSocket:

> There is no strong validation like HTTP endpoints

👉 This means playing all in:

- JSON fields
-parameters
-logic


----



🔁 Replaying & Generating Messages (Repeater)
=============================================

💡 Idea:
----------

> Not just modify... No, too **Replay the game at your convenience**

* * * * *

😈 Why is this important?
--------------

### 1️⃣ Brute Force

```json
{ "action": "login", "code": "1234" }
```

👉 You sit and change:

- 0000
- 1111
- 9999

💥 If there is no rate limit = jackpot

* * * * *

### 2️⃣ IDOR


```json
{ "action": "getMessages", "user_id": 5 }
```

👉 Try:

```json
{ "action": "getMessages", "user_id": 1 }
```

💥 If he gets someone else's data = Vulnerability

---


### 3️⃣ Hidden Actions 

Try changing:

```json
{ "action": "deleteUser" }
{ "action": "getAllUsers" }
{ "action": "adminPanel" }
```

💥 Sometimes the server has hidden actions 😈

* * * * *

🔌 Manipulating WebSocket Handshake (High Level)
====================================================


💡 This is a little advanced
-----------------------

Instead of playing in messages
👉 You play on ** connection itself **

* * * * *

🔥 When do we use it?
----------------

### 1️⃣ Change the Session

```
Cookie: session=XXXX
```

👉 Create another user session

💥 Account takeover possible

* * * * *

### 2️⃣ bypass authentication

If the server:

- It depends only on handshake
- He won't check anymore

👉 You just entered, okay?

* * * * *

### 3️⃣ Reconnect with a new token

- If the token has expired
- Or you want to try something new





---


🔥 CheckList can be accessed in any lab
=================================

1. Open Burp → WebSockets history
2. View all messages
3. Sell one Repeater
4. Edit:
    -IDs
    -actions
    -values
5. Try replay
6. Think:

    > “Can the server be more confident?” 😈








  
</details>




<details>
  <summary>WebSockets security vulnerabilities 🔴</summary>



  
</details>














































------


## **` Labs 🧪`**

































