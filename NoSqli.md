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





































  
</details>

























