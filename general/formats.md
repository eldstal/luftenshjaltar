# Formats

Various data formats have their own quirks.

A couple of things to look out for: 

1. Is any data parsed [multiple times, using different methods](https://about.gitlab.com/blog/2020/03/30/how-to-exploit-parser-differentials/)?   
2. Is the data validated before or after unicode normalization?  
3. Is any of the data part of a _turing complete configuration language_?

## Polyglots

[Tell me more](reversing.md#polyglots)

They can use polyglots to hide their code. We can use polyglots to bypass type checks.

_Yes, officer_, that's a GIF I'm uploading. Oh, but it's also PHP code.

## Regular Expressions

[Tool](https://regex101.com/)

#### Ranges

`[A-z]` includes more than just letters.

#### URL filtering

[Example](https://twitter.com/YShahinzadeh/status/1250889458641141760)  
If a regex is used to split the host, perhaps URL parsing can be fooled. Real parsers take everything before an `@` as a username. 

## Unicode

### Normalization

[Tell me more](https://jlajara.gitlab.io/web/2020/02/19/Bypass_WAF_Unicode.html)  
[Tool](https://github.com/JesseClarkND/abnormalizer)  
[Tool](https://spaceraccoon.github.io/unicollider/)

```text
① ② ③ ④ ⑤ ⑥ ⑦ ⑧ ⑨ ⑩ ⑪ ⑫ ⑬ ⑭ ⑮ ⑯ ⑰ ⑱ ⑲ ⑳ 
⑴ ⑵ ⑶ ⑷ ⑸ ⑹ ⑺ ⑻ ⑼ ⑽ ⑾ ⑿ ⒀ ⒁ ⒂ ⒃ ⒄ ⒅ ⒆ ⒇ 
⒈ ⒉ ⒊ ⒋ ⒌ ⒍ ⒎ ⒏ ⒐ ⒑ ⒒ ⒓ ⒔ ⒕ ⒖ ⒗ ⒘ ⒙ ⒚ ⒛ 
⒜ ⒝ ⒞ ⒟ ⒠ ⒡ ⒢ ⒣ ⒤ ⒥ ⒦ ⒧ ⒨ ⒩ ⒪ ⒫ ⒬ ⒭ ⒮ ⒯ ⒰ ⒱ ⒲ ⒳ ⒴ ⒵ 
Ⓐ Ⓑ Ⓒ Ⓓ Ⓔ Ⓕ Ⓖ Ⓗ Ⓘ Ⓙ Ⓚ Ⓛ Ⓜ Ⓝ Ⓞ Ⓟ Ⓠ Ⓡ Ⓢ Ⓣ Ⓤ Ⓥ Ⓦ Ⓧ Ⓨ Ⓩ 
ⓐ ⓑ ⓒ ⓓ ⓔ ⓕ ⓖ ⓗ ⓘ ⓙ ⓚ ⓛ ⓜ ⓝ ⓞ ⓟ ⓠ ⓡ ⓢ ⓣ ⓤ ⓥ ⓦ ⓧ ⓨ ⓩ 
⓪ ⓫ ⓬ ⓭ ⓮ ⓯ ⓰ ⓱ ⓲ ⓳ ⓴ ⓵ ⓶ ⓷ ⓸ ⓹ ⓺ ⓻ ⓼ ⓽ ⓾ ⓿
```

\([Source](https://www.hahwul.com/phoenix/ssrf-open-redirect)\)

## Unknown encodings

[Tool](https://transformations.jobertabma.nl/) to auto-detect layered text transforms  
[Cyberchef](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwi8mN7r6sTvAhVJa8AKHcXiBlEQFjAAegQIAxAD&url=https%3A%2F%2Fgchq.github.io%2FCyberChef%2F&usg=AOvVaw3cJhXGWs_4gKkmjmhQLSNC) is good for messing around with known chains

## JSON

[Tell me more](https://labs.bishopfox.com/tech-blog/an-exploration-of-json-interoperability-vulnerabilities)

Different parsers deal with special cases differently. Perhaps if an app uses one library for parsing and a different one for parsing+validation, you can bypass validation by duplicating keys?

## XML

Different XML-based formats have their own cavities. Can the schema be bypassed to include dangerous elements?

### XXE

[Tell me more](https://blog.cobalt.io/how-to-execute-an-xml-external-entity-injection-xxe-5d5c262d5b16)  
[Tell me even _more_](https://owasp.org/www-community/vulnerabilities/XML_External_Entity_%28XXE%29_Processing)  
__[You guessed it](https://www.netsparker.com/blog/web-security/xxe-xml-external-entity-attacks/)  
[Tool](https://github.com/luisfontes19/xxexploiter)  
[Work on it](https://gosecure.github.io/xxe-workshop/)

Can the XML "import" external resources?

## CSV

Even something this simple has multiple dialects and varieties. Mess around with whitespace and separators and quotes.

## URL

[Domain names](https://twitter.com/s0md3v/status/1354733673069694978?s=19) can't contain underscores, except that subdomains certainly can. Neat.

Domain name filters can sometimes be bypassed by unicode normalization exploits \(see above\).

[Not all languages](https://github.com/jimen0/differer) parse URLs identically.

Also try these IP tricks:

## IP address

[Twitter thread](https://twitter.com/dave_universetf/status/1342685822286360576)  
[Another twitter thread](https://twitter.com/x0rz/status/928584447292858368)  
[Blog post](https://blog.dave.tf/post/ip-addr-parsing/)  
[Another Blog post](https://ma.ttias.be/theres-more-than-one-way-to-write-an-ip-address/)  
[Tool](https://github.com/D4Vinci/Cuteit)

```text
$ ping 0177.1
$ ping 134744072
$ ping 0x8080808
$ ping 010.0x0000008.00000010.8
$ ping 8.0x0000000000000080808
$ ping 192.168.36095
$ ping 192.11046143
$ ping 0000000001.0000000002.0000000003.000000004
```

There are many less-common IP address formats. Try them.

## PDF

[Insecure PDF features](https://web-in-security.blogspot.com/2021/01/insecure-features-in-pdfs.html)

## Terminals

If, for some reason, your payload is inspected in an actual real-life terminal \(-emulator\), you may want to try [Terminal Escape Injection](https://www.infosecmatter.com/terminal-escape-injection/). Include escapes in your payload such that a terminal will overwrite the sensitive text with benign-looking data.

