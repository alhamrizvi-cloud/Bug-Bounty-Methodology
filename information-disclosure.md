This is an example of Information Disclosure Finding, change ?url=*.bmw.de/* into targeted domain and change output file 
> bmw_all_urls.txt


## CDX – Fetch All Archived URLs (bmw.de)

```bash
curl "https://web.archive.org/cdx/search/cdx?url=*.bmw.de/*&collapse=urlkey&output=text&fl=original" > bmw_all_urls.txt
```

## Normalize + Deduplicate URLs

```bash
cat bmw_all_urls.txt | uro > bmw_all_urls_clean.txt
```

## Grep Juicy / Sensitive Extensions

```bash
grep -E '\.xls|\.xml|\.xlsx|\.json|\.pdf|\.sql|\.doc|\.docx|\.pptx|\.txt|\.zip|\.tar\.gz|\.tgz|\.bak|\.7z|\.rar|\.log|\.cache|\.secret|\.db|\.backup|\.yml|\.gz|\.config|\.csv|\.yaml|\.md|\.env|\.ini|\.git' bmw_all_urls_clean.txt > bmw_juicy_files.txt
```

## Direct CDX Filter (Only Juicy Files)

```bash
curl "https://web.archive.org/cdx/search/cdx?url=*.bmw.de/*&collapse=urlkey&output=text&fl=original&filter=original:.*\.(xls|xml|xlsx|json|pdf|sql|doc|docx|pptx|txt|zip|tar\.gz|tgz|bak|7z|rar|log|cache|secret|db|backup|yml|gz|config|csv|yaml|md|env|ini|git)$" | tee bmw_filtered_urls.txt
```

## Browser View (Manual Hunting/optional)

```
https://web.archive.org/web/*/bmw.de/*
```

## Robots.txt (Historical/optional)

```
https://web.archive.org/web/*/bmw.de/robots.txt
```

## Find PDFs with Sensitive Keywords

```bash
cat bmw_filtered_urls.txt | grep -Ei '\.pdf' | while read -r url; do

curl -s "$url" | pdftotext - - | grep -Eaiq '(confidential|internal|restricted|private|do not share|proprietary|invoice|salary|contract|agreement|passport|identity|bank|credit card)' && echo "$url"
done
```

## Golden Method – Deleted (404) Files

```
https://web.archive.org/web/*/<PASTE_404_URL_HERE>
```

## One-Shot Automation (bmw.de)

```bash
bash wayback.sh
```

```
bmw.de
```
