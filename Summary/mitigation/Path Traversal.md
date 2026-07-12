# Path Traversal

---

>[!note]
> #### LFI happens when server code uses attacker-controlled input in file functions (include, require,include_once, require_once , file_get_contents, readfile, fopen, etc.)


<details>
  <summary>Types</summary>


-   LFI : when the server takes name of file and execute his content and show me the result
-   LFD : when the server takes name of file then the attacker will show the file only not executing the file
-   RFI : when the server takes URL and we can call php web shell inside this URL and execute his content
-   Path traversal : when the server takes name of file and show his content without executing the content inside the file

  
</details>

---

<details>
  <summary>How to get </summary>

- Look for any user input that accept URL , File names 

</details>

---

<details>
  <summary>How can i test & Bypass </summary>

-   IF there is parameter that gets something like :
    -   <https://ex.com/home/images/?page=index.php>
    -   then change that page value to :
    -   if it is linux : page=../../../../../../../../../../../../etc/passwd
-   Or if in the code have : <img src="/images?file=123.png" > then click on that path it will open url for you (the image in new tab)
    -   the url of the tab will be something like this : <https://ex.com/image?file=123.png>
    -   if you go 2 or 3 directories for the back and it didn't block you or something so there is LFI you can check it with adding some of : ../../../etc/passwd
        -   if it tells you → (file not found) so there is LFI try to go back more directories
    -   try to get the file without going back : file=etc/passwd
    -   or you can change it to : file=../../../../../../../../../../../../etc/passwd
    -   if it doesn't get the file try to add more : ../../
    -   or file=....//....//....//....//....//....//....//....//....//etc/passwd
        -   because the developer when see ../ will remove it so when he remove ../ from ..**../**/
        -   we will have ../
     
       ```
       $language = str_replace('../', '', $_GET['language']);
       ```

     -   we may use `..././` or `....\/`
     -   If the target web application did not allow `.` and `/` in our input, we can URL encode `../` into `%2e%2e%2f`, which may bypass the filter or encoded it double (one more time)
-   or you can send the request to intruder and add LFI List at the payload and try
-   if the image get a file like this : GET /image?filename=/var/www/images/24.jpg
    -   you can change it like this : GET /image?filename=/var/www/images/../../../../../../../../etc/passwd
-   or you can put a null byte and if it gets image put .png : GET /image?filename=../../../../../../../../../etc/passwd%00.png
    -   or .php or any extension
-   It can be in language parameter
-   and you can start with /../../../../../../../etc/passwd because it can be :
    -   `lang_../../../etc/passwd` if you added the ../ at the first
-   we can end our payload with a null byte (e.g. `/etc/passwd%00`), such that the final path passed to `include()` would be (`/etc/passwd%00.php`). This way, even though `.php` is appended to our string, anything after the null byte would be truncated, and so the path used would actually be `/etc/passwd`, leading us to bypass the appended extension.

-   http://94.237.48.12:37772/index.php?language=languages///....//....//....//.....//....//....//....//....//....//....//....//....//....//....//....//etc/passwd

  
</details>

---
---

<details>
  <summary>how can i mitigate</summary>

## 1. Never use raw user input in file paths with python 

```python
# ❌ Dangerous
filename = request.args.get("file")
open(filename)

# ✅ Safe
filename = request.args.get("file")
safe_name = os.path.basename(filename)          # strip directory components
full_path = os.path.join("/var/app/files", safe_name)
open(full_path)
```


## 2. Whitelist allowed files/directories


```python
ALLOWED_FILES = {"report.pdf", "manual.txt", "guide.html"}

filename = request.args.get("file")
if filename not in ALLOWED_FILES:
    abort(403)
```

## 3. Verify the final path (Canonicalization)

```python
from pathlib import Path

BASE_DIR = Path("/var/app/files").resolve()

requested = (BASE_DIR / filename).resolve()

if not requested.is_relative_to(BASE_DIR):
    abort(403)
```

> The idea is to verify the **final resolved path** (`resolve()`), rather than just the raw text.


## 4. Use IDs instead of filenames


Instead of:

```
/download?file=secret.pdf
```

Use:

```
/download?id=15
```

Then:

```
filename = db.get_filename(file_id)
```

The user does not directly control the filename.



## 5. Store uploaded files outside the web root

```
/var/app/storage/
```

instead of

```
/var/www/html/uploads/
```


## 6. Block null bytes


```
if "\x00" in filename:
    abort(400)
```

Although modern Python handles null bytes better, validating input remains a good idea if it comes from untrusted sources or is passed to other libraries.


## 7. Never concatenate paths manually


❌

```
path = "/files/" + filename
```

✅

```
path = os.path.join(BASE_DIR, filename)
```


## 8. Restrict file permissions

Even if a vulnerability occurs, the application operates with the minimum possible privileges (Principle of Least Privilege).

## 9. Log suspicious attempts


```
logger.warning("Possible path traversal attempt: %s", filename)
```

But **it does not show the user** error details or internal paths.





  
</details>



---

```
                 User Input
                      │
                      ▼
          Is filename provided?
                      │
                      ▼
          Reject NULL bytes (\x00)?
                      │
                      ▼
      Validate filename format (Regex)
      [A-Za-z0-9_.-]+
                      │
                      ▼
       Is filename in Allowlist?
                      │
          ┌───────────┴───────────┐
          │                       │
         No                      Yes
          │                       │
          ▼                       ▼
     Return 403          basename(filename)
                                  │
                                  ▼
                    Join with Base Directory
             os.path.join(BASE_DIR, filename)
                                  │
                                  ▼
                   Resolve Canonical Path
                     Path.resolve()
                                  │
                                  ▼
         Is resolved path inside BASE_DIR?
                                  │
                  ┌───────────────┴───────────────┐
                  │                               │
                 No                              Yes
                  │                               │
                  ▼                               ▼
            Return 403                    Open File Safely
```





























































