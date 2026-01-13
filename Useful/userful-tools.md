### **1️⃣ anew**

**Tool:** anew
**What it does:**
Adds **new lines to a file**, skipping duplicates (great for recon pipelines).

**Commands:**

```bash
cat new.txt | anew all.txt
subfinder -d target.com | anew subs.txt
```

---

### **2️⃣ gf**

**Tool:** gf
**What it does:**
Wrapper around `grep` to **filter URLs/parameters** using predefined patterns (xss, sqli, redirect, etc.).

**Commands:**

```bash
cat urls.txt | gf xss
cat urls.txt | gf redirect
cat urls.txt | gf sqli
```

---

### **3️⃣ uro**

**Tool:** uro
**What it does:**
**Declutters URLs** by removing duplicates, tracking params, and useless variations.

**Commands:**

```bash
cat urls.txt | uro > cleanurls.txt
gau target.com | uro
```

---

### **4️⃣ unfurl**

**Tool:** unfurl
**What it does:**
**Extracts parts of URLs** (params, keys, values, paths, domains).

**Commands:**

```bash
cat urls.txt | unfurl keys
cat urls.txt | unfurl values
cat urls.txt | unfurl paths
cat urls.txt | unfurl domains
```

---

### **5️⃣ qsreplace**

**Tool:** qsreplace
**What it does:**
Replaces **all query string values** with a supplied value (FUZZ, payloads, etc.).

**Commands:**

```bash
cat urls.txt | qsreplace FUZZ
cat urls.txt | qsreplace '"><script>alert(1)</script>'
```

---

### **Quick Summary**

| Tool      | Purpose                  |
| --------- | ------------------------ |
| anew      | Add only new results     |
| gf        | Filter URLs by vuln type |
| uro       | Clean & normalize URLs   |
| unfurl    | Extract URL components   |
| qsreplace | Replace parameter values |

