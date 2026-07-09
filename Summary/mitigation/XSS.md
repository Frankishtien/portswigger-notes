# XSS mitigation 

---


# 1) HTML Context

## vuln code

in website print user name 

```php
echo "<h1>Welcome " . $_GET['name'] . "</h1>";
```

> if user write :

```url
name=<h1>frank</h1>
```

>> it will run as `html`

## mitigation will be with 🧯 `HTML Encoding/Escaping` 🧯

in this case in php

```php
echo "<h1>Welcome " . htmlspecialchars($_GET['name'], ENT_QUOTES, 'UTF-8') . "</h1>";
```

>> 🔴🔴 If data will be inserted into an HTML context, use the `HTML encoding` or escaping mechanisms specific in the language or framework you are using.


---
---


# 2) Attribute Context

**`not secure`**

```php
echo '<input value="' . $_GET['username'] . '">';
```

## mitigation will be with 🧯 `HTML Encoding/Escaping` 🧯 + **`ENT_QUOTES`** 

> Why did we use ENT_QUOTES?

If you use just:

```
htmlspecialchars($username)
```

The `'` character might not be encoded, depending on the settings.

However:

```
htmlspecialchars($username, ENT_QUOTES, 'UTF-8')
```

will convert:

```
"
```

to:

```
&quot;
```

and convert:

```
'
```

to:

```
&#039;
```

Consequently, the user will not be able to break out of the attribute value.


---
---
---


# 3) JavaScript Context


**`example`**

if user input send to js code 

```html
<script>
   let username = "USER_INPUT";
</script>
```

## mitigation will be with 🧯 `json_encode()` 🧯

Its function:

> Converting a PHP value into a valid JavaScript/JSON representation.

```html
<script>
    let username = <?php echo json_encode($_GET['username']); ?>;
</script>
```


---
---
---

# 4) URL Context


**`example`**

```html
<a href="/profile?name=USER_INPUT">Profile</a>
```

If the user enters spaces or special characters:

The problem:

- Spaces break the link.
- The browser interprets it incorrectly.
- Unexpected behavior may occur.


## mitigation will be with 🧯 `urlencode()` 🧯

It converts anything that would "break a URL" into a safe format:

- space → `+` or `%20`
- `&` → `%26`
- `?` → `%3F`
- `=` → `%3D`


There are two types of URL encoding:

| Function | Usage |
|---|---|
| `urlencode()` | Query string |
| `rawurlencode()` | Path segments (sometimes better) |

```
echo '<a href="/profile?name=' . urlencode($_GET['name']) . '">Profile</a>';
```

---
----
---

# 5) CSS Context

**`example`**

What is CSS Context?

It is when you place user-provided data directly into CSS:

```css
<style>
body {
    background-color: USER_INPUT;
}
</style>
```

## mitigation will be with 🧯 `avoid inline CSS` 🧯


## (A) Best Practice: Avoid inline CSS

Instead of:

```
<div style="color: <?php echo $color ?>">
```

Use:

```
<div class="user-color">
```

And define the color solely via CSS classes.

---

## (B) If you must inject a value

Use a whitelist:

```
if (in_array($color, ['red','blue','green'])) {    echo $color;}
```

---

## (C) Or use escaping + strict control

However, this is less preferred for CSS compared to HTML/JS.

---
---
---

# 6) DOM XSS

**`not secure`**

```js
const name = new URLSearchParams(location.search).get("name");
document.getElementById("user").innerHTML = name;
```


## mitigation will be with 🧯 `textContent` 🧯



## The Problem with `innerHTML`

> `innerHTML` = “Execute any HTML written within the text”

This means that if the value contains HTML, the browser:

- Will not display it as plain text
- Will interpret it as actual HTML

And this is what creates DOM XSS.




**`save code`**

```js
const name = new URLSearchParams(location.search).get("name");
document.getElementById("user").textContent = name;
```

## Why is this safe?

### `textContent` means:

> “Display the text exactly as it is, without interpreting any HTML.”

This means:

- `<b>` → appears as plain text
- It doesn't turn into bold text
- And nothing gets executed




# What if you actually need to render HTML? ⁉️

For instance, consider an article editor that allows elements like:

```
<b>Bold</b><i>Italic</i>
```

You shouldn't use `innerHTML` directly on user input.

> ## Instead, use a reliable HTML sanitization library (sanitizer) like 🧯**DOMPurify**.🧯

Example:

```
const clean = DOMPurify.sanitize(userInput);
element.innerHTML = clean;
```

The idea is that the library removes dangerous elements and attributes, allowing only those defined in a whitelist of safe elements.

---
---
---




# 2) React

```
<h1>{username}</h1>
```

## Why is this safe?

React performs:

- Automatic HTML escaping
- Any `< > & " '` characters are converted into plain text

So, even if the input is:

```
<script>alert(1)</script>
```

It will appear as text, not as executable code.

---

## The Risk:

```
<div dangerouslySetInnerHTML={{__html: userInput}} />
```

### Why is it dangerous?

Because it tells React:

> “Do not escape this… treat it as actual HTML”

Consequently, any HTML within `userInput` could be executed.

---

# 3) EJS (Express)

```
<%= username %>
```

## Safe because:

- It performs automatic escaping.

---

```
<%- username %>
```

## Dangerous because:

- It renders raw HTML without escaping.

---

# 4) Django

```
{{ username }}
```

## Safe because:

- Auto-escaping is enabled by default.

---

```
{{ username|safe }}
```

## Dangerous because:

> "I trust this data."

This completely disables protection.

---

# 5) Laravel Blade

```
{{ $username }}
```

## Safe (escaped)

---

```
{!! $username !!}
```

## Dangerous (raw HTML output)

---

# 6) ASP.NET Razor

```
@Model.Name
```

## Safe because:

- Razor performs automatic encoding

---

```
@Html.Raw(Model.Name)
```

## Dangerous because:

- It bypasses all protection













---
---
---


# CSP (Content Security Policy)


```php
Content-Security-Policy:
  default-src 'self';
  script-src 'self';
  style-src 'self';
  img-src 'self';
  connect-src 'self';
  frame-ancestors 'none';
  object-src 'none';
  base-uri 'none';
```


## What does it protect?

> It protects **the page itself (the browser execution environment)**

It controls:

- Whether JavaScript executes
- Whether scripts can be loaded from another site
- Whether inline scripts are allowed
- Whether `fetch` or `connect` requests to external sources are permitted

---

## Simply put:

> CSP = “What the browser is allowed to run within the page”

---
---
---

# Cookie Flags


```js
// Express
res.cookie('session', token, {
  httpOnly: true,   // JS cannot access document.cookie
  secure: true,     // HTTPS only
  sameSite: 'Strict'
});
```

## What does it protect?

> It protects the **cookie (session / identity)**

---

### `httpOnly`

- Prevents JavaScript from reading the cookie
- Protects against:

    ```
    document.cookie
    ```

---

### `secure`

- The cookie is transmitted only over HTTPS
- Prevents interception

---

### `sameSite`

- Controls whether the cookie is sent with requests from other sites
- Protects against CSRF




---
---
----

### **`Bypass`**


**`https://cheatsheetseries.owasp.org/cheatsheets/XSS_Filter_Evasion_Cheat_Sheet.html`**













