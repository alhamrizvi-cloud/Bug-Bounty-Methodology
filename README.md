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
curl -s "https://crt.sh/?q=%25.target.com&output=json" \
| jq -r '.[].name_value' \
| sed 's/\*\.//g' \
| anew crtsh_subs.txt
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

### DNSx

```bash
cat bmw7.txt | dnsx -silent > bmw8.txt
```
then combine and  resoleve using dnsx

### Gobuster (Wordlist)

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

## Handling Single / Non-Wildcard Targets

Used when scope allows only specific domains like `target.example.com`.

### GAU

```bash
gau target.example.com | anew gau_results.txt
```

### Waybackurls

```bash
waybackurls target.example.com | anew wayback_results.txt
```

### Katana

```bash
katana -u target.example.com -silent -jc -o katana_results.txt
```

### Hakrawler

```bash
echo "https://target.example.com" | hakrawler -depth 2 -plain -js -out hakrawler_results.txt
```

### Merge URLs

```bash
cat *_results.txt | sort -u > urls_final.txt
```

---

## Additional Recon Techniques

### Reverse DNS

```bash
dnsx -ptr -l resolved_subs.txt -resp-only -o reverse_dns.txt
```

### ASN Enumeration

```bash
amass intel -asn ASN_NUMBER -o asn_results.txt
```

### Cloud Asset Enumeration

```bash
cloud_enum -k target.com
```

### Live Asset Validation

```bash
cat all_subs.txt | httpx -silent -title -o live_subdomains.txt
```

---

## 3. Probing & Deep Recon

### HTTPX

```bash
httpx -l resolved_subs.txt -p 80,443,8080,8443 -silent -title -sc -ip -o live_websites.txt
```

### HTTProbe

```bash
cat resolved_subs.txt | httprobe > live_websites.txt
```

### Endpoint Filtering

```bash
cat live_websites.txt | grep -Ei "login|admin|signin|dashboard" | tee login_endpoints.txt
```

---

## 4. JavaScript Analysis

### JS Collection

```bash
cat live_websites.txt | waybackurls | grep "\.js" | anew js_files.txt
cat live_websites.txt | subjs | anew js_files.txt
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

### FFuF

```bash
ffuf -u "https://target.com/page.php?FUZZ=test" -w /usr/share/wordlists/params.txt -o parameter_results.txt
```

---

## Cloud Asset Enumeration

```bash
cloud_enum -k target.com -b buckets.txt -o cloud_enum_results.txt
aws s3 ls s3://bucket-name --no-sign-request
python3 AWSBucketDump.py -b target-bucket -o dumped_data/
s3scanner scan --bucket target-bucket
```

---

## API Enumeration

```bash
kr scan https://api.target.com -w /usr/share/kiterunner/routes-large.kite -o api_routes.txt
```


## Subdomain takeover 

```bash
subzy run --targets bmw_resolved.txt --hide-fails
```

```bash
subzy run --targets bmw_resolved.txt --verify_ssl --concurrency 20
```

```bash
subzy run --targets bmw_resolved.txt | tee subzy_results.txt
```




