# Basics

### Calling conventions

* Thiscall
  * microsoft 32bit c++ specific 
  * this pointer in ECX, rest of arguments on the stack
  * [tell me more](https://docs.microsoft.com/en-us/cpp/cpp/thiscall?view=msvc-160)
* fastcall
  * The first 4 arguments are stored in registers, the rest on the stack
    * windows uses the registers RCX, RDX, r8 and r9
    * [tell me more](https://docs.microsoft.com/en-us/cpp/cpp/fastcall?view=msvc-160)
* stdcall
  * used by 32bit windows.
  * arguments are pused in reverse order onto the stack.
  * the callee cleans up the stack \(arguments\)
  * [tell me more](https://docs.microsoft.com/en-us/cpp/cpp/stdcall?view=msvc-160)
* cdecl
  * used by 32bits \*nix
  * same as stdcall but the caller cleans up the stack
  * [tell me more](https://docs.microsoft.com/en-us/cpp/cpp/cdecl?view=msvc-160)

