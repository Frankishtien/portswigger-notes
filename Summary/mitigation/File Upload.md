# File upload Mitigation

---


<details>
  <summary>How to Get</summary>

1.  Upload file
2.  execute or trigger the file
  
</details>

---


<details>
  <summary>How can i test & bypass</summary>


[File upload pentest](https://github.com/Frankishtien/portswigger-notes/blob/main/File%20upload%20vulnerabilities.md)

  
</details>


---

<details>
  <summary>how can i mitigate</summary>


-   ALLOWED_EXTENSIONS = {"jpg", "jpeg", "png", "gif", "webp"}
-   mitigation of

```python

import os
from urllib.parse import unquote

UPLOAD_DIR = "/var/app/uploads"

def safe_filename(filename):
    # 1. URL decode (handles %2f → /)
    filename = unquote(filename)

    # 2. Strip null bytes
    filename = filename.replace("\x00", "")

    # 3. Strip ALL directory components
    filename = os.path.basename(filename)

    # 4. Remove any remaining dots/slashes
    filename = filename.replace("..", "").replace("/", "").replace("\\", "")

    # 5. Rename entirely — never trust user filename
    import uuid
    ext = filename.rsplit(".", 1)[-1].lower()
    safe_name = f"{uuid.uuid4().hex}.{ext}"

    # 6. Verify final path stays inside upload dir
    final_path = os.path.realpath(os.path.join(UPLOAD_DIR, safe_name))
    if not final_path.startswith(UPLOAD_DIR):
        raise ValueError("Path traversal detected")

    return final_path

```


- mitigate for exif tool inject

```python
# ✅ 1. Strip ALL metadata from uploaded files
# Use PIL/Pillow to re-encode — destroys embedded payloads

from PIL import Image
import io

def strip_metadata(file_bytes):
    img = Image.open(io.BytesIO(file_bytes))

    # Re-save clean — strips ALL EXIF/metadata
    output = io.BytesIO()
    img.save(output, format=img.format)   # clean copy, no metadata
    return output.getvalue()
```


---

-  Verify Real MIME Type

```python
import magic

ALLOWED_MIME = {
    "image/jpeg",
    "image/png",
    "image/gif",
    "image/webp"
}

mime = magic.from_buffer(file.read(2048), mime=True)
file.seek(0)

if mime not in ALLOWED_MIME:
    raise ValueError("Invalid file type")
```

---

- Verify Magic Bytes


```python
from PIL import Image

def validate_image(file):
    try:
        img = Image.open(file)
        img.verify()      # Verify actual image structure
        file.seek(0)
    except Exception:
        raise ValueError("Corrupted or fake image")
```


---

- Don't Trust Client Content-Type


```python
# ❌ Never trust:
request.files["file"].content_type

# ✔ Always detect the type yourself
mime = magic.from_buffer(file.read(2048), mime=True)
```


---

- Sanitize SVG


If the application **does not require SVG**:

```
ALLOWED_EXTENSIONS = {
    "jpg",
    "jpeg",
    "png",
    "gif",
    "webp"
}
```

If it does require it, perform sanitization using a dedicated library before storing or displaying it.


---

- Prevent ZIP Bombs

```python
import zipfile

MAX_FILES = 100
MAX_UNCOMPRESSED_SIZE = 100 * 1024 * 1024  # 100MB

with zipfile.ZipFile(file) as z:
    if len(z.infolist()) > MAX_FILES:
        raise ValueError("Too many files")

    total = sum(i.file_size for i in z.infolist())

    if total > MAX_UNCOMPRESSED_SIZE:
        raise ValueError("Archive too large")
```

---

- loging

```python

import logging

logging.info(
    "User=%s uploaded %s (%d bytes)",
    current_user.id,
    filename,
    size
)

```




  
</details>











---


```

✔ Extension Allowlist
✔ MIME Validation
✔ Magic Bytes Validation
✔ Ignore Client Content-Type
✔ Remove Null Bytes
✔ Prevent Path Traversal
✔ UUID Filename
✔ Store Outside Web Root
✔ Disable Script Execution (Server Config)
✔ File Size Limit
✔ Upload Count Limit
✔ Strip EXIF Metadata
✔ Validate Image Integrity
✔ Malware Scan (Optional)
✔ Sanitize SVG
✔ Protect Against ZIP Bombs
✔ Logging

```
















