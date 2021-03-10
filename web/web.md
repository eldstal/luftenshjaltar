# Web

## Web

[OWASP Web Security Testing guide](https://owasp.org/www-project-web-security-testing-guide/)

### Enumeration

[Tool](https://github.com/michelin/ChopChop)

* robots.txt
* .htaccess
* API endpoints called by javascript/etc

### Sneaky techniky

* Does the server support `HTTP PUT`?
* Do all API endpoints actually verify authentication?
  * Missing auth parameters?
  * Provided but **invalid** parameters?

### JWT

[Tool](https://github.com/ticarpi/jwt_tool)

#### Public key mixup

[Tell me more](https://blog.silentsignal.eu/2021/02/08/abusing-jwt-public-keys-without-the-public-key/)  
[Tool](https://github.com/silentsignal/rsa_sign2n)

If a JWT uses RS256, the backend can be tricked into validating with HS256 instead. A HS256 JWT is signed with the _public_ key, which can perhaps be deduced from a few samples of RS256 JWTs.

### HTTP Response Splitting

[Tell me more](https://owasp.org/www-community/attacks/HTTP_Response_Splitting)  
[Tool](https://github.com/ryandamour/crlfmap)  
[Tool](https://github.com/dwisiswant0/crlfuzz)

Unvalidated user input leaks into the early parts of a HTTP response. The attacker can inject CRLF, allowing them to control the rest of the response, including additional headers and content.

### 40x bypassing

* [byp4xx](https://github.com/lobuhi/byp4xx)
* [4-ZERO-3](https://github.com/Dheerajmadhukar/4-ZERO-3)
* [403fuzzer](https://github.com/intrudir/403fuzzer)

### CORS misconfiguration

[Tool](https://github.com/s0md3v/Corsy)

### WAF

#### Identification

* [wafalyzer](https://github.com/NeuraLegion/wafalyzer)

#### Bypassing

[A Methodology](https://blog.isec.pl/waf-evasion-techniques/)  
[Some tips](https://labs.secforce.com/posts/bypassing-wafs-web-application-filters/)

**Mess with the HTTP request**

Pass two different `content-length` headers. Maybe the WAF uses one and the target server uses the other?

Set `Content-Type: */*` if certain filetypes \(XXE?\) are blocked.

Try a `POST` instead of a `GET`. If the filtering is applied to the requested path but the naughty string is in the parameters...

**Find the unprotected server**

[Tell me more](https://delta.navisec.io/a-pentesters-guide-part-5-unmasking-wafs-and-finding-the-source/)  
[DNS history](https://securitytrails.com/domain/0x00sec.org/dns)  
[SSL history](https://crt.sh)  
[Subdomains](http://dnsdumpster.com/)

[Lilly](https://github.com/Dheerajmadhukar/Lilly) \(requires a shodan API key\)

Maybe `dev.target.com` points to the actual server behind the WAF? Maybe `target.com` _used to_ point straight to the server, and the server is still the same?

**Bypass blocklists**

URL-encode some part of your naughty string. Maybe the filter is applied before decoding?

[Try multiple layers of encoding](https://www.redtimmy.com/how-to-hack-a-company-by-circumventing-its-waf-for-fun-and-profit-part-2/), strange as it may sound.

### NoSQL injection

[Tool](https://github.com/Charlie-belmer/nosqli)

### Exposed SCM data

* [ayfabtu](https://github.com/tautology0/ayfabtu) supports svn, mercurial and git.
* [GitTools](https://github.com/internetwache/GitTools)
* [gitjacker](https://github.com/liamg/gitjacker)
* [git-dumper](https://github.com/arthaud/git-dumper)

### CSP

[Evasion techniques](https://cspscanner.com/csp-bypasses)

### SSRF

[What to do with a blind SSRF](https://blog.assetnote.io/2021/01/13/blind-ssrf-chains/)

### XSS

[Event handlers by tag](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)  
[Mutation points](https://twitter.com/FaniMalikHack/status/1353309941197631488?s=20) within an `<a>` tag

