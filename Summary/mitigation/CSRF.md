# CSRF mitigation


<details>
  <summary>How To Get</summary>


- **`Search on any action like update email , change password and generate CSRF POC`**

  
</details>


---



<details>
  <summary>How can i test & Bypasses</summary>


-   check if the token is static
-   if there is no validation on the token
-   check if the token is guessable
-   if the token have error it can pass (make the token let's say something like array) → []
-   anything in the cookie didn't used for validation because it is saved in the browser so he will send it with the request
-   check if there is CSRF token or not if not so it may be vulnerable
-   try to remove the referrer and if it is the same response as the normal request so it may be vulnerable
-   try also remove the origin and if it is the same response as the normal request so it may be vulnerable
-   try also remove the content-type and if it is the same response as the normal request so it may be vulnerable
-   remove the CSRF Token or change on it
-   Change the request method from POST to GET
-   check if csrf token is tied to user session
-   you can't set headers in HTML
-   If i have 2 accounts change the CSRF Token with the second account CSRF Token
-   if he validate on the referrer tell him there is no referrer → `<head><meta name="referrer" content="no-referrer"></head>`
-   check if he validate in only part of the referrer , remove it part by part
  
</details>


----


<details>
  <summary>how can i mitigate</summary>

## -  CSRF Token 

<details>
  <summary> - SameSite</summary>


-   Example → Set-Cookie: sessionid=abc123; SameSite=Lax
-   Prevent CSRF : hacker send victim link to change his email by the cookie that saved in the victim browser
-   samesite prevent changing any thing if it is not from the same domain , because it check for domain also not only the cookie

- <details>
    <summary>Strict🔴</summary>

  -   Cookies won't sent if the request from another domain even if it is GET, POST
  -   What it does
  -   Cookie is sent **ONLY** when:
      -   User is already on the same site
  -   Cookie is **NOT sent** for **any cross-site navigation**
  -   Example
      -   User clicks a link on `evil.com`:
  
          ```
          <ahref="<https://bank.com/transfer>">Transfer</a>
  
          ```
  
  ➡️ Request to `bank.com`
  
  ➡️ **Cookie NOT sent**
  
  ➡️ User appears logged out


  </details>


- <details>
    <summary>Lax🟠</summary>

  
  -   the cookie can sent if the request is GET
  -   but in POST Request changing something it will blocked cookies won't sent


  </details>


- <details>
    <summary>None🔵</summary>

  - Cookie will sent even if it is GET or POST request


  </details>



-   SameSite=Strict → block
-   SameSite=Lax → allow GET only
-   SameSite=None → allow all


  
</details>




<details>
  <summary>- SOP (Same-Origin Policy)</summary>


-   **A web page can only read data from another page if both have the SAME** 3 things :
    -   Protocol
    -   Port
    -   Host
 


- <details>
    <summary>details</summary>

  
  Example
  
  You have
  
  ```
  https://facebook.com
  ```
  
  open, and you also have
  
  ```
  https://evil.com
  ```
  
  open.
  
  Can the JavaScript on
  
  ```
  evil.com
  ```
  
  execute
  
  ```
  fetch("https://facebook.com/profile")
  ```
  
  and read the response?
  
  **No**.
  
  The SOP prevents it.
  
  * * * * *
  
  
  What is an "Origin"?
  ----------------
  
  An Origin consists of three components:
  
  ```
  Protocol + Domain + Port
  ```
  
  Example:
  
  ```
  https://example.com:443
  ```
  
  All of them must match.
  
  * * * * *
  
  Example:
  
  ```
  https://example.com
  ```
  
  and
  
  ```
  https://example.com
  ```
  
  ✅ Same Origin.
  
  * * * * *
  
  However:
  
  ```
  http://example.com
  ```
  
  and
  
  ```
  https://example.com
  ```
  
  ❌ Different.
  
  Because the protocol is different.
  
  * * * * *
  
  
  Or
  
  ```
  https://api.example.com
  ```
  
  and
  
  ```
  https://example.com
  ```
  
  ❌ Different.
  
  Because the host is different.
  
  * * * * *
  
  Or
  
  ```
  https://example.com:8080
  ```
  
  and
  
  ```
  https://example.com
  ```
  
  ❌ Different.
  
  Because the port is different.
  


  </details>



-   SOP does **NOT** stop requests
-   SOP stops **reading responses**




  
</details>




<details>
  <summary>- XSRF-Token</summary>



-   Often used in **Angular**

-   Often paired with Axios

-   Stored in a cookie

-   Used by frontend frameworks automatically

-   Frontend JavaScript reads it and sends it as a header

    ```
    Set-Cookie: XSRF-TOKEN=abc123;

    ```

- <details>
    <summary>Flow & Storage: </summary>
  
  
  -   Flow
  
      1.  Server sets cookie:
  
          ```
          Set-Cookie: XSRF-TOKEN=abc123
  
          ```
  
      2.  Frontend framework automatically reads cookie
  
      3.  Sends it in header:
  
          ```
          X-XSRF-TOKEN: abc123
  
          ```
  
  -   Storage : stored in cookie



  </details>
    



  
</details>


 

<details>
  <summary>- X-CSRF-Token</summary>


-   Sent as a **custom HTTP header**
-   Usually manually handled by backend + frontend
-   Very common in Rails, Django, Laravel APIs

```
X-CSRF-Token: abc123

```


- <details>
    <summary>Flow & Storage : </summary>

  
  1.  Server generates token
  
  2.  Token is embedded in HTML:
  
      ```
      <metaname="csrf-token"content="abc123">
  
      ```
  
  3.  Frontend reads it
  
  4.  Sends it in header:
  
      ```
      X-CSRF-Token: abc123
  
      ```
  
  -   Storage
      -   Often stored in:
      -   Session (server-side)
      -   HTML meta tag
      -   Hidden form input




  </details>




  
</details>













  
</details>

---

<details>
  <summary>CRLF : Carriage Return Line Feed </summary>

- `\n` → new line , if you encode it as URL will get → `%0a`
- `\r` → if you encode it as URL will get → `&0D`
- put them together at any parameter that make changes in the URL to delete the next cookie things like secure , httponly
    
   <img width="734" height="113" alt="image" src="https://github.com/user-attachments/assets/a8f9b4ca-2cbe-4e7e-bb34-802a47c11583" />

    
- but you need to close them so you will add new set-cookie


   <img width="737" height="97" alt="image" src="https://github.com/user-attachments/assets/bf8c0c02-3146-46fc-823d-36e76286d94a" />

  
</details>












---
---
---


```
CSRF Token

+

SameSite=Lax

+

Origin & Referer Check

+

Secure Cookie

+

HttpOnly

+

Password Confirmation

+

MFA
```




























































