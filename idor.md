## IDOR Parameter Fetch

**Requirement:** `parameter.txt` also it depends on your txt file, copy the first 50 lines of your txt file aand send it to chatgpt and ask it give commands to fetch idor parameters from this file,

this is just an example

```bash
grep -Ei "(\\?|&)(VPNR|OutletID|page|session|wer|go2|bmw)=" www.bmw.de.txt > idor_only.txt
grep -Ev "lb(matchtype|creative|network|keyword)|gclid=|tl=" idor_only.txt > idor_clean1.txt
grep -Ei "(VPNR=|OutletID=|session=|wer=)" idor_clean1.txt > idor_clean2.txt
sort -u idor_clean2.txt > idor_final.txt
```
