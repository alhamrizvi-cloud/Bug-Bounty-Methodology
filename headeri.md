### Tool: **headi** 

## 📁 Required Files (Minimal)

### `urls.txt`  ✅ (REQUIRED)

### `headers.txt`  ✅ (REQUIRED)
I have provided header.txt on the payloads folder > Header INjection > headers.txt on our repo,
eg.
```
X-Forwarded-For
X-Client-IP
X-Real-IP
X-Originating-IP
Referer
User-Agent
```

### `payloads.txt`  ✅ (OPTIONAL but recommended)

Payloads for header values
I have provided header.txt on the payloads folder > Header INjection > headerspayloads.txt on our repo,
```
127.0.0.1
localhost
evil.com
"><script>alert(1)</script>
```

## Basic Header Injection Scan

```bash
python3 headi.py -u https://example.com
```

## Scan Multiple URLs (urls.txt)

```bash
python3 headi.py -l urls.txt
```

## Inject Custom Headers Only

```bash
python3 headi.py -l urls.txt -H headers.txt
```

## Inject Headers with Payloads (MAIN COMMAND)

```bash
python3 headi.py -l urls.txt -H headers.txt -p payloads.txt
```

## Test Single Header Manually

```bash
python3 headi.py -u https://example.com -h "X-Forwarded-For: 127.0.0.1"
```

## Save Output

```bash
python3 headi.py -l urls.txt -H headers.txt -p payloads.txt -o headi_output.txt
```

## Silent Mode (Clean Output)

```bash
python3 headi.py -l urls.txt -H headers.txt -p payloads.txt --silent
```

## Typical Workflow (Copy–Paste Ready)

```bash
python3 headi.py -l urls.txt -H headers.txt -p payloads.txt -o header_injection_results.txt
```
## Notes (Very Short)

* `urls.txt` → **mandatory**
* `headers.txt` → controls **which headers**
* `payloads.txt` → controls **what gets injected**
* Works for **host injection, cache poisoning, auth bypass, IP trust issues**
