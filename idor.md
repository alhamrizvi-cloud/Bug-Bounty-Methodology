Got it — **only titles + commands**, clean and straight 🔥

---

## Setup GF IDOR Pattern

```bash
nano ~/.gf/idor.json
```

---

## Extract IDOR URLs

```bash
cat alivewayback.txt | gf idor > idor_raw.txt
```

---

## Clean Valid URLs

```bash
grep -E "^https?://" idor_raw.txt | grep -v "<" | grep -v "#" | sort -u > idor_clean.txt
```

---

## Filter Object Parameters

```bash
grep -Ei "(id|uid|user|pid|rid|sid|account|profile)=" idor_clean.txt > idor_objects.txt
```

---

## Strict Object Values

```bash
grep -E "=[0-9a-zA-Z_-]{2,}" idor_objects.txt > idor_objects_strict.txt
```

---

## Extract IDOR Parameter Names

```bash
cat idor_objects_strict.txt | unfurl --unique keys | sort -u > idor_params.txt
```

---

## Prepare FUZZ URLs

```bash
cat idor_objects_strict.txt | qsreplace FUZZ | sort -u > idor_fuzz_ready.txt
```

---

## High-Confidence IDOR Filter

```bash
grep -Ev "(search|q=|tag=|category|cmpid)" idor_fuzz_ready.txt > idor_high_confidence.txt
```

---

## ffuf IDOR Test

```bash
ffuf -u "https://target.com/path?param=FUZZ" -w ids.txt -mc 200 -fs 0
```

---

## Turbo Intruder Payload

```text
1
2
3
4
5
```

---

## One-Line Full Pipeline

```bash
cat alivewayback.txt | gf idor | grep -E "^https?://" | grep -v "<" | grep -v "#" | grep -Ei "(id|uid|user|pid|rid|sid)=" | qsreplace FUZZ | sort -u > idor_final_ready.txt
```

---

## Burp Extensions:

https://github.com/Quitten/Autorize
