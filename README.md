# UB-Collection
Collection of C++20 undefined behavior, ideally with examples.

## Why this list?

There once was a [list of undefined behavior in C99](http://port70.net/~nsz/c/c99/n1256.html#J.2) and I don't really want to learn C99. At the same time I don't want to produce undefined behavior in C++20 either, so maybe it's a good idea to have a list of all undefined behavior in one place with a few examples of what you should not do.

## What is UB?

The C++ specification defines three dangerous things:

* undefined behavior: may result in anything and does not need to be documented [C++20, 3.57, page 8]
* unspecified behavior: depends on the compiler and needn't be documented [C++20, 3.58, page 8]
* implementation defined behavior: depends on the compiler and needs to be documented [C++ 20, 3.23, page 5]

## How much UB is there?

There are 282 findings for the word "undefined" in the C++20 specification. So, it's probably less than that since some of the words occur in definitions and examples. The C99 specification had a [dedicated list for undefined behavior](http://port70.net/~nsz/c/c99/n1256.html#J.2) with 191 entries.

Check it yourself:

* [Where do I find the current C or C++ Standard documents?](https://stackoverflow.com/questions/81656/where-do-i-find-the-current-c-or-c-standard-documents)
* [C++20 draft, N4860](https://isocpp.org/files/papers/N4860.pdf)

## Further reading

* LLVM blog

  * [What every C programmer should know about undefined behavior, Part 1/3](http://blog.llvm.org/2011/05/what-every-c-programmer-should-know.html)

    * > C is not a high level assembler

    * > C++ and Objective-C have directly inherited plenty of issues from it

    * > produce code that formats your hard drive, does completely unexpected things, or worse

    * > designers of C wanted it to be an extremely efficient low-level programming language

    * > languages like Java (and many other 'safe' languages) have eschewed  undefined behavior because they want safe and reproducible behavior  across implementations

  * [What every C programmer should know about undefined behavior, Part 2/3](https://blog.llvm.org/2011/05/what-every-c-programmer-should-know_14.html)

    * > different compilers often have substantially different optimizers

    * > Undefined behavior and security don't mix well
    
    * > there are a wide range of *very reasonable* looking C fragments that are completely invalid
    
    * > "Working" code that uses undefined behavior can "break" as the compiler evolves or changes 
    
    * > optimization based on undefined behavior can start being triggered on buggy code at any time in the future
    
    * > there is no good way to determine whether a large scale application is free of undefined behavior
    
  * [What every C programmer should know about undefined behavior, Part 3/3](https://blog.llvm.org/2011/05/what-every-c-programmer-should-know_21.html)

    * From an optimization alone it can't be judged whether it's from undefined behavior or valid code
    * it seems that especially macros (preprocessor stuff) is difficult to handle, because it produces code that humans wouldn't write.

* John Regehr

  * [A guide to Undefined behavior in C and C++, Part 1/3](https://blog.regehr.org/archives/213)
  * [A guide to Undefined behavior in C and C++, Part 2/3](https://blog.regehr.org/archives/226)
  * [A guide to Undefined behavior in C and C++, Part 3/3](https://blog.regehr.org/archives/232)

## Finding undefined behavior

According the LLVM blog, there is no good way to determine if a large scale application is free of undefined behavior. However, there are some tools that might at least detect some of it:

* LLVM compiler warnings
  * turn on `-Wall` and `-Wextra`
  * turn on `-fwrapv` to catch signed integer overflows at runtime
  * turn on `-fno-strict-aliasing` for reasons I do not fully understand :-)
  * turn on `-Wbool-conversions` to catch bool assignments to pointers
  * turn on `-Wshift-overflow` for arithmetic shift overflows
  * turn on `-Wliteral-conversion` to avoid having constants of the wrong type
  * limitation: can perform static analysis only.
  * can find: uninitialized variables
* Valgrind memcheck (on Linux)
  * limitation: it works on the binary and cannot know what code has been removed on the steps between the source code to machine language. It will e.g. not find oversized shift amounts and signed integer overflows. 
  * can find: random pointer dereferences
* Application Verifier (on Windows)
  * limitation: it works on the binary and was designed to check the Windows API calls only.
  * can find: random pointer dereferences, inconsistencies in Windows API calls, e.g. dealing with handles and synchronization objects.
* Clang with `-fcatch-undefined-behavior`
  * limitations: adds instrumentation into the binary, making it slower. Can't find random pointer dereferences.
  * can find: signed integer overflow
* Clang static analyzers
  * limitations: as a static code analyzer, it can't find errors at runtime.
  * can find: null pointer dereferences; other types of undefined behavior
* Klee
  * limitations: not practical for large scale applications

## Undefined behavior

Mentioned in the LLVM blog:

* Use of an uninitialized variable
* Signed integer overflow
* Oversized shift amounts
* Derefences of wild pointers and out of bounds array access
* Dereferencing a NULL pointer
* Violating type rules
* Sequence point violation
* Race conditions in multithreading
* violating "restrict"
* Divide by zero

