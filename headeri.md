### Tool: **headi** 

## 📁 Required Files

### `subdomains.txt`  ✅ 

### ✅ **1️⃣ Add trailing slash (headi-safe URLs)**

```bash
sed 's|$|/|' subd.txt > dailymotion_slash.txt
```

---

### ✅ **2️⃣ (Optional but recommended) remove empty / bad lines**

```bash
grep -E '^https?://' dailymotion_slash.txt > dailymotion_headi.txt
```

---

### ✅ **3️⃣ Run headi in parallel**

```bash
cat dailymotion_headi.txt | xargs -P10 -I{} headi -u {}
```

---

## 🧠 One‑liner version (if you want it compact)

```bash
sed 's|$|/|' dailymotion_clean.txt | grep -E '^https?://' | xargs -P10 -I{} headi -u {} -o headi.txt
```
Perfect question 👍
When **`headi` shows a few `[+]` lines**, **THIS is exactly what you do** — no guessing.

---

## what `[+]` REALLY means in `headi`

`[+]` = **response difference detected**

It does **NOT automatically mean a vulnerability**.
It means: *“This request behaved differently — investigate.”*

## ✅ STEP‑BY‑STEP: What to do when you see `[+]`

### 🔹 1️⃣ Isolate ONLY the positives

```bash
grep '^\[\+\]' headi_output.txt > headi_positives.txt
```

This is your **shortlist**.

### 🔹 2️⃣ Inspect ONE line carefully

Example:

```
[+] [https://api.stage-17.dailymotion.com/] [X-Rewrite-URL: /admin] [Code: 200] [Size: 91234]
```

You care about:

* which **header**
* which **payload**
* what **changed** (code / size)

### 🔹 3️⃣ Reproduce MANUALLY (MANDATORY)

Never trust automated output alone.

```bash
# Baseline
curl -i https://api.stage-17.dailymotion.com/

# With injected header
curl -i https://api.stage-17.dailymotion.com/ \
  -H "X-Rewrite-URL: /admin"
```

If **difference disappears** → false positive
If **difference persists** → continue


### 🔹 4️⃣ Check IMPACT (this decides reportability)

Ask:

* Did status change? (401 → 200)
* Did content change?
* Did redirect change?
* Did internal page appear?
* Did auth get bypassed?

If answer = **NO** → discard
If answer = **YES** → possible vuln

### 🔹 5️⃣ Confirm exploitability

Try:

```bash
-H "Host: evil.com"
-H "X-Forwarded-Host: evil.com"
-H "X-Rewrite-URL: /internal"
```

And compare results.

## 🚨 When NOT to report

Do **NOT** report if:

* only size changes slightly
* headers echoed but unused
* static content difference
* CDN behavior only

Bug bounty teams will reject it.

## 🏆 When TO report

Report **ONLY if you can show**:

* auth bypass
* access to internal endpoint
* password reset link poisoning
* cache poisoning
* internal routing abuse



## 🧪 Quick decision rule (save time)

| Result                  | Action      |
| ----------------------- | ----------- |
| `[+]` but same content  | Ignore      |
| `[+]` + status change   | Investigate |
| `[+]` + new data        | High value  |
| `[+]` + redirect change | Reportable  |

