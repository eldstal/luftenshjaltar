# Tools

## Using a provided libc

[Tell me more](https://www.ayrx.me/using-a-non-system-libc)  
[Tool](https://github.com/Ayrx/reutils/blob/master/bin/change_glibc)

Some challenges come with a provided `libc.so` and it is helpful to debug against that specific version rather than whatever you have in your system. The tool above patches `RUNPATH` in an ELF binary to force it to use the `libc` of your choice. It requires a copy of `ld-linux` which matches the `libc.so`. You can probably find the proper version using [libc-db](https://libc.blukat.me).

## Identifying server-side libc

[Database](https://libc.blukat.me/)  
[Tool](https://docs.pwntools.com/en/stable/libcdb.html)

If you can leak the address of a known symbol \(Say, `printf@got.plt`\), you can look up known compiled versions in [libc-db](https://libc.blukat.me/). This allows you to tailor your exploit to the server.

