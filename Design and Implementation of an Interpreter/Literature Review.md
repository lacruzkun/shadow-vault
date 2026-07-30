This will be were i log all my research

- Introduction
- How each language implements execution
- Cross cutting challenges interpreters face
- design response
- reference

What is this review about, at a general level?
Why does this matter / what's the motivating problem?
What languages are you looking at, and why these ones?
What's the shape of the rest of the review?





Programming languages need a mechanism for executing source code.
• Interpreters are useful for immediate execution, education, and experimentation.
• Existing language systems are often too complex for a minimal educational implementation.
• The project addresses the design of a lightweight interpreter that remains clear and manageable.
• The goal is to support meaningful programming tasks without unnecessary architectural weight.

Aim: To design and implement a lightweight interpreter for a minimal programming language that is clear, structured, and suitable for educational and practical use. my objective

1. Design the syntax and structure of the minimal programming language.
2. Implement an interpreter capable of evaluating valid program statements.
3. Support essential programming constructs such as conditions, iteration, and function-like behavior.
4. Include selected features such as file I/O, lambda functions, map(), filter(), comprehensions, and chained conditionals.
5. Ensure the interpreter remains simple, readable, and manageable in implementation.

if there's any flaw anywhere highlight it and my surpervisor said i should focus more on the interpreter than the language because those are two separate stuff, i just want to know if the introduction is in line nothing more nothing less



## Introduction
This section is dedicated to reviewing how programming languages are implemented and executed. Existing implenmetations are too complex for what a minimal educational interpreter needs, since most are compiled or hybrid systems with architecture beyond what a lightweight interpreter requires. I will be looking into different execution implementations across different paradigms such as Rust, Haskell, Lisp, Python, Lua, OCaml and JavaScript runtimes. I'll look at their implementation approaches, common challenges they raise, before discussing how these inform my own design.

## How Each Language Implements Execution

Rust uses a strict compiler that enforces memory ownership at compile time and it produces native binary (Klabnik, 2023). what i think is useful here is it's strict rules, some might argue it's bad but i think when a system has one way of doing something it makes it easier for a beginner to wrap their head around it.

python uses an interpreter that is very flexible, infact too flexible for my liking and it pays that price with memory and execution speed. Variables are stored as a struct that has a pointer to the actual values doing so a variable can store arbitrary data and change data midway through the program (Python Software Foundation, n.d.), now this makes the fetching of variables slow but i think it also hinders a beginner learning because it forces the beginner to track the data type of a variable at different point. Also it's usually a bad practice to change the data type of a variable midway, so unless told a beginner is bound to be confused and make a mistake. But the thing i like about python is it's expressiveness and simplicity, the interpreter of python is so expressive that it can handle different paradigms (‘Python (Programming Language)’, 2026), and i think imperative and functional paradigms are the most essential ways of thinking a beginner should master to lay down the blocks for future learning.

The Glasgow Haskell Compiler (GHC) is both an interpreter and native-code compiler, it has features such as Glossary:  [[Generalized Algebraic Data Types]] (GADT) and also lazy evaluation (‘Haskell’, 2026). The whole concept of lazy evaluation is gotten from two intuitive ideas: Perform an evaluation step only when it is necessary; Never perform the same step twice (HaskellWiki, n.d.). most compilers and interpreters of functional programming languages use lazy evaluation, lazy evaluation is hard to implement with imperative features like exception handling and input/output because the order of operations becomes indeterminate (‘Lazy Evaluation’, 2026).

Lua like most interpreters uses a byte code VM. But Lua is very minimal using only around 38 opcodes for the VM (Kein-Hong Man, 2006). Its simplicity is what makes it a joy to use, although i'd rather it had a little bit of a simple type system at the least.

OCaml can be compiled into bytecode and then interpreted by ocamlrun or it can be compiled down to native code by the compiler ocamlopt (Whitington, 2013). Ocaml uses Glossary: Hindley-Milner style type inference which means you almost never write a type explicitly but it's still statically analysed and strictly enforced. The compiler/interpreter also warns when a match on an Glossary: Algebraic Data Type (ADT) is not exhaustive. 

Lisp is a broad term having many flavours, but i'm going to focus on the general idea every lisp implements. Lisp has a tiny set of primitives like lua, but unlike lua it builds everything else from those primitives. Lisp is a very mathematical model centric language and is used as a formalism for computation. 

## Cross cutting challenges interpreters face










## Glossary


## References
Klabnik, Steve. _The Rust Programming Language, 2nd Edition_. With Carol Nichols. No Starch Press, 2023.

Python Software Foundation. (n.d.). _The Python language reference: Data model_. Retrieved 25 July 2026, from [https://docs.python.org/3/reference/datamodel.html](https://docs.python.org/3/reference/datamodel.html)

Haskell. (2026). In _Wikipedia_. [https://en.wikipedia.org/w/index.php?title=Haskell&oldid=1364608049](https://en.wikipedia.org/w/index.php?title=Haskell&oldid=1364608049)

HaskellWiki. (n.d.). _Haskell lazy evaluation_. Retrieved 25 July 2026, from [https://wiki.haskell.org/Lazy_evaluation](https://wiki.haskell.org/Lazy_evaluation)

Lazy evaluation. (2026). In _Wikipedia_. [https://en.wikipedia.org/w/index.php?title=Lazy_evaluation&oldid=1353732111](https://en.wikipedia.org/w/index.php?title=Lazy_evaluation&oldid=1353732111)

Kein-Hong Man. (2006). _A no-frills introduction to Lua 5.1 VM instructions (Version 0.1)_. Internet Archive. [https://archive.org/details/a-no-frills-intro-to-lua-5.1-vm-instructions](https://archive.org/details/a-no-frills-intro-to-lua-5.1-vm-instructions)

Python (programming language). (2026). In _Wikipedia_. [https://en.wikipedia.org/w/index.php?title=Python_(programming_language)&oldid=1365917903](https://en.wikipedia.org/w/index.php?title=Python_\(programming_language\)&oldid=1365917903)

Whitington, J. (2013). _OCaml from the very beginning_. Coherent Press.


#interpreter #computer-science #programming