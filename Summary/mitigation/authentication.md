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
  <summary>..........</summary>

1.  **Password Change Security**
    -   Require the current password before changing it.
    -   Invalidate all other sessions after the password is changed.
    -   Notify the user that the password has been changed.
2.  **Password Reset Security**
    -   Do not reveal whether an email address is registered.
    -   Use a short-lived, random token.
    -   Invalidate the token after its first use.
3.  **Email Change Security**
    -   Require re-authentication (password or MFA).
    -   Verify the new email address before adopting it.
4.  **Multi-Factor Authentication (MFA)**
    -   Prevent bypassing the second factor due to logic errors.
    -   Securely store TOTP keys or MFA data.
    -   Provide recovery codes and protect them.
5.  **Session Management**
    -   Rotate the session ID after login or privilege escalation.
    -   Terminate the session upon logout.
    -   Define a session lifespan with an inactivity timeout.
6.  **Account Recovery**
    -   Do not rely on traditional security questions, as they are often easy to guess or discover.
    -   Use stronger verification methods, such as email or MFA.
7.  **OAuth / OpenID Connect**
    If using Google or GitHub login:
    -   Validate the `state` parameter to prevent CSRF.
    -   Validate the `redirect_uri`.
    -   Validate the `nonce` in OpenID Connect.
    -   Do not trust data received from the client without verifying it with the identity provider.
8.  **JWT Best Practices**
    -   Validate the signature, algorithm, and claims (`exp`, `iss`, `aud`).
    -   Keep the access token lifespan short.
    -   Store refresh tokens securely and ensure they can be revoked when necessary.
9.  **Logging & Monitoring**
    -   Log both successful and failed login attempts.
    -   Monitor for unusual activity (e.g., a high volume of attempts or logins from different locations within a short timeframe).
    -   Do not log passwords or tokens.  


  
</details>

---

<details>
  <summary></summary>
</details>
















































































































