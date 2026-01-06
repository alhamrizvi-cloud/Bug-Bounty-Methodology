

## Index

* [1. Passive Subdomain Enumeration](#1-passive-subdomain-enumeration)
* [2. Active Subdomain Discovery](#2-active-subdomain-discovery)
* [3. Probing & Deep Recon](#3-probing--deep-recon)
* [4. JavaScript Analysis](#4-javascript-analysis)
* [5. Google & GitHub Dorking](#5-google--github-dorking)
* [6. URL Discovery & Crawling](#6-url-discovery--crawling)
* [7. Archive & Parameter Discovery](#7-archive--parameter-discovery)
* [8. Cloud Asset Enumeration](#8-cloud-asset-enumeration)
* [9. API Enumeration](#9-api-enumeration)
* [10. Subdomain Takeover](#10-subdomain-takeover)
* [11. XSS Automation](#11-xss-automation)
* [12. My XSS Workflow](#12-my-xss-workflow)
* [13. IDOR Parameter Fetch](#13-idor-parameter-fetch)
* [14. CMD Injection](#14-cmd-injection)
  
## Other Stuff

- **[Open Redirect](open_redirect_testing.md)**
- **[Information Disclosure](information-disclosure.md)**
- **[Host Header Injection](host-header-injection.md)**
- **[Local File Inclusion](lfi.md)**
- **[XSS Methdology](xss.md)**

## 1. Passive Subdomain Enumeration

### Subfinder

```bash
subfinder -d target.com -all -recursive -silent -o subfinder_subs.txt
```

### Amass (Passive)

```bash
amass enum -passive -d target.com -o amass_passive_subs.txt
```

### Assetfinder

```bash
assetfinder --subs-only target.com > assetfinder_subs.txt
```

### Findomain

```bash
findomain -t target.com -q > findomain_subs.txt
```

### Chaos

```bash
chaos -d target.com -silent > chaos_subs.txt
```

### CRT.sh

```bash
curl -s "https://crt.sh/?q=%25.target.com&output=json" | jq -r '.[].name_value' | sed 's/\\*\\.//g' | anew crtsh_subs.txt
```

### GitHub Subdomains

```bash
github-subdomains -d target.com -t YOUR_GITHUB_TOKEN -o github_subs.txt
```

### Merge Passive Results

```bash
cat *_subs.txt | sort -u | anew all_subs.txt
```

---

## 2. Active Subdomain Discovery

### Amass (Active)

```bash
amass enum -active -d target.com -o amass_active_subs.txt
```

### DNSX Resolve

```bash
cat bmw7.txt | dnsx -silent > bmw8.txt
```

### Gobuster DNS

```bash
gobuster dns -d target.com -w dns_wordlist.txt -o gobuster_subs.txt
```

### ShuffleDNS

```bash
shuffledns -d target.com -w dns_wordlist.txt -r resolvers.txt -silent > shuffledns_subs.txt
```

### Merge Active Results

```bash
cat *_active_subs.txt | sort -u > active_final.txt
```

---

## 3. Probing & Deep Recon

### HTTPX

```bash
httpx -l resolved_subs.txt -p 80,443,8080,8443 -silent -title -sc -ip -o live_websites.txt
```

### Httprobe

```bash
cat resolved_subs.txt | httprobe > live_websites.txt
```

### Endpoint Filtering

```bash
cat live_websites.txt | grep -Ei "login|admin|signin|dashboard" | tee login_endpoints.txt
```

---

## 4. JavaScript Analysis

### JavaScript Collection

```bash
cat live_websites.txt | gau --subs | grep -i "\\.js$" | sort -u > js_passive.txt
katana -list live_websites.txt -jc -silent | grep -i "\\.js$" | sort -u > js_active.txt
sort -u js_active.txt > katanajs_clean.txt
cat js_passive.txt katanajs_clean.txt | sort -u > js_all.txt
```

### Mantra

```bash
cat js_all.txt | mantra
```

### LinkFinder

```bash
python3 linkfinder.py -i js_files.txt -o js_endpoints.txt
```

### JSFinder

```bash
python3 JSFinder.py -u https://target.com -o jsfinder_results.txt
```

### SecretFinder

```bash
python3 SecretFinder.py -i js_files.txt -o secrets.txt
```

### GF Patterns

```bash
cat js_files.txt | gf aws-keys | tee aws_keys.txt
cat js_files.txt | gf api-keys | tee api_keys.txt
cat js_files.txt | gf urls | tee sensitive_urls.txt
```

---

## 5. Google & GitHub Dorking

### GitDorker

```bash
python3 GitDorker.py -tf github_token.txt -q target.com -d dorks.txt -o git_dorks_output.txt
```

### GitHound

```bash
githound --config config.yml --subdomain-file live_websites.txt
```

---

## 6. URL Discovery & Crawling

### Katana

```bash
katana -list live_websites.txt -jc -o katana_urls.txt
```

### Gospider

```bash
gospider -S live_websites.txt -d 2 -o gospider_output/
```

### Hakrawler

```bash
cat live_websites.txt | hakrawler -depth 3 -plain -out hakrawler_results.txt
```

### Feroxbuster

```bash
feroxbuster -u https://target.com -d 2 -q -o ferox_urls.txt
```

---

## 7. Archive & Parameter Discovery

### GAU & Wayback

```bash
gau --subs target.com | anew archived_urls.txt
waybackurls target.com | anew wayback_urls.txt
```

### Merge Archives

```bash
cat archived_urls.txt wayback_urls.txt | sort -u > all_archived_urls.txt
```

### Parameter Extraction

```bash
cat all_archived_urls.txt | grep "=" | anew parameters.txt
```

### ParamSpider

```bash
python3 paramspider.py --domain target.com --exclude woff,css,png,jpg --output paramspider.txt
```

### Arjun

```bash
arjun -u https://target.com -oT arjun_params.txt
```

### FFUF

```bash
ffuf -u "https://target.com/page.php?FUZZ=test" -w /usr/share/wordlists/params.txt -o parameter_results.txt
```

---

## 8. Cloud Asset Enumeration

```bash
cloud_enum -k target.com -b buckets.txt -o cloud_enum_results.txt
aws s3 ls s3://bucket-name --no-sign-request
python3 AWSBucketDump.py -b target-bucket -o dumped_data/
s3scanner scan --bucket target-bucket
```

---

## 9. API Enumeration

```bash
kr scan https://api.target.com -w /usr/share/kiterunner/routes-large.kite -o api_routes.txt
```

---

## 10. Subdomain Takeover

```bash
subzy run --targets bmw_resolved.txt --hide-fails
subzy run --targets bmw_resolved.txt --verify_ssl --concurrency 20
subzy run --targets bmw_resolved.txt | tee subzy_results.txt
```

---

## 11. XSS Automation

**Requirement:** `parameter.txt`
```bash
dalfox file parameters.txt --custom-payload payloads.txt
python3 xsstrike.py -f parameters.txt --payload payloads.txt
cat parameters.txt | kxss > reflected.txt
cat parameters.txt | gxss -o gxss.txt
cat parameters.txt | kxss | dalfox pipe --custom-payload payloads.txt
```

---

## 12. My XSS Workflow

**Requirement:** `parameter.txt`
```bash
cat parameter.txt | uro > uro.txt
cat uro.txt | kxss > reflected.txt
dalfox file reflected.txt --only-discovery
nuclei -l reflected.txt -t http/vulnerabilities/ -tags xss -severity medium,high,critical
cat reflected.txt | qsreplace '\"><svg/onload=alert(1)>' | httpx -silent
```

---

## 13. IDOR Parameter Fetch

**Requirement:** `parameter.txt`
```bash
grep -Ei "(\\?|&)(VPNR|OutletID|page|session|wer|go2|bmw)=" www.bmw.de.txt > idor_only.txt
grep -Ev "lb(matchtype|creative|network|keyword)|gclid=|tl=" idor_only.txt > idor_clean1.txt
grep -Ei "(VPNR=|OutletID=|session=|wer=)" idor_clean1.txt > idor_clean2.txt
sort -u idor_clean2.txt > idor_final.txt
```

---

## 14. CMD Injection

**Requirement:** `parameter.txt` must exist (combined output from ParamSpider / Arjun / FFUF).

The goal here is to **reduce noise**, scan safely, and test CMD injection in **controlled chunks of 100 URLs**.

```bash
# Step 1: Filter parameters that commonly lead to command injection
# This avoids scanning every parameter blindly
grep -Ei "ip|host|ping|cmd|exec|shell|run|query" parameter.txt > cmdi_all.txt

# Step 2: Remove duplicate URLs to reduce scan time
sort -u cmdi_all.txt > cmdi_unique.txt

# Step 3: Keep only valid parameterized URLs (must contain '=')
grep "=" cmdi_unique.txt > cmdi_final.txt

# Step 4: Split into chunks of 100 URLs per file
# Example output files: cmdi_aa, cmdi_ab, cmdi_ac ...
split -l 100 cmdi_final.txt cmdi_

# Step 5: Safe initial detection scan (fast, low noise)
commix -m /home/alhamr/Downloads/bmw/results/cmdi_aa --batch --level=1

# Step 6: Deeper confirmation scan on the same 100 URLs
commix -m /home/alhamr/Downloads/bmw/results/cmdi_aa --batch --level=2

# Step 7: Blind / time-based CMD injection testing (slow, last option)
commix -m /home/alhamr/Downloads/bmw/results/cmdi_aa --batch --level=3 --technique=t

# Step 8: Move to the next 100 URLs and repeat from level 1
commix -m /home/alhamr/Downloads/bmw/results/cmdi_ab --batch --level=1

# Step 9: Save detailed results for reporting and later review
commix -m /home/alhamr/Downloads/bmw/results/cmdi_aa --batch --level=2 --output-dir=commix_results
```

**Recommended flow:**
`level=1 → level=2 → level=3 (only if needed)`

Always scan chunk‑by‑chunk to avoid crashes, WAF bans, and false positives.
