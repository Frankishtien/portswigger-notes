# authentication mitigation 

<details>
  <summary>1. Weak Password Policy</summary>


-   Minimum length: **8 or 12** characters.
-   Must contain an **uppercase letter (A-Z)**.
-   Must contain a **lowercase letter (a-z)**.
-   Must contain a **number (0-9)**.
-   Must contain a **special character** such as:

    ```
    ! @ # $ % ^ & * ( ) _ + - = ? . ,
    ```

    -   No spaces allowed.
    -   Cannot include the username or email address within the password.
    -   Cannot use common passwords such as:

    ```
    password
    Password123
    admin123
    qwerty
    12345678
    ```

  
</details>


---

<details>
  <summary>2. Password Stored in Plain Text</summary>


### The Problem

Storing the password in plain text.

❌

```
password = "123456"
```

* * * * *

### The Correct Approach

Use:

-   Argon2 (currently the best choice)
-   bcrypt
-   scrypt

Example:

```
hashed = argon2.hash(password)
```

And upon login:

```
argon2.verify(password, hashed)
```




  
</details>

---

<details>
  <summary>3. Weak Hash Algorithm</summary>

### Incorrect

```
MD5
SHA1
SHA256 only
```

These are not suitable for storing passwords.

### Correct

```
Argon2
bcrypt
scrypt
```

  
</details>


---

<details>
  <summary>4. User Enumeration</summary>


### The Problem

The program says:

```
User not found
```

Or, if the username exists:

```
Wrong password
```

The attacker learns which users exist.

### Prevention

Always return the same message.

```
Invalid username or password
```

  
</details>

---

<details>
  <summary>5. Timing Attack</summary>


### The Problem

If the application responds quickly when the user does not exist,\
but slowly when the password is incorrect,

an attacker can measure the time.

### Prevention

Use constant-time comparison.

Example:

```
hmac.compare_digest()
```

  
</details>


---

<details>
  <summary>6. Brute Force</summary>


### The Problem

Trying thousands of passwords.

### Prevention

-   Rate limiting
-   Temporary account lockout
-   CAPTCHA after a certain number of attempts
-   MFA

Example:

```
5 attempts

↓

Lockout for 15 minutes
```


  
</details>


---

<details>
  <summary>7. Credential Stuffing</summary>


### The Problem

Using passwords leaked from other sites.

### Prevention

-   MFA
-   Checking passwords against breach databases
-   Detecting unusual login attempts

  
</details>


---


<details>
  <summary>8. Session Fixation</summary>


## The Concept

The attacker causes the victim to use a Session ID known to the attacker.

After login:

If the session does not change:

The attacker possesses a valid session.

---

## Protection

After login:

```
Old Session

↓

Destroy

↓

New Session ID
```
  
</details>

----

<details>
  <summary>9. Session Hijacking</summary>


### The Problem

Session cookie theft.

### Prevention

Cookies:

```
HttpOnly

Secure

SameSite
```

And always use HTTPS.
  
</details>


---


<details>
  <summary>10. Session Never Expires</summary>
</details>


---

<details>
  <summary>11. weak Password Reset </summary>


### The Problem

A reset link that does not expire or is guessable.

### Prevention

-   Strong, random token.
-   10–15 minute validity period.
-   Single-use.
-   Invalidate all old tokens after use.
  
</details>


----

<details>
  <summary>12. MFA Bypass</summary>


### The Problem

The server relies solely on the frontend to determine that the user has completed the second factor.

### Prevention

Verify on the server that the user has passed MFA before issuing a full authentication session.
  
</details>


---

<details>
  <summary>13. Default Credentials</summary>

### The Problem

```
admin
admin
```

### Prevention

-   Enforce a password change upon the first login.
-   Remove default accounts if possible.

  
</details>


---


<details>
  <summary>14. Sensitive Data in Logs</summary>


### Error

```
Login:

username=admin

password=123456
```

### Prohibition

Do not log:

-   Password
-   OTP
-   Session
-   JWT
-   Access Token

  
</details>


---

<details>
  <summary>15. Insecure JWT</summary>


### The Problem

-   Accepting an insecure algorithm.
-   Failure to verify the signature.
-   Lack of an expiration time.

### Prevention

-   Verify the signature.
-   Use a strong algorithm.
-   Validate:
    -   `exp`
    -   `iss`
    -   `aud`

Do not accept unsigned tokens.
  
</details>

---

<details>
  <summary>16. Missing Logout</summary>


### The Problem

Clicking "Logout" does not invalidate the session on the server.


  
</details>


---

<details>
  <summary>No Email Verification</summary>


### The Problem

Creating fake accounts or using email addresses not owned by the user.

### Prevention

Do not activate the account until the email address is verified.

  
</details>


---

<details>
  <summary>Reauthentication Missing</summary>


### The Issue

Anyone with access to the active session can directly change the email address or password.

### Prevention

Require the user to re-enter their password (or complete MFA) before performing sensitive operations, such as:

-   Changing the password.
-   Changing the email address.
-   Deleting the account.

  
</details>



---

<details>
  <summary></summary>
</details>


















































































































