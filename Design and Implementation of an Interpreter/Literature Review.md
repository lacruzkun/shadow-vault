
## Introduction
This section is dedicated to reviewing how programming languages are implemented and executed. Existing implenmetations are too complex for what a minimal educational interpreter needs, since most are compiled or hybrid systems with architecture beyond what a lightweight interpreter requires. I will be looking into different execution implementations across different paradigms such as Rust, Haskell, Lisp, Python, Lua, OCaml and JavaScript runtimes. I'll look at their implementation approaches, common challenges they raise, before discussing how these inform my own design.

## How Each Language Implements Execution

Rust uses a strict compiler that enforces memory ownership at compile time and it produces native binary (Klabnik, 2023). what i think is useful here is it's strict rules, some might argue it's bad but i think when a system has one way of doing something it makes it easier for a beginner to wrap their head around it.

Python uses an interpreter that is very flexible, infact too flexible for my liking and it pays that price with memory and execution speed. Variables are stored as a struct that has a pointer to the actual values doing so a variable can store arbitrary data and change data midway through the program (Python Software Foundation, n.d.), now this makes the fetching of variables slow but i think it also hinders a beginner learning because it forces the beginner to track the data type of a variable at different point. Also it's usually a bad practice to change the data type of a variable midway, so unless told a beginner is bound to be confused and make a mistake. But the thing i like about python is it's expressiveness and simplicity, the interpreter of python is so expressive that it can handle different paradigms ("Python (Programming Language)", 2026), and i think imperative and functional paradigms are the most essential ways of thinking a beginner should master to lay down the blocks for future learning.

The Glasgow Haskell Compiler (GHC) is both an interpreter and native-code compiler, it has features such as Glossary:  [[Generalized Algebraic Data Types]] (GADT) and also lazy evaluation ("Haskell", 2026). The whole concept of lazy evaluation is gotten from two intuitive ideas: Perform an evaluation step only when it is necessary; Never perform the same step twice (HaskellWiki, n.d.). most compilers and interpreters of functional programming languages use lazy evaluation, lazy evaluation is hard to implement with imperative features like exception handling and input/output because the order of operations becomes indeterminate (Karachalias et al., 2015).

Lua like most interpreters uses a byte code VM. But Lua is very minimal using only around 38 opcodes for the VM (Man, 2006). Its simplicity is what makes it a joy to use, although i'd rather it had a little bit of a simple type system at the least.

OCaml can be compiled into bytecode and then interpreted by ocamlrun or it can be compiled down to native code by the compiler ocamlopt (Whitington, 2013). Ocaml uses Glossary: Hindley-Milner style type inference which means you almost never write a type explicitly but it's still statically analysed and strictly enforced. The compiler/interpreter also warns when a match on an Glossary: Algebraic Data Type (ADT) is not exhaustive. I like the strictness of the type system of OCaml it makes it so every variable type is known at compile time, although ADT are another matter of their own.

Lisp is a broad term having many flavours, but i'm going to focus on the general idea every lisp implements. Lisp has a tiny set of primitives like lua, but unlike lua which uses minimal opcode (Low level VM instructions) to represent users source code, Lisp's primitives are language-level building blocks used by the programmer to builds everything (McCarthy, 1960). Lisp is a very mathematical model centric language (McCarthy, 1960) and is used as a formalism for computation. It has a manageable interpreter that is self hosted. Lisp has a fascinating property where data is code, and code is data (McCarthy, 1960).

JS runtime is a complex system, using most of mordern infrastructure and research to make the most out of the CPU in terms of performance (V8, n.d.). Because of its complexity it isn't just an interpreter or a compiler it uses different methods at different point of the execution pipeline. The first phase is the Ignition phase, here it's purely interpreted. Next is the TurboFan phase if a function gets called a lot (hot spot) it's  compiled to native code and then called instead of being interpreted every time (V8, n.d.). While the execution is quite fascinating, i'm afraid it's too complicated for what we are going for.


## Cross cutting challenges interpreters face

### Performance vs. Simplicity
Each language tries to tackle the problem of performance and simplicity from different angles. Some accept a strict tradeoff, while others try to get both by shifting the cost to either a build time choice or to runtime adaptivity.

Rust takes the strict approach by producing optimized native binary for the specific platform (Klabnik, 2023). Python however sits on the opposite side of the same tradeoff: it prioritizes flexibility and simplicity, which increases the memory usage and reduces performance (Python Software Foundation, n.d.). OCaml tried to resolve this tradeoff by letting the programmer decide which is conducive for them, the programmer can decide to go with the bytecode interpreter which starts executing instantly but slow on the long-run, or go with the compiler which compiles to native code so is faster when running (Whitington, 2013). JavaScript runtimes go further and gives that choice to the runtime itself, so the runtimes chooses what part of the code to compile to native code and what part should be interpreted, thereby making the runtime system intricate (V8, n.d.).

For a minimal, educational interpreter, going for simplicity seems most appropriate because it would be easier to demonstrate features and easier for beginners to wrap their heads around; while going for highest performance possible would likely add complexity that works against being manageable and minimal.

### Type-Error Timing
In the aspect of type errors it is crucial for the interpreter to verify type correctness of the program. Different interpreters/compilers approach this in different ways, some give a hard error before running, some give warnings and then run while some only catch it at runtime.

Rust catches all type related error at compilation and aborts, it checks for interactions between different types to ensure there's no undefined behaviour, while its ADT also being exhaustive means it raises an error when you don't make a case for each possible variant of an ADT (Klabnik, 2023). OCaml also catches general type errors at compile time as a hard failure, but its exhaustiveness check for ADT pattern matches only produces a warning rather than blocking compilation (Whitington, 2013). Haskell checks types at compile time like OCaml, but unlike OCaml it doesn't warn about incomplete pattern matching by default (Karachalias et al., 2015). At the end of the spectrum we have Python and Lua both are dynamically typed meaning the types of variables, functions, classes and symbols can change at runtime only raising errors when an operation is not valid (Python Software Foundation, n.d.; _Lua 5.4 Reference Manual_, n.d.).

For a minimal, beginner-oriented interpreter, catching errors early seems more appropriate, because it teaches the beginner how the system works from the start. While a loose type system might allow for more exploration, it also risks encouraging bad habits, and can be frustrating in practice. An error might not surface until the specific part of the code that contains it actually runs, which becomes especially painful as a project grows in size. I'm leaning closer to OCaml's model than Rust's full strictness, given the scope of a minimal interpreter.

### Syntax and Complexity
The issue of complexity can be viewed from the perspective of the person building the interpreter, versus the perspective of the person writing programs in the language once it exists.

JavaScript Runtimes is a nightmare of a system, so complex to implement that it also leaks to the programmers experience being a very difficult language to work with (V8, n.d.). Lisp on the other hand has a very simple implementation, so the job of the implementer is easy but the programmer has to use those little primitives  to build a more complete structure such as more complex conditionals or basic arithmetic operations making it hard for beginners to use (McCarthy, 1960). The Rust compiler take an enormous burden of making itself strict for the benefit of the programmer, things can usually only be done one way making the language unambiguous and straightforward to use (Klabnik, 2023).

I'm leaning toward Rust's approach, but not fully. The interpreter's implementation will take on more of the burden than Lisp's bare-minimum primitives, giving the programmer built-in conveniences rather than requiring them to construct everything themselves, without going as far as Rust's full compile-time enforcement, which is beyond the scope of a minimal educational interpreter.

### Feature Completeness

Conditionals: Rust uses `if` and `else` for conditionals and also provides exhaustive pattern matching with the `match` keyword (Klabnik, 2023). Python uses `if`/`elif`/`else` statements for conditionals, but unlike Rust it does not enforce exhaustiveness, so an `else` branch is optional (Python Software Foundation, n.d.). In OCaml, conditionals are expressions, not statements, so `if condition then expr1 else expr2` evaluates to one of two values, and both branches must return compatible types (Leroy et al., 2026). In Lisp, conditionals are special forms that evaluate only the branch whose condition succeeds, with most Lisp dialects treating only a single false value (`NIL` or `#f`) as false and everything else as true (Steele, 1990). In Haskell, conditionals are lazy expressions that evaluate the Boolean condition first and then evaluate only the selected branch, returning its value while leaving the other branch unevaluated (Marlow, 2010).

File I/O: File input and output (I/O) enables programs to read data from and write data to files stored on a system. Python provides the built-in `open()` function, which returns a file object and is commonly used with the `with` statement to ensure files are automatically closed after use (Python Software Foundation, n.d.). Rust performs file I/O through the `std::fs` module, where operations return a `Result` type that requires the programmer to explicitly handle potential errors such as missing files or permission failures (Klabnik, 2023). Node.js provides file I/O through the `fs` module, offering both synchronous and asynchronous operations to support different programming models and improve performance in I/O-bound applications (Node.js, n.d.). Similarly, Lua provides file I/O through its `io` library, where functions such as `io.open()` are used to open, read, write, and close files (_Lua 5.4 Reference Manual_, n.d.).

Lambda functions: Anonymous functions are based on the concept of lambda expressions from lambda calculus, which was first introduced into programming through Lisp (McCarthy, 1960). Python uses `lambda` to create small anonymous functions consisting of a single expression, which is automatically evaluated and returned when the function is called (Python Software Foundation, n.d.). Haskell, being based on lambda calculus, uses lambda expressions to create anonymous functions that are first-class values, evaluated lazily, and can be passed, returned, or partially applied like any other function (Marlow, 2010). Rust uses `|parameters| expression` syntax to create anonymous functions called closures, which can capture values from their surrounding environment and are evaluated only when called (Klabnik, 2023). Similarly, OCaml uses the `fun` keyword to create anonymous functions, which are also first-class values that can be passed, returned, or partially applied like named functions (Leroy et al., 2026).

map()/filter(): Higher-order functions such as `map()` and `filter()` provide a concise way to transform and select elements from a collection by applying a function to each element or retaining only those that satisfy a condition. Python provides the built-in `map()` and `filter()` functions, which take a function and an iterable and return a lazy iterator in Python 3 (Python Software Foundation, n.d.). Haskell provides `map` and `filter` as core list functions, and because of Haskell's lazy evaluation, their results are evaluated only when needed (Marlow, 2010). Rust provides similar functionality through the iterator API, where methods such as `.map()` and `.filter()` are chained together to transform data lazily (Klabnik, 2023). OCaml provides `List.map` and `List.filter` through its standard `List` module to perform equivalent operations on lists (Leroy et al., 2026). Building on these operations, Python and Haskell also provide comprehension syntax, allowing mapping and filtering to be expressed more concisely using list comprehensions. In contrast, Rust and OCaml do not provide native comprehension syntax, relying instead on iterator chains and standard library functions respectively to achieve the same result.

Comprehension: While `map` and `filter` define the underlying operations, comprehensions provide a more readable way of expressing those operations in languages that support them. As discussed above, Python and Haskell provide dedicated comprehension syntax for expressing `map` and `filter` operations more concisely (Python Software Foundation, n.d.; Marlow, 2010), while Rust and OCaml rely on iterator chains and standard library functions respectively to achieve the same result (Klabnik, 2023; Leroy et al., 2026).

Iteration: Iteration is the process of traversing the elements of a collection, allowing each element to be processed sequentially. Python supports iteration through the `for` loop, which relies on the iterator protocol, where an object defines `__iter__()` to return an iterator and `__next__()` to produce each successive element, and also provides generators through the `yield` keyword for lazy iteration (Python Software Foundation, n.d.). Lua supports iteration using numeric `for` loops as well as generic `for` loops  with iterator functions `ipairs()` for sequential numeric indices and `pairs()` for all key-value pairs in a table (_Lua 5.4 Reference Manual_, n.d.). Rust performs iteration through the `Iterator` trait, where the `next()` method produces one element at a time, making iterator operations lazy by default and enabling efficient method chaining (Klabnik, 2023). Unlike these languages, Haskell does not rely on imperative loop constructs for iteration; instead, iteration is typically expressed through recursion over lists, which may be finite or infinite due to Haskell's lazy evaluation (Marlow, 2010).







## Glossary


## References
Klabnik, Steve. _The Rust Programming Language, 2nd Edition_. With Carol Nichols. No Starch Press, 2023.

Python Software Foundation. (n.d.). _The Python Language Reference_. Python Documentation. Retrieved 1 August 2026, from [https://docs.python.org/3/reference/index.html](https://docs.python.org/3/reference/index.html)

Haskell. (2026). In _Wikipedia_. [https://en.wikipedia.org/w/index.php?title=Haskell&oldid=1364608049](https://en.wikipedia.org/w/index.php?title=Haskell&oldid=1364608049)

HaskellWiki. (n.d.). _Haskell lazy evaluation_. Retrieved 25 July 2026, from [https://wiki.haskell.org/Lazy_evaluation](https://wiki.haskell.org/Lazy_evaluation)

Lazy evaluation. (2026). In _Wikipedia_. [https://en.wikipedia.org/w/index.php?title=Lazy_evaluation&oldid=1353732111](https://en.wikipedia.org/w/index.php?title=Lazy_evaluation&oldid=1353732111)

Kein-Hong Man. (2006). _A no-frills introduction to Lua 5.1 VM instructions (Version 0.1)_. Internet Archive. [https://archive.org/details/a-no-frills-intro-to-lua-5.1-vm-instructions](https://archive.org/details/a-no-frills-intro-to-lua-5.1-vm-instructions)

Python (programming language). (2026). In _Wikipedia_. [https://en.wikipedia.org/w/index.php?title=Python_(programming_language)&oldid=1365917903](https://en.wikipedia.org/w/index.php?title=Python_\(programming_language\)&oldid=1365917903)

Whitington, J. (2013). _OCaml from the very beginning_. Coherent Press.

McCarthy, J. (1960). Recursive functions of symbolic expressions and their computation by machine, Part I. _Communications of the ACM_, _3_(4), 184–195. [https://doi.org/10.1145/367177.367199](https://doi.org/10.1145/367177.367199)

V8. (n.d.). _V8 Javascript Engine Documentation_. Retrieved 21 July 2026, from [https://v8.dev/docs](https://v8.dev/docs)
Karachalias, G., Schrijvers, T., Vytiniotis, D., & Jones, S. P. (2015). GADTs meet their match: Pattern-matching warnings that account for GADTs, guards, and laziness. _Proceedings of the 20th ACM SIGPLAN International Conference on Functional Programming_, 424–436. [https://doi.org/10.1145/2784731.2784748](https://doi.org/10.1145/2784731.2784748)

Leroy, X., Doligez, D., Frisch, A., Garrigue, J., Rémy, D., Sivaramakrishnan, K., & Vouillon, J. (2026). _The OCaml system_. [https://ocaml.org/manual/5.5/ocaml-5.5-refman.pdf](https://ocaml.org/manual/5.5/ocaml-5.5-refman.pdf)

Marlow, S. (Ed.). (2010). _Haskell 2010 Language Report_. [https://www.haskell.org/definition/haskell2010.pdf](https://www.haskell.org/definition/haskell2010.pdf)

Steele, G. L. (1990). _COMMON LISP: The language_ (2nd ed). Digital Press.

Node.js. (n.d.). _File system_. Retrieved 2 August 2026, from [https://nodejs.org/api/fs.html](https://nodejs.org/api/fs.html)


#interpreter #computer-science #programming