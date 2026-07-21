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
This section is dedicated to reviewing how programming languages are implemented and executed. Existing implemetations are too complex for what a minimal educational interpreter needs, since most are compiled or hybrid systems with architecture beyond what a lightweight interpreter requires. I will be looking into different execution implementations across different paradigms such as Rust, Haskell, Lisp, Python, Lua, OCaml and JavaScript runtimes. I'll look at their implementation approaches, common challenges they raise, before discussing how these inform my own design.

## How Each Language Implements Execution
Klabnik, Steve. _The Rust Programming Language, 2nd Edition_. With Carol Nichols. No Starch Press, 2023.

Klabnik, S. (with Nichols, C.). (2023). _The Rust Programming Language, 2nd Edition_. No Starch Press.

#interpreter #computer-science #programming