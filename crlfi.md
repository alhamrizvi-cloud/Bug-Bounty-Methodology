# Carriage Return line feed (CRLFi)
Carriage Return (CR) and Line Feed (LF). It is a special, invisible sequence of two characters used in computer text to mark the end of a line and start a new one. 
## **CRLF Injection – Tools, Commands & Requirements**


### **1️⃣ CRLFsuite**

**Tool:** CRLFsuite
**Requirements:**

* ✅ **URL file with parameters**

**Command:**

```bash
python3 crlfsuite.py -u urls.txt
```


### **2️⃣ crlfuzz**

**Tool:** crlfuzz (Go-based)
**Requirements:**

* ✅ **URL file with parameters**

**Command:**

```bash
crlfuzz -l urls.txt
```


### **3️⃣ CRLF-Injection-Scanner**

**Tool:** CRLF-Injection-Scanner
**Requirements:**

* ✅ **URL file with parameters**

**Command:**

```bash
python3 crlf.py -f urls.txt
```

### **4️⃣ Injectus**

**Tool:** Injectus
**Requirements:**

* ✅ **URL file with parameters**
* ✅ **OR parameter file**

**Command (URL file):**

```bash
python3 injectus.py -f urls.txt -t crlf
```

**Command (Parameter file):**

```bash
python3 injectus.py -f params.txt -t crlf
```

### **Summary**

| Tool                   | URL File | Parameter File |
| ---------------------- | -------- | -------------- |
| CRLFsuite              | ✅        | ❌              |
| crlfuzz                | ✅        | ❌              |
| CRLF-Injection-Scanner | ✅        | ❌              |
| Injectus               | ✅        | ✅              |


