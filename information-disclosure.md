

> Replace `TARGET_DOMAIN` once and reuse everywhere.

```bash
export TARGET_DOMAIN=bmw.de
```
## 1️⃣ Fetch ALL Archived URLs (CDX)

```bash
curl "https://web.archive.org/cdx/search/cdx?url=*.$TARGET_DOMAIN/*&collapse=urlkey&output=text&fl=original" > all_urls.txt
```

## 2️⃣ Normalize + Deduplicate URLs

```bash
cat all_urls.txt | uro > all_urls_clean.txt
```

## 3️⃣ Extract Juicy / Sensitive File Extensions

```bash
grep -Ei '\.(pdf|doc|docx|xls|xlsx|csv|txt|log|bak|backup|old|zip|rar|7z|tar|gz|sql|db|env|ini|conf|config|yml|yaml|json|git)$' all_urls_clean.txt > juicy_files.txt
```

## 4️⃣ CDX Direct Filter (High Signal)

```bash
curl "https://web.archive.org/cdx/search/cdx?url=*.$TARGET_DOMAIN/*&collapse=urlkey&output=text&fl=original&filter=original:.*\.(pdf|doc|docx|xls|xlsx|csv|txt|log|bak|zip|rar|sql|db|env|ini|yml|yaml|json|git)$" | tee cdx_filtered.txt
```

## 5️⃣ Identify LIVE vs DEAD URLs

### ✅ Live URLs (200 / Redirects)

```bash
cat all_urls_clean.txt | httpx -mc 200,301,302,307,308 -silent > alive.txt
```

### ❌ Dead URLs (404 / Gone)

```bash
cat all_urls_clean.txt | httpx -mc 404,410 -silent > dead.txt
```

## 6️⃣ GOLDEN METHOD – Recover Dead Files via Wayback

```bash
cat dead.txt | while read url; do
  echo "https://web.archive.org/web/*/$url"
done > dead_wayback.txt
```

➡️ Open `dead_wayback.txt` in browser
➡️ Choose **older snapshot**
➡️ Download sensitive files

## 7️⃣ Find Still-Downloadable Sensitive Files

```bash
cat juicy_files.txt | httpx -mc 200,206 -silent > juicy_alive.txt
```

## 8️⃣ PDF-Only Sensitive Keyword Scan (HIGH VALUE)

```bash
grep -Ei '\.pdf$' cdx_filtered.txt | while read -r url; do
  curl -s "$url" | pdftotext - - 2>/dev/null | \
  grep -Eaiq '(confidential|internal|restricted|private|do not share|proprietary|invoice|salary|contract|agreement|identity|bank)' \
  && echo "$url"
done
```

## 9️⃣ JS Files (Secrets / Endpoints)

```bash
grep -Ei '\.js$' alive.txt > js_files.txt
```

## 🔟 Robots / Sitemap (Historical)

```
https://web.archive.org/web/*/TARGET_DOMAIN/robots.txt
https://web.archive.org/web/*/TARGET_DOMAIN/sitemap.xml
```

## 1️⃣1️⃣ Internal / Admin / Debug Paths

```bash
grep -Ei 'admin|internal|config|debug|test|backup|private|hidden|_admin|_config' alive.txt > internal_paths.txt
```

## 1️⃣2️⃣ Parameters That May Leak Data

```bash
grep -Ei '\?|=|token|key|auth|session|id=' alive.txt > param_urls.txt
```

## 1️⃣3️⃣ FINAL – Information Disclosure Candidates

```bash
cat juicy_alive.txt js_files.txt internal_paths.txt param_urls.txt | sort -u > info_disclosure_final.txt
```
