## Subdomain Takeover

```bash
subzy run --targets bmw_resolved.txt --hide-fails
subzy run --targets bmw_resolved.txt --verify_ssl --concurrency 20
subzy run --targets bmw_resolved.txt | tee subzy_results.txt
```

## with nuclei

```bash
nuclei -l dailylive.txt -tags takeover -severity high,critical
```

```bash
nuclei -l dailylive.txt -tags takeover -severity high,critical -stats
```
because of false positives i recommend verifying CNAME No cname matching no bounty
Here you go — **commands only**, clean and correct ✅
This will **dig CNAME for ALL those domains** in one go.



### 1️⃣ Put all flagged hosts into a file(subzy output)

```bash
cat << 'EOF' > cargo_candidates.txt
cast.dailymotion.com
corpostatic.dailymotion.com
developer.dailymotion.com
developers.dailymotion.com
links.notifications.dailymotion.com
links.alerts.dailymotion.com
statics.dailymotion.com
lemeilleurdelacoupedumonde.ondailymotion.com
www.lemeilleurdelacoupedumonde.ondailymotion.com
EOF
```

### 2️⃣ Run CNAME check for all (BEST)

```bash
while read d; do
  echo "### $d"
  dig $d CNAME +short
done < cargo_candidates.txt
```


### 3️⃣ If you also want A records (optional confirmation)

```bash
while read d; do
  echo "### $d"
  dig $d A +short
done < cargo_candidates.txt
```


### 4️⃣ One-liner (no file)

```bash
for d in cast.dailymotion.com corpostatic.dailymotion.com developer.dailymotion.com developers.dailymotion.com links.notifications.dailymotion.com links.alerts.dailymotion.com statics.dailymotion.com lemeilleurdelacoupedumonde.ondailymotion.com www.lemeilleurdelacoupedumonde.ondailymotion.com; do echo "### $d"; dig $d CNAME +short; done
```
