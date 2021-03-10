# Payloads

## The "Magic Gadget"

[Tell me more](https://0xabe.io/howto/exploit/2016/03/30/Radare2-of-the-Lost-Magic-Gadget.html)  
[Tool](https://github.com/david942j/one_gadget)

libc contains code to `execve("/bin/sh")` in several places. If you know the libc in use and its loaded base address \(see [ASLR](Evasion#markdown-header-aslr)\), you can just jump there.

## ROP techniques

[Exercises](https://ropemporium.com/)

Tools:

* [AngROP](https://github.com/angr/angrop)
* [ROPgadget](https://github.com/JonathanSalwan/ROPgadget)
* [pwn.ROP](https://docs.pwntools.com/en/stable/rop/rop.html)

Good to know:

* `call` on x86-64 requires a 16-byte aligned stack. Not always an issue, but it can be. Pad your ROP chain.

### Ret2CSU

[Tell me more](https://www.rootnetsec.com/ropemporium-ret2csu/)

_TODO: Learn about this_

### Ret2ld-resolve

[Tell me more](https://gist.github.com/ricardo2197/8c7f6f5b8950ed6771c1cd3a116f7e62)  
[Tell me not enough](https://ir0nstone.gitbook.io/notes/types/stack/ret2dlresolve)  
[Tool](https://docs.pwntools.com/en/stable/rop/ret2dlresolve.html)

`.got` is populated by the linker \(ld\) on demand, when a `.plt` function is called for the first time.

Lookups are performed using some giant tables \(`JMPREL`, `SYMTAB`, `STRTAB`\), and _string_ matching in these tables.

The top of `.plt` is a stub which calls `dl-resolve`, and takes an index into these tables as a parameter.

1. Write a fake `SYMTAB` and `STRTAB` somewhere, containing an entry for `"system"`
2. ROP into `dl-resolve` with a well-chosen table index \(overflow the existing `STRTAB` into our fake buffer\)
3. `dl-resolve` will go find `system` and call it for us!

**Danger** [Stack alignment](https://ropemporium.com/guide.html#Common%20pitfalls) is an issue, and `system()` relies on the stack being 16-byte aligned when it's called. If your chain doesn't work, try adding a `ret;` gadget to it for a different alignment.

## Syscall cobbling

[Linux syscalls](https://syscalls.w3challs.com/)

If you've got ROP control, maybe you can emulate a system call?

#### x86

[x86 Syscalls](https://syscalls.w3challs.com/?arch=x86)

Set `$eax` and find an `int 0x80` instruction to lift off! Arguments are in `$ebx`, `$ecx`, `$edx`, `$esi`.

#### x86\_64

[x86\_64 Syscalls](https://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/)

Making a system call is as easy as setting `$rax` and executing the `syscall` instruction. Arguments are in `$rdi`, `$rsi`, `$rdx`.

32-bit syscalls are also available, see [Evasion](Evasion#markdown-header-32-bit-syscalls) for more info about that.

#### Lesser-known syscalls

If there's a blocklist, here are some less obvious tricks:

* `arch_prctl` can be used for an arbitrary write \([Tell me more](https://ctftime.org/writeup/18792)\)
* `brk` can be used to find the address of the heap \([Tell me more](https://ctftime.org/writeup/18792)\)

#### Register control

The `ax` registers which control syscall number also happen to be the return value registers in the SystemV calling convention. If you control a `read()` before your ROP chain, for example, it will return the number of bytes read. Send it 11 bytes and you've lined yourself up for `execve()`!

Alternatively, check out the Sigreturn syscall below to control _all_ the registers!

## Sigreturn Oriented Programming

[Tell me more](https://translate.google.com/translate?sl=auto&tl=en&u=https://firmianay.gitbooks.io/ctf-all-in-one/content/doc/6.1.4_pwn_backdoorctf2017_fun_signals.html)  
[Tool](https://docs.pwntools.com/en/stable/rop/srop.html)

If you can push to the stack and execute syscall 15, you can control all the registers at once. Neat.

## Reverse shells

[Reverse Shell Generator](https://weibell.github.io/reverse-shell-generator/)

