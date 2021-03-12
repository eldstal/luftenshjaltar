# Hints

CTF challenges are usually pretty close to minimal working examples of some specific exploit. Here are some things to look out for, and what avenues they may open.

## pwn

### User controlled `malloc()` and `free()`

[Tell me more](../binary/heap.md)

There's no reason to allow this if it isn't for heap manipulation. This can also apply to the `std::` containers in C++ programs, they do some reallocation behind the scenes.

### pthreads

[Tell me more about stacks](../binary/vectors.md#hijack-the-stack-of-a-created-pthread)

New threads get their stack allocated with mmap, and it may or may not be possible to double-free your way to controlling that space.

[Tell me more about .got](../binary/vectors.md#threads-have-their-own-got-plt-it-seems)

Apparently, threads have their own procedure linkage?

### C++ containers mixed with pointers

[Tell me more](../binary/vectors.md#forged-c-object)

Containers do dynamic allocation in the background. Pointers to their internals don't stay valid.

### Method calls with odd signatures

A method call on an object pointer may be resolved via a vtable, which may be modifiable. If the call has an oddly specific signature \(say, dereferences a pointer?\), this is a useful gadget for a hijacked vtable.

### `fork()`

If some protection is enabled in the child process \(for example, seccomp\), you may be able to break into the parent process somehow to evade it. Do they communicate? Shared file descriptors? Shared memory? Pipes?

In addition, the processes all share the same pre-initialized random stack cookies and ASLR, so a forking server gives you multiple stabs at those!

### `printf()` on non-constant format

There's no reason to `printf(user_input)` unless you're opening up for a format string exploit. A good fmtstr will give you anything you want from the stack: Canary value, code address, stack address, possibly heap address. If you look far enough back, the stack before `main` contains environment variables!

### `scanf()`

* `%s` lacks bounds checking
* `%f` and friends will leave the value uninitialized if you pass `-` or `+`.

### `system()` imported from libc

OK, this one is pretty obvious. It's not a way in, but it's a pretty useful gadget for a payload.

### `union` types

If the program appears to use unions \(i.e. an `int` sharing the space of a `char*` in a structure, for example\), this opens up for type confusion.

### Lots of visible `rand()` output

[Tool](https://github.com/ALSchwalm/foresight)

If you can see a long sequence of pseudo-random numbers, perhaps the internal state of the generator can be reconstructed. This lets you predict upcoming numbers!

## Web

### "Report to the admin" and similar forms

This probably means an automated "administrator" will visit some link you can affect. XSS all the way to the bank.

Actually, if it's a forum-type application, try and see if any bots visit your links.

