# Heap

[Tell me more](https://0xabe.io/ctf/exploit/2016/04/24/BlazeCTF-dmail.html)

Using a double-free, an arbitrary address can be introduced in the free list.

TODO: Learn about tcache poisoning, which is related to this.

## Integer Overflows and `malloc()`

[Tell me more](https://undeadly.org/cgi?action=article&sid=20060330071917)

There's a pretty common pattern used to allocate an array of `n` objects of type `T`: `malloc(sizeof(T) * n)` 

A _very_ large `n` can cause an overflow of the `size_t` passed to `malloc()`. An attacker who controls `n` can cause a smaller memory area to be allocated, leading to a buffer overflow when the application tries to use that shiny new buffer.

## Attacks

### \(glibc 2.29\) House of Corrosion 

[Tell me more](https://github.com/CptGibbon/House-of-Corrosion)

