# What is CORS?

CORS (Cross-Origin Resource Sharing) is a browser security mechanism that controls which external origins are allowed to read responses from a web application. A misconfiguration can allow an attacker-controlled origin to access sensitive data using a victim’s authenticated session.

## 🥇 Corser (Go – Advanced CORS Scanner)

```bash
corser -i urls.txt -o corser_results.txt
```

---

## 🥈 Corsy (Python – Payload-based CORS Scanner)

```bash
python3 corsy.py -i urls.txt -t 20
```

---

## 🧪 CORStest (Basic CORS Tester)

```bash
python3 corstest.py -i urls.txt
```

---

## ⚡ cors-scanner (Multi-threaded Scanner)

```bash
python3 cors-scanner.py -i urls.txt -t 50
```

---

## 🔍 CorsMe (CORS Misconfiguration Scanner)

```bash
python3 corsme.py -i urls.txt
```

