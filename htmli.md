This can be done by using XSS Automation tools,

### Dalfox HTML Injection Scan

```bash
dalfox file parameter.txt
```
### Dalfox HTML Injection With Payload File

```bash
dalfox file parameter.txt -w payloadfile.txt
```
### Dalfox Pipe Mode (Fast)

```bash
cat parameter.txt | dalfox pipe -w payloadfile.txt
```
### Dalfox Discovery Only

```bash
dalfox file parameter.txt --only-discovery
```
### httpx HTML Injection Detection

```bash
sed 's/=.*/=<h1>test<\/h1>/' parameter.txt > html_test.txt
httpx -l html_test.txt -match-string "<h1>test</h1>" -silent
```
### Nuclei HTML Injection Scan

```bash
nuclei -l parameter.txt -t html-injection.yaml
```
### XSStrike Fuzz Mode

```bash
xsstrike -u "https://target.com/page.php?id=FUZZ" --payloads payloadfile.txt
```
### Manual Curl Validation

```bash
curl -s "https://target.com/page.php?id=<h1>test</h1>"
```

