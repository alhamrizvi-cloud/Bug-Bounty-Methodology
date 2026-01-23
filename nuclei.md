
# 🧨 NUCLEI – COMPLETE CHEATSHEET

## 🔰 1. BASIC USAGE

### Scan a single target

```bash
nuclei -u https://example.com
```

### Scan multiple targets

```bash
nuclei -l targets.txt
```

### Resume interrupted scan

```bash
nuclei -resume resume.cfg
```

---

## 🎯 2. TARGET HANDLING

### Scan all IPs of a domain

```bash
nuclei -u example.com -sa
```

### Force IPv6 / IPv4

```bash
nuclei -u example.com -iv 6
```

### Exclude hosts

```bash
nuclei -l hosts.txt -eh 127.0.0.1,internal.local
```

---

## 📂 3. INPUT FORMATS (ADVANCED)

### Burp / OpenAPI / Swagger

```bash
nuclei -l burp.xml -im burp
nuclei -l openapi.yaml -im openapi
```

### Skip format validation

```bash
nuclei -l input.yaml -sfv
```

---

## 🧬 4. TEMPLATE CONTROL

### Run specific templates

```bash
nuclei -u example.com -t cves/
```

### Multiple template paths

```bash
nuclei -u example.com -t http/ -t ssl/
```

### Remote templates

```bash
nuclei -turl https://example.com/template.yaml
```

### Only NEW templates

```bash
nuclei -u example.com -nt
```

### Templates from specific version

```bash
nuclei -ntv v9.7.2
```

---

## 🧠 5. AUTOMATIC & AI MODE

### Auto scan (tech → vuln mapping)

```bash
nuclei -u example.com -as
```

### AI-generated template (🔥)

```bash
nuclei -u example.com -ai "detect exposed admin panels"
```

---

## 🏷️ 6. FILTERING (MOST IMPORTANT)

### By severity

```bash
nuclei -u example.com -s high,critical
```

### Exclude low/info

```bash
nuclei -u example.com -es info,low
```

### By tags

```bash
nuclei -u example.com -tags xss,sqli
```

### Exclude noisy tags

```bash
nuclei -u example.com -etags dos,fuzz
```

### By template ID

```bash
nuclei -id CVE-2023-*
```

---

## 📤 7. OUTPUT & REPORTING

### JSON / JSONL

```bash
nuclei -u example.com -json-export out.json
nuclei -u example.com -jle out.jsonl
```

### Markdown report

```bash
nuclei -u example.com -me reports/
```

### SARIF (for GitHub Security)

```bash
nuclei -u example.com -se report.sarif
```

### Silent mode (clean output)

```bash
nuclei -u example.com -silent
```

---

## 🧾 8. STORE REQUESTS / RESPONSES

```bash
nuclei -u example.com -sresp
```

Custom directory:

```bash
nuclei -u example.com -srd nuclei-traffic/
```

---

## ⚙️ 9. CONFIG & HEADERS

### Custom headers (Auth, Cookies)

```bash
nuclei -u example.com -H "Authorization: Bearer TOKEN"
```

### Variables

```bash
nuclei -u example.com -V user=admin -V pass=admin
```

### Follow redirects

```bash
nuclei -fr
```

---

## 🌐 10. INTERACTSH (OAST)

### Disable OAST

```bash
nuclei -ni
```

### Self-hosted Interactsh

```bash
nuclei -iserver https://oast.example.com -itoken TOKEN
```

---

## 🧪 11. FUZZING / DAST MODE

### Enable DAST fuzzing

```bash
nuclei -u example.com -dast
```

### Aggressive fuzzing

```bash
nuclei -dast -fa high
```

### Show fuzz points

```bash
nuclei -dfp
```

---

## 🔍 12. UNCOVER (ASSET DISCOVERY)

### Shodan → feed into nuclei

```bash
nuclei -uc -uq "ssl:\"example.com\"" -ue shodan
```

### Limit results

```bash
nuclei -ul 50
```

---

## 🚦 13. RATE LIMIT & SPEED CONTROL

### Requests per second

```bash
nuclei -rl 50
```

### Concurrency control

```bash
nuclei -c 10 -bs 10
```

### Headless tuning

```bash
nuclei -headc 5 -hbs 5
```

---

## 🚀 14. OPTIMIZATION FLAGS (PRO MODE)

### Stop after first match

```bash
nuclei -spm
```

### Project mode (dedup requests)

```bash
nuclei -project -project-path ./nuclei-project
```

### Stream input (huge lists)

```bash
nuclei -stream
```

---

## 🧠 15. HEADLESS SCANNING

```bash
nuclei -u example.com -headless
```

Show browser:

```bash
nuclei -sb
```

List actions:

```bash
nuclei -lha
```

---

## 🐞 16. DEBUGGING & DEV

### Full request/response

```bash
nuclei -debug
```

### Only requests

```bash
nuclei -dreq
```

### List DSL functions

```bash
nuclei -ldf
```

### Show loaded templates

```bash
nuclei -vv
```

---

## 🔄 17. UPDATE & MAINTENANCE

```bash
nuclei -up
nuclei -ut
```

Reset everything:

```bash
nuclei -reset
```

---

## ☁️ 18. CLOUD (PDCP)

```bash
nuclei -pd
nuclei -dashboard-upload results.jsonl
```

---

## 🧪 19. AUTHENTICATED SCANS

```bash
nuclei -sf secrets.yaml
nuclei -ps
```

---

## 🧠 20. REAL-WORLD BUG BOUNTY COMMANDS

### High-signal scan

```bash
nuclei -l alive.txt -s high,critical -silent
```

### CVE-only scan

```bash
nuclei -l hosts.txt -tags cve
```

### XSS focused

```bash
nuclei -l urls.txt -tags xss -dast
```

### Recon → exploit chain

```bash
httpx -l subs.txt | nuclei -as
```

---

