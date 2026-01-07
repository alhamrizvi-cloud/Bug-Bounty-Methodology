### WordPress Detection

```bash
httpx -l hosts.txt -path /wp-login.php -status-code -silent
```


### WPScan Full Enumeration

```bash
wpscan --url https://target.com --api-token API_TOKEN
```


### WPScan Plugins Enumeration

```bash
wpscan --url https://target.com --enumerate p --api-token API_TOKEN
```

### WPScan Themes Enumeration

```bash
wpscan --url https://target.com --enumerate t --api-token API_TOKEN
```


### WPScan Users Enumeration

```bash
wpscan --url https://target.com --enumerate u
```


### WPScan Vulnerable Plugins Only

```bash
wpscan --url https://target.com --enumerate vp --api-token API_TOKEN
```


### WPScan Aggressive Mode

```bash
wpscan --url https://target.com --plugins-detection aggressive --api-token API_TOKEN
```

### Nuclei WordPress CVE Scan

```bash
nuclei -u https://target.com -t http/cves/wordpress/
```


### Nuclei WordPress Files Exposure

```bash
nuclei -u https://target.com -t http/exposures/
```


### WordPress Backup Files

```bash
dirsearch -u https://target.com -w wordlists/wp-backups.txt
```


### wp-config.php Exposure

```bash
curl -I https://target.com/wp-config.php
```

---

### REST API Enumeration

```bash
curl https://target.com/wp-json/wp/v2/users
```


### XML-RPC Enabled Check

```bash
curl -X POST https://target.com/xmlrpc.php
```

### XML-RPC Brute Force (Detection)

```bash
wpscan --url https://target.com --passwords passwords.txt --usernames users.txt
```


### Theme File LFI Test

```bash
curl https://target.com/wp-content/themes/theme/file.php?file=../../../../etc/passwd
```

### Plugin File LFI Test

```bash
curl https://target.com/wp-content/plugins/plugin/file.php?file=../../../../etc/passwd
```

### WordPress Upload Directory Access

```bash
httpx -u https://target.com/wp-content/uploads/ -status-code
```

### wp-admin Access Check

```bash
httpx -u https://target.com/wp-admin/ -status-code
```

### WordPress Version Detection

```bash
curl https://target.com | grep -i "generator"
```

