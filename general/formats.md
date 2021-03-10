# Formats

Various data formats have their own quirks.

A couple of things to look out for: 1. Is any data parsed multiple times, using different methods? 2. Is the data validated before or after unicode normalization? 3. Is any of the data part of a _turing complete configuration language_?

## Polyglots

[Tell me more](https://luftenshjaltar.gitbook.io/ctf/reverse/reversing#polyglots)

They can use polyglots to hide their code. We can use polyglots to bypass type checks.

_Yes, officer_, that's a GIF I'm uploading. Oh, but it's also PHP code.

## Unicode

### Normalization

[Tool](https://github.com/JesseClarkND/abnormalizer)

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

## JSON

[Tell me more](https://labs.bishopfox.com/tech-blog/an-exploration-of-json-interoperability-vulnerabilities)

Different parsers deal with special cases differently. Perhaps if an app uses one library for parsing and a different one for parsing+validation, you can bypass validation by duplicating keys?

## XML

Different XML-based formats have their own cavities. Can the schema be bypassed to include dangerous elements?

### XXE

[Tell me more](https://blog.cobalt.io/how-to-execute-an-xml-external-entity-injection-xxe-5d5c262d5b16)  
[Tell me even _more_](https://owasp.org/www-community/vulnerabilities/XML_External_Entity_%28XXE%29_Processing)  
[Work on it](https://gosecure.github.io/xxe-workshop/)

Can the XML "import" external resources?

## CSV

Even something this simple has multiple dialects and varieties. Mess around with whitespace and separators and quotes.

## URL

[Domain names](https://twitter.com/s0md3v/status/1354733673069694978?s=19) can't contain underscores, except that subdomains certainly can. Neat.

Domain name filters can sometimes be bypassed by unicode normalization exploits \(see above\).

Also try these IP tricks:

## IP address

[Twitter thread](https://twitter.com/dave_universetf/status/1342685822286360576)  
[Blog post](https://blog.dave.tf/post/ip-addr-parsing/)  
[Another Blog post](https://ma.ttias.be/theres-more-than-one-way-to-write-an-ip-address/)

There are many less-common IP address formats. Try them.

## PDF

[Insecure PDF features](https://web-in-security.blogspot.com/2021/01/insecure-features-in-pdfs.html)

