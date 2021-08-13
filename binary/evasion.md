# Evasion

## ASLR

### Unknown libc version

[Database](https://libc.blukat.me/) of known offsets in libc packages  
[Another interface](https://libc.rip/) to that database

So you've leaked a libc address, but you don't know the distance to `system`?

### Application pointer leak

A stack or .text pointer certainly is most useful. If you can locate libc, you're ready to go.

Craft a string without a NULL terminator and see what falls out!

Allocate a buffer but never write to it. See what it contains!

### Format string vulnerability

On x86-64, arguments 7 and onward are on the stack. Use GDB to find offsets with interesting values. You can often score a stack address \(local vars used as parameters\), a .text address \(return back into `main` for example\) and a .libc address \(return from `main` into `__libc_start_main`\) all at once! If you're out of ideas, you can also see the environment variables passed to `main` on the stack.

```text
printf("%7$lx %380$lx\n");
```

### Overwrite only a partial address

If your write primitive is limited, perhaps you can get away with only slightly overwriting an address in `.got` or the like. Little-endian can be a blessing.

#### Massage ASLR first

[Example](https://ctftime.org/writeup/27030)  

Typically, the base of `libc` is not only random, but the lowest 12 bits are uneven. If you have a complete libc leak but a write primitive limited to a single byte, you might try to restart the application until you have a nice predictable third nibble.

### Forking server

Parent and Child processes share the same ASLR allcations \(app and libraries are mapped identically\). If you crash the child while leaking pointers, no worries! Just spawn a new one and keep working!

### Get `mmap()` to allocate next to another segment

[Tell me more](https://amritabi0s.wordpress.com/2016/06/11/asis-ctf-quals-2016-b00ks-writeup/)

It seems that the heap allocator likes to snug up against other allocated memory...?

### Use `malloc()` to infer empty spaces

### Finding the stack via libc

[Tell me more](https://0xabe.io/ctf/exploit/2016/04/24/BlazeCTF-dmail.html)

If you've leaked libc, it contains a global variable pointing to the env data, which is on the stack.


## NX bit

### `mprotect` shellcode to be executable

NX binaries never have Write and eXecute permissions on the same memory page. If we can call `mprotect`, that can be adjusted.

### Invoke `_dl_make_stack_executable`

This function is part of the GNU dynamic linker, but linked into some binaries for some weird reason. Maybe only in statically linked binaries? Does exactly what it looks like.

### ROP

Use existing code instead. There's usually a `pop rdi` gadget in libc which is useful to smuggle in a first argument.

#### SCOP

[Tell me more](https://www.alchemistowl.org/pocorgtfo/pocorgtfo20.pdf) \(See paper 20:07\)

Apparently, it's cool to reduce the amount of data hanging around in executable space.

## Pointer Guard / pointer mangling

### Use `_dl_fini` to demangle function pointers

[Tell me more](https://m101.github.io/binholic/2017/05/20/notes-on-abusing-exit-handlers.html)

This is the function which calls `.fini` pointers, and is sometimes registered with the exit handlers \(see above\). If glibc has pointer guard enabled, this can serve as a good _known_ function pointer to calculate the guard value.


## Stack Cookie/Canary

GCC uses a stack canary which contains \(starts with\) a NULL byte.

### Format string vulnerability

`printf("%1337$lx")`

If you're able to print anything that's on the stack, why not the cookie?

### Uninitialized stack buffers

If you can leak an uninitialized stack buffer, maybe there's a cookie laying around there from something else that used to have that stack space?

`scanf` is a possible [vector](../general/hints.md#scanf) for this

### Forking server

Cookie is the same across parent and all child processes.

## Syscall filters \(seccomp\)

### Lesser-known syscalls like `execveat` \(via `fexecve`\)

[Tell me more](https://magisterquis.github.io/2018/03/31/in-memory-only-elf-execution.html)

If there's a syscall blocklist, maybe this one was forgotten? It's like `execve` but works on an open file descriptor. Perhaps combine with `memfd_create` which opens memory as a file descriptor. The "executable" can even be a shell script!

[This broken writeup](https://ctftime.org/writeup/17907) creates an "executable" with the target as a shebang `#!/read_flag` in order to also circumvent AppArmor.

This syscall is only available on linux &gt;= 3.19.

### 32-bit syscalls

If the challenge uses seccomp to limit syscalls, the filter may be faulty. Use seccomp-tools to dump the BNF filter and verify. x86 syscall numbers are not the same as x86\_64, and execution can be switched to x86 mode \(and thus the X32 ABI\) by

```text
push 0x23
push <code address>
retf
```

If the seccomp filter explicitly checks architecture, 32-bit syscalls can still be accessed using syscall numbers + 0x40000000.

