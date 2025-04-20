# lab 1

 - > /robots.txt
   - > /administrator-panel

---

# lab 2

 - > ``view page sourse``
   -> found ``/admin-2g9weg``


---

# lab 3

 - > /admin with intercept the request change admin=false ==> true
   - > delete carlos

---

# lab 4

 - > login using ``wiener`` :  ``peter`` and try to change email
   > see the response you will found that "roleid":1
   - > intercept the request of change the email and add "roleid":2
     > ``{"email":"admin@example.com","roleid":2}``

Don't forget ``,`` in json

---

# lab 5
   
 - >  login using ``wiener`` :  ``peter`` found in url
   - > /my-account?id=wiener   ==> change it to ``carlos``



---

# lab 6

 - > login using ``wiener`` :  ``peter`` found in url
   - > my-account?id=72d06022-c4ac-4535-b9c8-54a7cb6c444b
     > navigate posts you will find post that ``carlos`` write
     > and found
     - > blogs?userId=e7a9dfef-ee9a-46ba-bba9-b317a18e67a8
       > my-account?id=e7a9dfef-ee9a-46ba-bba9-b317a18e67a8



---

# lab 7

 - > login using ``wiener`` :  ``peter``
 - >  reload the page with intercept the request
 - > change id=wiener ==> carlos
   > if you do it in website it forward you to login page
   > but in burp suite you will notice that he forwared you to carlos page before go to login page


---

# lab 8

 - > login using ``wiener`` :  ``peter``
   > found in url my-account?id=wiener
   > change it to administrator
   > and passowrd in the page put it disappear like *******
   > reload page with intercept the request you will see the password


---

# lab 9

 - > you find live chat open it
   > click ``view transcript``
   > you will notice that 5.txt downloaded
   > click button angain but intrecpt the request change 5.txt ==> 1.txt


---

# lab 10

 - > try to open /admin but not allowed
   > use X-Original-URL: /admin
   > it will open admin panel click on clear carlos it's forbbeden but intercept ther reuest
   > GET /?username=carlos
   > ....
   > X-Original-URL: /admin/delete


---

# lab 11 

 - > login with admin account and sent upgrade request to carlos and sent it to repeater
   > no login to wiener and reload page with intercept take your cookie put it instead of cookie if admin in repeater
   > change request method to get and sent it and booom !!!



---

# lab 12 

 - > like lab before it but it will ask you if you are sure you want to delete this user click yes with intercpt the reuqest and other steps like the lab before


--- 

# lab 13 


 - > same but in request header not body
   > GET /admin-roles?username=wiener&action=upgrade HTTP/2










   


















