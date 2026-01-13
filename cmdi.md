## CMD Injection

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
