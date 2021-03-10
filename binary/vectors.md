# Vectors

## Overwrite a return address on the stack

Not much else to say about that.

## Overwrite some application function pointer

If the code itself using func pointers, nuke them.

## vtable techniques

### Forged C++ object

C++ objects with inheritances start with a `vtable` pointer. If you can control the vtable pointer, you can run whatever function you want.

* Corrupt an object pointer to point to **your** vtable pointer.
* Corrupt an object in memory to overwrite the vtable pointer itself.

[This writeup](https://gist.github.com/farazsth98/19cc2268311e248502168b1eb52502f9) shows how a Use-After-Free bug can be exploited this way.

### vtables of `FILE*` objects

[Tell me more](https://dhavalkapil.com/blogs/FILE-Structure-Exploitation/)  
[A writeup](https://ctftime.org/writeup/18765)  
[A broken writeup](https://blog.jsec.xyz/ctf-write-up/2021/01/03/TetCTF-babyformat-write-up.html)

The opaque `FILE*` type points to an internal object which has a vtable-ish thing.

### vtables of stdin/stdout/stderr

[Tell me more](https://github.com/mehQQ/public_writeup/tree/master/0ctf2017/engineOnline)

There are some function pointers here, which get called to clean up after closing the streams. This may be the same as `FILE*` above, not sure.

### Function pointers in `__IO_STR_FIELDS`

[Tell me more](https://sourceware.org/bugzilla/show_bug.cgi?id=23236)  
[Patch](https://github.com/bminor/glibc/commit/4e8a6346cd3da2d88bbad745a1769260d36f2783#diff-379a18f5e20efd52728e4cff64ba0f11a3b17e80f935902205e073e0b0baa141)

glibc before 2.28 had some function pointers which were called by various file handling functions \(see patch above\) and printf derivates. They were never anything other than `malloc` and `free` or `NULL`, so the devs removed the pointer magic.

## Overwrite a function pointer in .got.plt

[Tell me more](https://systemoverlord.com/2017/03/19/got-and-plt-for-pwning.html)

If the binary lacks **FULL** RELRO, `.got.plt` is writable. It's only used on the very first call to a library function, so the sploit needs to overwrite an unused call and trigger it later. You may only need to overwrite the least-significant bytes, depending on where you want to go.

## Threads have their own .got.plt, it seems.

[Tell me more](https://ctftime.org/writeup/18768)

## glibc exit handlers

[Tell me more](https://m101.github.io/binholic/2017/05/20/notes-on-abusing-exit-handlers.html)

if `exit()` is ever called, glibc invokes a set of functions called exit handlers. Check out the implementation of `exit()` for a pointer to `__run_exit_handlers` and the function list it uses.

## Function pointers used by `_call_tls_dtors`

[Tell me more](https://m101.github.io/binholic/2017/05/20/notes-on-abusing-exit-handlers.html)

Called as a part of `__run_exit_handlers` as described above, has a list of destructors to invoke. The function pointers are mangled using [Pointer Guard](https://sourceware.org/glibc/wiki/PointerEncryption) \(see link above for evasion technique\)

## .fini ELF termination function table

This table contains function pointers which are called after `main()` returns. Many challenges disable this by NOPing out the function responsible for calling. Check for xrefs.

## Overwrite `__malloc_hook` and friends

If you know you're running glibc and know where glibc is and have a write primitive, you can overwrite one of the hooks

```text
__malloc_hook
__realloc_hook
__memalign_hook
__free_hook
```

They're just global variables with function pointers in them.

## Hijack the stack of a created pthread

[Tell me more](http://tukan.farm/2016/07/27/munmap-madness/)  
[A writeup](https://blog.dragonsector.pl/2017/03/0ctf-2017-uploadcenter-pwn-523.html)

The system allocates stack memory for new pthreads using `mmap`. It has a somewhat predictable behavior, so you may be able to double-free so that the same memory gets allocated both as a stack space and as user data space. Write whatever you want on that stack and run free!

