## ssrf mitigation

<details>
  <summary>types</summary>

- `basic`
   - display the response to attacker

- `blind`
   - doesn’t display the response 


</details>

---

<details>
  <summary>How to get </summary>


-   if there is any parameter that accept URL you must try SSRF
-   if there is any link try to change it to → <http://localhost/admin>

  
</details>


---

<details>
  <summary>How can i test & bypasses</summary>



-   SSRF to XSPS

    -   (XSPS : cross site port scanning ) scan another website using another website like <https://example.com:22> and check the response
-   SSRF to LFI → change the parameter url to url=file:///etc/passwd

-   localhost == 127.0.0.1 == 127.0.1 == 127.1 == 127.01 == 010.010.010.010 == 0177.1 == 0x7f.1 == ::1 (IPv6) == **2130706433 (decimal)**

-   you can make a domain that host 127.0.0.1 and when you give the website the domain name it will bypass if 127.0.0.1 blacklist

    <img width="635" height="251" alt="image" src="https://github.com/user-attachments/assets/da34814e-8b36-4216-b3d9-41255717cd95" />


-   ip to hex decimal

-   any OS have Inet_aton() : address translation : take IP with strange type it can translate it to the real IP like 127.1 or ::1 all of this == 127.0.0.1

-   try corp domains like if you want to access yahoo try to change the URL to : <http://corp.yahoo.com>



  
</details>

---

<details>
  <summary>SSRF to RCE </summary>


-   Cloud providers users Metadata files to store sensitive information of the service user, such as **SSH keys**. These data is only accessible on the local network of the running instance.
    -   AWS > <http://169.254.169.254/latest/meta-data/>
    -   Google > <http://metadata.google.internal/computeMetadata/v1beta1/project/attributes/ssh-keys?alt=json>
    -   Digital Ocean > <http://169.254.169.254/metadata/v1.json>
-   if there is url that get data from server with GET change the value to AWS metadata url=http://169.254.169.254/latest/meta-data

  
</details>


---

# **`Mitigation`**

<details>
  <summary>whitelist</summary>



1. Use an allowlist instead of a blocklist
----------------------------------------

The best solution.

Instead of allowing any site:

Allow only specific sites.

Example:

```
allowed = [
    "images.company.com",
    "cdn.company.com"
]
```

If the site is not in the list:

```
return 403
```

* * * * *


2. Parse the URL
----------------------

Instead of:

```
requests.get(user_input)
```

Do this:

```
from urllib.parse import urlparse

url = urlparse(user_input)
```

Then check the:

-   Scheme
-   Host
-   Port

* * * * *


3. Allow only HTTP and HTTPS
----------------------------

For example:

```
if url.scheme not in ["http", "https"]:
    reject()
```

Block protocols that are unnecessary for the application.

* * * * *

4. Block internal IP addresses
---------------------------

Before executing the request, verify that the destination is not within internal or local networks if the application does not require access to them.

For example, do not allow access to:

-   `127.0.0.1`
-   `localhost`
-   Private network addresses (such as RFC1918 ranges)
-   Link-local addresses (unless required)

* * * * *

5. Perform DNS resolution, then verify
-------------------------------------

Do not rely solely on the domain name.

Obtain the final IP address.

Then, verify that it is not an internal IP.

* * * * *

6. Re-verify after DNS resolution
---------------------------------

Some attacks exploit changes in DNS resolution results.

Therefore:

```
Resolve DNS

↓

Get IP

↓

Check IP

↓

Connect
```

It is best to base both the verification and the connection on the same result to minimize the risk of bypassing security measures.

* * * * *


### SSRF Redirect

In an SSRF scenario, it works like this:

The user enters a URL:

```
https://example.com/image.jpg
```

The server executes:

```
requests.get(url)
```

However, `example.com` responds with:

```
302 Location: http://internal-service.local/
```

If your HTTP library **automatically follows redirects**, the **server itself** will proceed to request `internal-service.local`.

Thus, the underlying vulnerability remains **SSRF**, but the redirect serves as a method to bypass filters if you only validate the initial URL without checking the new destination.

### Protection

Instead of simply blocking redirects entirely, the best approach is:

-   If the application **does not need** to follow redirects → disable them.
-   If the application **does need** them → validate **every redirect destination** using the same security rules (allowlist, IP checks, protocol validation, etc.) before proceeding with the request.







---




-   **Vulnerable**

    ```python
    import requests
    from flask import Flask, request, jsonify

    app = Flask(__name__)

    @app.route("/fetch")
    def fetch_url():
        url = request.args.get("url")
        # Attacker sends: <http://169.254.169.254/latest/meta-data/iam/credentials>
        response = requests.get(url)
        return jsonify({"content": response.text})

    ```

-   **Safe --- Allowlist Domains**

    ```python
    import requests
    from urllib.parse import urlparse
    from flask import Flask, request, jsonify

    app = Flask(__name__)

    ALLOWED_DOMAINS = {
        "api.github.com",
        "api.stripe.com",
        "api.sendgrid.com"
    }

    def is_allowed_url(url: str) -> bool:
        try:
            parsed = urlparse(url)

            # Only allow https
            if parsed.scheme != "https":
                return False

            # Strip port and check against allowlist
            hostname = parsed.hostname or ""
            if hostname not in ALLOWED_DOMAINS:
                return False

            return True
        except Exception:
            return False

    @app.route("/fetch")
    def fetch_url():
        url = request.args.get("url", "")

        if not is_allowed_url(url):
            return jsonify({"error": "URL not allowed."}), 400
    
        response = requests.get(url, timeout=5)
        return jsonify({"content": response.text})

    ```







  
</details>


>[!note]
> #### **Important Note:** Some guidelines may vary depending on the nature of the application. For instance, if you have an internal service specifically designed to communicate with services within the network, blocking all internal addresses would not be appropriate; however, it remains best practice to restrict access to an allowlist of known services.



```
                    User URL
                       │
                       ▼
             Parse & Normalize URL
                       │
                       ▼
          ┌────────────────────────┐
          │ Validate URL Structure │
          └────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
   Check Scheme    Check Port    Check Length
   HTTP/HTTPS      80/443 only   Valid URL
        │              │              │
        └──────────────┼──────────────┘
                       ▼
              Check Allowlist
           (Trusted Domains Only)
                       │
                       ▼
                DNS Resolution
                       │
                       ▼
                 Get Final IP
                       │
                       ▼
       Is IP Private / Local / Internal ?
                 │               │
               YES              NO
                │                │
             Reject              ▼
                         Follow Redirect?
                               │
                     ┌─────────┴─────────┐
                     │                   │
                    NO                  YES
                     │                   │
                     ▼                   ▼
              Send Request      Validate Redirect
                                   │
                                   ▼
                           Repeat Validation
                                   │
                                   ▼
                             Send Request
                                   │
                                   ▼
                     Timeout + Network ACL
                                   │
                                   ▼
                             Return Response
```

































