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


<details>
  <summary>Lab: Manipulating WebSocket messages to exploit vulnerabilities</summary>



1. go to live chat
2. write any message in chat and send it
3. intercpt request with burp
4. look at `websockets history` tab 

> ### to make the connection form front side to backend refresh the page

<img width="1259" height="418" alt="image" src="https://github.com/user-attachments/assets/78bc070d-d96f-4552-b504-cc5296e6f89e" />

5. send it to repeater
6. click connect then send it 

<img width="1876" height="507" alt="image" src="https://github.com/user-attachments/assets/cd9857e0-9ae6-4a16-8647-bff019b6e548" />

7. now what if we try to inject some thing or do xss 

### write 

```json
{"user":"You","content":"<img src=1 onerror='alert(1)'>"}
```

### instead of 

```
{"user":"You","content":"hello, i am hacker "}
```

## if send it from frontend found that html encoded

<img width="1000" height="141" alt="image" src="https://github.com/user-attachments/assets/2f81b5eb-fd32-46ac-99f8-74853da20895" />

## so send message and edit it first 

<img width="789" height="113" alt="image" src="https://github.com/user-attachments/assets/7bbf248b-edcc-4680-ad53-d8a3da2e9716" />

## then send it 

<img width="1680" height="521" alt="image" src="https://github.com/user-attachments/assets/4ae44db8-56d6-465e-a4fc-11123c97dd6d" />


  
</details>






























