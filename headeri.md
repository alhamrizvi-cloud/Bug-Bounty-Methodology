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
sed 's|$|/|' dailymotion_clean.txt | grep -E '^https?://' | xargs -P10 -I{} headi -u {}
```

