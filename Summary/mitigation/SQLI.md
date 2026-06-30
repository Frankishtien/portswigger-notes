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






















  
</details>

















































































```ruby
SQL Injection
│
├── WHERE
│      └── Prepared Statement
│
├── INSERT
│      └── Prepared Statement
│
├── UPDATE
│      └── Prepared Statement
│
├── DELETE
│      └── Prepared Statement
│
├── LIKE
│      └── Prepared Statement
│
├── IN
│      └── Prepared Statement
│
├── ORDER BY
│      └── Whitelist
│
├── GROUP BY
│      └── Whitelist
│
├── HAVING
│      └── Prepared Statement أو Whitelist حسب الاستخدام
│
├── LIMIT
│      └── Integer Validation
│
├── OFFSET
│      └── Integer Validation
│
├── Table Name
│      └── Whitelist
│
├── Column Name
│      └── Whitelist
│
├── Stored Procedure
│      └── لا تستخدم Dynamic SQL
│
├── ORM
│      └── تجنب Raw SQL غير المُعامل
│
└── Second-Order SQLi
       └── استخدم Prepared Statements دائمًا
```















