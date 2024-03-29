# Web

[OWASP Web Security Testing guide](https://owasp.org/www-project-web-security-testing-guide/)

## Enumeration

[ChopChop](https://github.com/michelin/ChopChop)  
[WhatWeb](https://github.com/urbanadventurer/WhatWeb)  
[BlindElephant](http://blindelephant.sourceforge.net/)  
[FeroxBuster](https://github.com/epi052/feroxbuster)  
[List of interesting URLs](https://github.com/random-robbie/bruteforce-lists)  
[Another sweet set of lists](https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content)

* robots.txt
* .htaccess
* API endpoints called by javascript/etc

## Domains

[DNS Dumpster](https://dnsdumpster.com/)  
[DNS History](https://completedns.com/dns-history/)  
[Domains sharing IP](https://networksdb.io/)

## Sneaky techniky

* Does the server support `HTTP PUT`?
* Do all API endpoints actually verify authentication?
  * Missing auth parameters?
  * Provided but **invalid** parameters?
* Does the application use any [binary libraries](https://blog.silentsignal.eu/2020/04/20/uninitialized-memory-disclosures-in-web-applications/)?
* What happens if you remove the `Host` header?
* What other domains are hosted at the same IP?

## Probing payloads

```text
<img src=x onerror=alert(1)> {{1*3}} ${1*3} test
‘“></script>{{2*2}}${2*2}
```

## JWT

[Tool](https://github.com/ticarpi/jwt_tool)

Try setting `alg: none` or `alg: nonE` etc. Maybe that's enough.

### Public key mixup

[Tell me more](https://blog.silentsignal.eu/2021/02/08/abusing-jwt-public-keys-without-the-public-key/)  
[Tool](https://github.com/silentsignal/rsa_sign2n)

If a JWT uses RS256, the backend can be tricked into validating with HS256 instead. A HS256 JWT is signed with the _public_ key, which can perhaps be deduced from a few samples of RS256 JWTs.

## Path Traversal

#### Java applications

[Tell me more](https://gist.github.com/harisec/519dc6b45c6b594908c37d9ac19edbc3)

## HTTP Response Splitting

[Tell me more](https://owasp.org/www-community/attacks/HTTP_Response_Splitting)  
[Tool](https://github.com/ryandamour/crlfmap)  
[Tool](https://github.com/dwisiswant0/crlfuzz)

Unvalidated user input leaks into the early parts of a HTTP response. The attacker can inject CRLF, allowing them to control the rest of the response, including additional headers and content.

## HTTP Request Smuggling

[Sweet Infographic](https://securityzines.com/comics/hrs.html)  
  
A proxy in front of the server merges multiple incoming requests into a single connection to the backend server.

A malicious HTTP request has both a short `Content-Length` header \(which is proper\) and an additional `Transfer-Encoding: Chunked` header \(which is ignored by the proxy\). It is immediately followed by an authenticated request from a legitimate user.

The proxy concatenates the requests and passes them on to the backend server, still via HTTP. The server parses the malicious request as `Transfer-Encoding: Chunked` and ignores the  `Content-Length`. As a result, the malicious result is parsed short, and the tail end of its data is interpreted by the server as the start of the next request.

As a result, the second request handled by the server is malformed. Malicious control over the HTTP command and headers, but includes the headers of the legitimate request as well. Oops. 

## 40x bypassing

* [4-ZERO-3](https://github.com/Dheerajmadhukar/4-ZERO-3)
* [byp4xx](https://github.com/lobuhi/byp4xx)
* [403fuzzer](https://github.com/intrudir/403fuzzer)

## CORS misconfiguration

[Tool](https://github.com/s0md3v/Corsy)

## WAF

### Identification

[wafalyzer](https://github.com/NeuraLegion/wafalyzer)  
[wafw00f](https://github.com/EnableSecurity/wafw00f)

### Bypassing

[A Methodology](https://blog.isec.pl/waf-evasion-techniques/)  
[Some tips](https://labs.secforce.com/posts/bypassing-wafs-web-application-filters/)  
[Unicode shenanigans](https://jlajara.gitlab.io/web/2020/02/19/Bypass_WAF_Unicode.html), because _of course_

#### **Mess with the HTTP request**

Pass two different `content-length` headers. Maybe the WAF uses one and the target server uses the other?

Set `Content-Type: */*` if certain filetypes \(XXE?\) are blocked.

Try a `POST` instead of a `GET`. If the filtering is applied to the requested path but the naughty string is in the parameters...

#### **Find the unprotected server**

[Tell me more](https://delta.navisec.io/a-pentesters-guide-part-5-unmasking-wafs-and-finding-the-source/)  
[DNS history](https://securitytrails.com/domain/0x00sec.org/dns)  
[SSL history](https://crt.sh)  
[Subdomains](http://dnsdumpster.com/)  
[CloudFail](https://github.com/m0rtem/CloudFail) for cloudflare-protected sites  
[Lilly](https://github.com/Dheerajmadhukar/Lilly) \(requires a shodan API key\)

Maybe `dev.target.com` points to the actual server behind the WAF? Maybe `target.com` _used to_ point straight to the server, and the server is still the same?

#### **Bypass blocklists**

URL-encode some part of your naughty string. Maybe the filter is applied before decoding?

[Try multiple layers of encoding](https://www.redtimmy.com/how-to-hack-a-company-by-circumventing-its-waf-for-fun-and-profit-part-2/), strange as it may sound.

## NoSQL injection

[Tool](https://github.com/Charlie-belmer/nosqli)

## Exposed SCM data

[ayfabtu](https://github.com/tautology0/ayfabtu) supports svn, mercurial and git.  
[DVCS Ripper](https://github.com/kost/dvcs-ripper) \(svm mercurial, git, BZR\)  
[GitTools](https://github.com/internetwache/GitTools)  
[gitjacker](https://github.com/liamg/gitjacker)  
[git-dumper](https://github.com/arthaud/git-dumper)

## CSP

[Evasion techniques](https://cspscanner.com/csp-bypasses)

## LFI

[fimap](https://github.com/kurobeats/fimap)  
[liffy](https://github.com/hvqzao/liffy)  
[Kadimus](https://github.com/P0cL4bs/Kadimus)  
[Kadabra](https://github.com/D35m0nd142/Kadabra)

Try to use it to get the source of the vulnerable script. That will show you any filtering etc.

PHP has all kinds of [funky pseudo-file protocols](https://book.hacktricks.xyz/pentesting-web/file-inclusion#lfi-rfi-using-php-wrappers), and these are valid wherever file paths are. fimap and liffy support some of these tricks.

## SSRF

[What to do with a blind SSRF](https://blog.assetnote.io/2021/01/13/blind-ssrf-chains/)

## SSTI

[Tell me more](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection)  
[Tool](https://github.com/epinna/tplmap)

Server Side Template Injection

## SQLi

### SQLite

Use a NULL byte to terminate the query and get rid of stuff you can't comment out. Neat.

## XSS

[Event handlers by tag](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)  
[Mutation points](https://twitter.com/FaniMalikHack/status/1353309941197631488?s=20) within an `<a>` tag

