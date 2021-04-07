# Reversing

## General evaluation

Here are some tools to identify stuff:

* [Detect it Easy](https://github.com/horsicq/Detect-It-Easy)
* [TrID](https://mark0.net/soft-trid-e.html)
* [Polyfile](https://github.com/trailofbits/polyfile)
* `file`
* [binwalk](https://github.com/ReFirmLabs/binwalk)
* [Decodify](https://github.com/s0md3v/Decodify) auto-decodes strings using various methods

## Polyglots

JAR is zip and zip can be combined with all kinds of stuff.

[Polyfile](https://github.com/trailofbits/polyfile) can help map out files like this. Beware zip bombs, PDF bombs and the like, they can make output _explode_.

## Fat binaries

Windows PE can have a windows portion and a DOS portion in the same .exe. Usually the DOS part just prints a helpful message, but nothing prevents a narnia door to another world... [Rusty from justCTF2020](https://ctftime.org/task/14642) is an example of this in the wild.

Macintosh apps can be both Intel and PPC \(and presumably ARM now\).

## Code mangling

[Tell me more](https://www.alchemistowl.org/pocorgtfo/pocorgtfo19.pdf) \(see article 19:04\)

Instruction encodings may have some undefined/reserved bits. If these are garbled, the program may still be _valid_, but your disassembler might choke.

## Anti-debugging

Several techniques exist to prevent dynamic analysis. This section needs more info on that.

### `/proc/self/status`

Does `strace` appear to show a different execution than the normal one? If the program reads `/proc/self/status`, that's probably why. TracerPid in there is non-zero if any debugger is connected:

```text
$ grep TracerPid /proc/self/status
TracerPid:      0

$ strace grep TracerPid /proc/self/status
TracerPid:      6553
```

## Hidden code

[Tell me more](http://blog.k3170makan.com/2018/10/introduction-to-elf-format-part-v.html)

There are a few mechanisms to run code before `main()` gets invoked. Look for `_init_array` \(called by `__libc_start_main`\) and the even sneakier `_preinit_array` \(invoked by the loader\). Try `LD_DEBUG=all` to see what `ld` is up to.

## Modified Python interpreter

[Tell me more](https://medium.com/tenable-techblog/remapping-python-opcodes-67d79586bfd5)  
[Writeup](https://lkmidas.github.io/posts/justctf2020_writeups/#remap)

Apparently it's pretty common to mess with the byte-to-opcode mapping of Python and then embed your custom interpreter.

## Self-modifying code

#### EXE packing

[Detect it Easy](https://github.com/horsicq/Detect-It-Easy) shows all kinds of info about binaries.  
[xvolkolak](http://ntinfo.biz/index.html#xvolkolak) emulates the processor to safely let the binary unpack itself.  
`file` can identify UPX packing, and the [tool](https://github.com/upx/upx) can also unpack.  
[Helpful list](https://www.bttr-software.de/freesoft/arc2.htm#exepck) of DOS/Windows packers.  
[PEiD](https://www.aldeid.com/wiki/PEiD) can identify several DOS/Windows packers.

## Custom tooling

It can pay off, during bigger challenges, to write robust tools.

[pwnlib](https://docs.pwntools.com/en/stable/) has excellent i/o facilities, on top of all the exploit development goodies.  
[gdb-peda](https://github.com/longld/peda) makes GDB a lot friendlier.

### Custom GDB scripts

It's surprisingly easy to implement your own GDB commands using the [python API](https://sourceware.org/gdb/onlinedocs/gdb/Python-API.html):

```python
import os

class MyCMD (gdb.Command):
  """Implements the run_my_stuff command, which will be available at the gdb prompt"""

  def __init__(self):
    super(MyCMD, self).__init__("run_my_stuff", gdb.COMMAND_USER)

  def invoke(self, arg, from_tty):
    gdb.execute("info breakpoints")
    rax = gdb.parse_and_eval("$rax")

MyCMD()
```

If you know what a binary is up to, you can set breakpoints at well-chosen locations and use a GDB script to pretty-print program state.

### Custom Ghidra scripts

[Snippets](https://github.com/HackOvert/GhidraSnippets) of ghidra-python  
[API](https://ghidra.re/ghidra_docs/api/index.html) reference

