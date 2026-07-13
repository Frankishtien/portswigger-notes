# JWT mitigation

<details>
  <summary>How can i test </summary>


[jwt-](https://github.com/Frankishtien/portswigger-notes/blob/main/JWT.md)


  
</details>

---

## how can i mitigate

<details>
  <summary>None Algorithm Attack</summary>


The Problem
-----------

The server accepts:

```
{
 "alg":"none"
}
```

Without a signature.

The Cause
---------

The code trusts the header:

```
algorithm = token.header.alg
verify(token, algorithm)
```

The Fix
-------

Specify the algorithm manually:

```
jwt.decode(
    token,
    SECRET_KEY,
    algorithms=["HS256"]
)
```

Do not do this:

```
algorithms = token.alg
```

  
</details>


<details>
  <summary>Algorithm Confusion (RS256 → HS256)</summary>



One of the most common issues with JWTs.

For example, the server uses:

```
RS256
```

This implies the use of a public/private key pair.

The problem arises when the application accepts:

```
HS256
```

And treats the public key as if it were a secret key.

Remediation:
--------

Specify the algorithm:

```
jwt.decode(
 token,
 public_key,
 algorithms=["RS256"]
)
```

Do not allow multiple algorithm types without a valid reason.

- ### ✅ Use separate keys per algorithm, never share keys



  
</details>


<details>
  <summary>Weak Secret Key (HS256)</summary>


If you have:

```
SECRET="password123"
```

That is a problem.

Because HS256 relies on the secret.

Recommendation:
--------

Use:

-   A long, random key
-   A Secret Manager

Example:

```
SECRET=os.environ["JWT_SECRET"]
```

It should be:

```
256-bit random value
```


  
</details>



<details>
  <summary>JWT without expiration</summary>


The Risk:

A token that lasts forever.

Example:

```
{
"user":"ali"
}
```

The Fix:
--------

Set:

```
{
 "sub":"123",
 "exp":1750000000
}
```

And on the server:

```
verify_exp=True
```

  
</details>


<details>
  <summary>Failure to verify claims</summary>


It is not enough to simply verify:

```
signature valid
```

You must also check:

exp
---

Has it expired?

iss
---

Who issued the token?

aud
---

Which service is it intended for?

Example:

```
jwt.decode(
 token,
 key,
 algorithms=["RS256"],
 issuer="my-api",
 audience="mobile-app"
)
```

  
</details>



<details>
  <summary>Sensitive Data Exposure</summary>



Incorrect:

```
{
"user":"ali",
"password":"123456",
"credit_card":"5555"
}
```

JWTs can be viewed.

Correct:
-----

```
{
"sub":"123",
"role":"user"
}
```

Keep sensitive data in the database.


  
</details>


<details>
  <summary>Token Theft</summary>


If the token is stolen, it's game over.

Causes:

-   LocalStorage
-   XSS
-   Lack of HTTPS

Solution:
--------

For the browser:

Use:

```
HttpOnly Cookie
Secure Cookie
SameSite
```

Example:

```
Set-Cookie:
token=xxxxx;
HttpOnly;
Secure;
SameSite=Strict
```

  
</details>


<details>
  <summary>No Token Revocation</summary>


Stateless JWT.

This means that if it is stolen, you cannot easily revoke it.

Solutions:

### Blacklist

Store:

```
token_id -> revoked
```

Or:

### Short-lived Access Token

For example:

```
Access Token: 10 minutes
Refresh Token: days
```

  
</details>


<details>
  <summary>Weak Token Validation</summary>


Incorrect:

```
decode(token, verify=False)
```

Or:

```
verify_signature=False
```

Correct:
--------

Always:

```
verify_signature=True
```

  
</details>



<details>
  <summary>Missing Audience Validation</summary>



A token for Application A is used in Application B.

Example:

```
mobile-api token
```

Used on:

```
admin-api
```

Solution:

```
{
"aud":"admin-panel"
}
```

And it is validated.


  
</details>


<details>
  <summary>Refresh Token Problems</summary>


Incorrect:

The refresh token has a one-year lifespan and is stored in LocalStorage.

Solution:

-   Rotation

Meaning:

With every refresh:

The old one expires.

A new one is generated.

  
</details>


<details>
  <summary>JWT Replay Attack</summary>

The attacker resends the same token.

Solutions:

Use:

```
jti
```

Example:

```
{
"jti":"8a72hd72"
}
```

And monitor it.

  
</details>


<details>
  <summary>Key ID </summary>

If you use:

```
{
"kid":"file/path"
}
```

and the server reads the key from it...

That is dangerous.

The solution:

Whitelist:

```
keys={
 "key1":PUBLIC_KEY
}

key=keys[token.kid]
```

  
</details>



<details>
  <summary>Missing valid permissions within the JWT.</summary>


Incorrect:

```
{
"role":"admin"
}
```

...and trusting it without validation.

Better:

-   Handle permissions server-side.
-   Or validate the role.
  
</details>














---





```
- Do not store secrets in the payload
- Use a standard algorithm
- Verify the signature
- Validate the expiration claim (`exp`)
- Validate the issuer (`iss`) and audience (`aud`)
- Use a strong secret
- Use short-lived access tokens
- Implement refresh token rotation
- Use HttpOnly and Secure cookies
- Do not store sensitive tokens in LocalStorage
- Implement token revocation when necessary
- Do not trust token roles without proper authorization checks
```



