# Escape

## Homebrew shells

[List for \*nix](https://gtfobins.github.io/) \([Tool](https://github.com/mzfr/go-gtfo)\)  
[List for windows](https://lolbas-project.github.io/)

If the challenge is a homemade shell with some artificial limitations, gtfobins and LOLBAS list myriad ways to escape. These aren't vulnerabilities, per se, but intended functionality of applications that are commonly installed.

## Bash

[Good ideas](https://wiki.bash-hackers.org)  
[List of parameter expansions](https://wiki.bash-hackers.org/syntax/pe)

### Forbidden strings

```bash
${NOSUCHVAR:-fla}${NOSUCHVAR:-g.txt}    # expands to flag.txt
```

### Stars aren't the only wildcard

```bash
fla?.txt
fl{a,A}g.txt
fl[a-z]g.txt
```

## Python

[Tell me more](https://book.hacktricks.xyz/misc/basic-python/bypass-python-sandboxes)

Python has **so many** ways to introspect, reflect, reload, import, execute unintended code.

Here's a pretty simple one:

```python
import statistics
statistics.random._os._execvpe("/bin/sh", [], {})
```

## Docker

* [deepce](https://github.com/stealthcopter/deepce/), a vulnerability scanner.  
* [grype](https://github.com/anchore/grype), a vulnerability scanner.  
* [WhaleScan](https://github.com/nccgroup/whalescan), scans windows containers.

## Electron

[Tell me more](https://github.com/doyensec/awesome-electronjs-hacking)

[view-source:](https://twitter.com/HusseiN98D/status/1325464364569276417) isn't necessarily blocked like `file://` is. Perhaps the app even uses its own [custom URI scheme](https://twitter.com/zer0pwn/status/1325581291060826112)?

