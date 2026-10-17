# CHAPTER FOUR: IMPLEMENTATION AND TESTING

## 4.1 Introduction

This chapter presents the implementation of the Licht interpreter and the tests used to verify it against the requirements set out in Chapter Three. Chapter Two compared how Rust, Python, Haskell, Lua, OCaml, Lisp, and JavaScript runtimes execute programs, and concluded that a minimal educational interpreter should favour implementation simplicity over execution speed. Chapter Three turned that conclusion into a pipeline of lexer, parser, type checker, and tree-walking evaluator, with 22 functional requirements and six non-functional requirements.

The chapter follows the incremental order of Section 3.1. Section 4.2 describes how each component is implemented. Section 4.3 sets out the testing strategy and the test cases for each stage. Section 4.4 traces the test cases back to the functional requirements. Section 4.5 discusses the outcome against the design decisions of Chapter Two, and Section 4.6 summarises the chapter.

## 4.2 Implementation

Licht is implemented as a standalone Rust application whose modules mirror the processing pipeline of Section 3.3: lexer, parser, type checker, and evaluator.

### 4.2.1 Implementation Environment

The interpreter is written in Rust and uses only the Rust standard library for its core components, as specified in Section 3.6. No lexer-generator, parser-generator, or parser-combinator crate is used, so the path from source text to tokens, AST nodes, type checking, and evaluation can be followed directly in the project's own code (Nystrom, 2021; Appel, 1997).

The code is organised into separate modules for the lexer, the parser, the AST definitions, the type checker, and the evaluator. Treating each stage boundary as a module boundary allows a stage to be built and tested before the next one depends on it (Graham, 1989), and it supports the extensibility requirement of Section 3.2.2.

### 4.2.2 Lexer

The lexer is a hand-written scanner that reads the source one character at a time and groups characters into the token categories listed in Section 3.4.1: keywords, identifiers, integer, float, string and Boolean literals, operators, punctuation, and delimiters. Whitespace and comments are recognised and discarded so that the parser never sees them (Ierusalimschy et al., 2025).

Several operators share a first character with a longer operator: `=` and `==`, `-` and `->`, `!` and `!=`, `<` and `<=`, `>` and `>=`. The scanner therefore looks at the next character before it chooses a token, so `==` is one comparison token and not two assignment tokens (Reis, 2011; Thain, 2023). Float literals require digits on both sides of the decimal point, which keeps `3.14` and `3` unambiguous. Invalid characters and malformed elements, such as an unterminated string literal, are reported by the lexer before parsing starts.

### 4.2.3 Parser and Abstract Syntax Tree

The parser is a hand-written recursive-descent parser that follows the grammar in Section 3.4.2 (Reis, 2011). Each level of the expression grammar is one function that calls the level below it: assignment, logical or, logical and, comparison, term, factor, unary, and primary. This gives the precedence order `!` > `&&` > `||` and the usual arithmetic precedence without special-case code.

Statement parsing handles `let` declarations, `if` and `else`, `for ... in` loops, `return`, and expression statements. A block may end with an expression whose value becomes the value of the block, which is how a function such as `add` returns `x + y` without a `return` keyword.

The parser builds AST nodes while it recognises the grammar. The nodes are Rust enums for declarations, statements, and expressions, with associated data for operators, operands, arguments, and bodies (Klabnik & Nichols, 2023). The expression `x + 2 * 3` therefore becomes an addition node whose right child is a multiplication node, which makes precedence explicit for the type checker and evaluator (Appel, 1997).

### 4.2.4 Type Checker

The type checker is a separate recursive pass over the AST that runs before any code is evaluated (Pierce, 2002). It keeps a type environment that maps visible variables and functions to types. For a `let` declaration it determines the type of the assigned expression and records it against the variable name, so `let total = add(3, 4);` records `total` as `int`. Function parameters and return types come from explicit annotations: the parameters are placed in a function-local environment, and the final value of the body is checked against the declared return type.

Operator and condition rules follow Section 3.4.4. Arithmetic operators require numeric operands, comparisons produce `bool`, `&&`, `||`, and `!` require `bool` operands, and an `if` condition must be `bool`. Ordinary calls are checked by comparing the number and types of the arguments with the declared parameters.

`map()` and `filter()` have dedicated rules instead of ordinary signatures. For `map()`, the function's parameter type must match the collection's element type, and the result is a collection of the function's return type. For `filter()`, the function must accept the element type and return `bool`, and the result keeps the original element type. Element types are tracked internally, so no generic list type appears in the language. When any check fails, the checker reports an error and the evaluator is not invoked.

### 4.2.5 Evaluator

The evaluator walks the checked AST recursively and computes the value of each node (Nystrom, 2021). A runtime environment maps names to values. Literals return their stored values, variable references are looked up in the environment, and binary expressions evaluate both operands before applying the operator. An `if` expression evaluates its condition and then only the selected branch.

A call evaluates its arguments and binds them to the parameters in a new environment before the function body runs. The last expression of the body is the implicit return value, and `return` exits early. Named functions are runtime values, so `is_even` and `double` can be passed to `map()` and `filter()`, which the evaluator implements as built-in operations. A `for i in range(0, 10)` loop obtains the values of the range and runs the body once for each value.

`read_file` and `write_file` call the Rust standard-library file operations. Any failure from those calls is converted into an interpreter-level runtime error (Klabnik & Nichols, 2023).

### 4.2.6 Error Reporting

Each stage reports its own category of error: lexical, syntax, type, or runtime. The category tells the programmer which stage rejected the program, and the message names the source construct involved so it can be located, as required by the error-clarity requirement in Section 3.2.2. Because type checking happens first, type errors never surface as runtime failures. The runtime category is left for failures that depend on the environment, such as a file that cannot be read.

## 4.3 Testing

Each stage of Licht is tested on its own before it is integrated with its neighbours, following the incremental method of Section 3.1 (Nystrom, 2021; Appel, 1997). Every test case has one expected outcome, either a specific output or a specific error category, and an observed result that differs from it counts as a failure.

### 4.3.1 Testing Strategy

Testing has five levels, matching the order in which the components were designed:

1. Lexer tests use valid and invalid source fragments, before the parser exists.
2. Parser tests use token sequences for expressions, declarations, functions, conditionals, and loops.
3. Type-checker tests use programs containing both valid and invalid type combinations.
4. Evaluator tests use programs that have already passed type checking.
5. Integration tests run complete Licht programs, including the sample from Section 3.5, through the whole pipeline.

The expected outputs below assume that `print` shows a list as `[2, 4, 6]` and that `range(a, b)` excludes `b`.

### 4.3.2 Lexer Test Cases

| ID | Input | Expected outcome |
| --- | --- | --- |
| L1 | `let x = 5;` | Five tokens: keyword `let`, identifier `x`, `=`, integer `5`, `;` |
| L2 | `a == b` and `a = b` | `==` is one comparison token; `=` is one assignment token |
| L3 | `fn f() -> int` | `->` is one return-arrow token |
| L4 | `3.14` | One float literal |
| L5 | `x != y` and `!z` | `!=` is one token; a lone `!` is the logical-not token |
| L6 | `a && b` | `&&` is one logical-and token |
| L7 | `"Licht"` | One string literal |
| L8 | `let x = 1; // note` | The comment is discarded; no token follows `;` |
| L9 | `let x = @;` | Lexical error: invalid character |
| L10 | `"abc` | Lexical error: unterminated string literal |

### 4.3.3 Parser Test Cases

| ID | Input | Expected outcome |
| --- | --- | --- |
| P1 | `x + 2 * 3` | Addition node whose right child is a multiplication node |
| P2 | `a \|\| b && c` | Parsed as `a \|\| (b && c)` |
| P3 | `!a && b` | Parsed as `(!a) && b` |
| P4 | `let total = add(3, 4);` | Let node holding a call expression with two arguments |
| P5 | `fn add(x: int, y: int) -> int { x + y }` | Function declaration with two parameters, return type `int`, and a body ending in an expression |
| P6 | `if total > 5 { print("big"); } else { print("small"); }` | If node with a condition and both branches |
| P7 | `for i in range(0, 10) { print(i); }` | For node with loop variable `i`, a call as the iterable, and a body |
| P8 | `let numbers = [1, 2, 3];` | List literal with three elements |
| P9 | `let = 5;` | Syntax error: identifier expected |
| P10 | `if x > 1 print(x);` | Syntax error: block expected |
| P11 | `let x = 1` | Syntax error: `;` expected |

### 4.3.4 Type Checker Test Cases

Cases T1 to T13 assume the functions `add(x: int, y: int) -> int`, `is_even(n: int) -> bool`, and `double(n: int) -> int` from Section 3.5, and `numbers` as a collection of integers.

| ID | Input | Expected outcome |
| --- | --- | --- |
| T1 | `let total = add(3, 4);` | Accepted; `total` is inferred as `int` |
| T2 | `let can_run = is_ready && pi > 3.0;` | Accepted; `can_run` is inferred as `bool` |
| T3 | `add(3, "four")` | Type error: second argument expected `int`, found `string` |
| T4 | `add(3)` | Type error: wrong number of arguments |
| T5 | `if 5 { print("x"); }` | Type error: condition must be `bool` |
| T6 | `let x = true + 1;` | Type error: arithmetic operands must be numeric |
| T7 | `let b = 1 && true;` | Type error: `&&` requires `bool` operands |
| T8 | `fn f(n: int) -> bool { n + 1 }` | Type error: body has type `int`, declared `bool` |
| T9 | `filter(is_even, numbers)` | Accepted; result is a collection of `int` |
| T10 | `map(double, numbers)` | Accepted; result is a collection of `int` |
| T11 | `map(is_even, ["a", "b"])` | Type error: function parameter type does not match the element type |
| T12 | `filter(double, numbers)` | Type error: filter function must return `bool` |
| T13 | `print("hello"); add(3, "four");` | Type error reported; nothing is printed, because the evaluator is not invoked |

### 4.3.5 Evaluator Test Cases

| ID | Input | Expected outcome |
| --- | --- | --- |
| E1 | `add(3, 4)` | `7` |
| E2 | `abs(-5)` | `5`, through the early `return` |
| E3 | `let x = 1; x + 2 * 3` | `7` |
| E4 | `!true`, `true && false`, `true \|\| false` | `false`, `false`, `true` |
| E5 | The `if total > 5` program with `total` equal to 7 | Prints `big` only |
| E6 | `for i in range(0, 3) { print(i); }` | Prints `0`, `1`, `2`, one per line |
| E7 | `filter(is_even, [1, 2, 3, 4, 5, 6])` | `[2, 4, 6]` |
| E8 | `map(double, [1, 2, 3, 4, 5, 6])` | `[2, 4, 6, 8, 10, 12]` |
| E9 | `write_file("out.txt", read_file("data.txt"))`, with `data.txt` present | `out.txt` is created with the same content as `data.txt` |
| E10 | `read_file("missing.txt")` | Runtime error: file cannot be read |

### 4.3.6 Integration Test Cases

| ID | Input | Expected outcome |
| --- | --- | --- |
| I1 | The sample program of Section 3.5, with `data.txt` present | Passes every stage; prints `big`, the numbers `0` to `9`, `[2, 4, 6]`, and `[2, 4, 6, 8, 10, 12]`; `out.txt` matches `data.txt` |
| I2 | The sample program with `add(3, "4")` in place of `add(3, 4)` | Type error; the program prints nothing |
| I3 | A source file containing an invalid character | Lexical error; parsing does not start |
| I4 | A source file with a missing `;` | Syntax error; type checking does not start |

## 4.4 Verification Against Requirements

Every functional requirement in Section 3.2.1 is covered by at least one test case from Section 4.3. The table groups the requirements (FR1 to FR22, in the order of Section 3.2.1) and lists the cases that exercise them.

| Requirements | Capability | Test cases |
| --- | --- | --- |
| FR1, FR22 | Accept a Licht program and produce the expected output | I1 |
| FR2, FR3 | Lexical analysis and token recognition | L1 to L8 |
| FR4 | Parsing and AST construction | P1 to P8 |
| FR5, FR6 | `let` declarations with inferred types | P4, T1, T2 |
| FR7 | Explicit parameter and return annotations | P5, T3, T4, T8 |
| FR8 | Integer, float, Boolean, string, and collection values | L1, L4, L7, P8, T2 |
| FR9 | Arithmetic, comparison, and logical operations | P1 to P3, T6, T7, E3, E4 |
| FR10 | `if` and `else` | P6, T5, E5 |
| FR11, FR12 | Named functions as first-class values | P5, E1, E2 |
| FR13, FR14 | `map()` and `filter()` | T9 to T12, E7, E8 |
| FR15 | Range-based iteration | P7, E6 |
| FR16, FR17 | File input and output | E9, E10 |
| FR18, FR19 | Separate type-checking pass; no evaluation after a type error | T1 to T13, I2 |
| FR20 | Tree-walking evaluation | E1 to E10 |
| FR21 | Lexical, syntax, type, and runtime errors reported | L9, L10, P9 to P11, T3 to T8, E10, I3, I4 |

The non-functional requirements of Section 3.2.2 are assessed by inspection of the design and code rather than by test cases:

- **Simplicity, manageability, and portability:** the core uses only the Rust standard library, with no external crate and one module per stage (Section 4.2.1).
- **Predictable type behaviour:** cases T3 to T13 show that type errors are found before evaluation begins.
- **Usability:** the sample program in Section 3.5 uses conditionals, loops, `map()`, `filter()`, and file input and output directly, without building them from lower-level primitives.
- **Error clarity:** every error belongs to one of four named categories and names the construct involved (Section 4.2.6).
- **Extensibility:** lexing, parsing, type checking, and evaluation are separate modules, so a new feature touches only the stages it needs.

## 4.5 Discussion

The implementation follows the design decisions of Chapter Two, and the test cases show where those decisions help and where they cost.

**Simplicity over speed.** The tree-walking evaluator keeps the execution path short: source text becomes tokens, tokens become a tree, the tree is checked, and the checked tree is evaluated (Nystrom, 2021). Chapter Two accepted that this carries more execution overhead than the bytecode virtual machine of Lua or the tiered compilation of V8 (Smith & Nair, 2005; Ierusalimschy et al., 2025; V8, 2023). The tests in this chapter check behaviour only, and execution speed is not measured.

**Type errors before evaluation.** Cases T13 and I2 show the behaviour argued for in Chapter Two: a program with a type error produces no output, because the evaluator never starts (Pierce, 2002). This confirms the ordering of the stages, not a teaching benefit. As Chapter Two stated, early checking is a design rationale and not an empirical result, and the effect of Licht's error messages on novices (Nagura & Kondo, 2024) was not tested with learners.

**A lighter type system.** Explicit signatures with inferred local variables (T1, T2, T8) give the predictability of Rust's explicit interfaces without ownership and borrowing (Klabnik & Nichols, 2023). Licht's inference is limited to deriving a local variable's type from its assigned expression, so it is much narrower than the Hindley–Milner inference used by OCaml (Leroy et al., 2026c).

**Scope limits.** `map()` and `filter()` accept named functions only (T9 to T12), so even a one-off transformation needs a function declared beforehand, where Python and Rust allow an inline function (Python Software Foundation, n.d.; Klabnik & Nichols, 2023). Collection types cannot be written in function annotations (Section 3.3.3), so a user-defined function cannot take or return a list. These are the costs of excluding lambdas, comprehensions, and a general generic type, a scope decision made deliberately in Chapter Two.

**Limits of the testing.** The test cases are written by hand and cover every requirement, but they are not exhaustive. They do not include randomly generated programs, stress tests, or a study with novice users, and those remain open for future work.

## 4.6 Chapter Summary

This chapter described the implementation of the Licht interpreter as four modules, lexer, parser, type checker, and evaluator, written in Rust with only the standard library. It then set out 48 test cases: 10 for the lexer, 11 for the parser, 13 for the type checker, 10 for the evaluator, and 4 integration cases.

The test cases cover all 22 functional requirements of Chapter Three, and the non-functional requirements are addressed by the module structure and the four error categories. The discussion showed that the design gives the early type-error behaviour and the short execution path argued for in Chapter Two, at the cost of execution speed and of higher-order operations that accept named functions only.

## References

Appel, A. W. (1997). *Modern compiler implementation in ML*. Cambridge University Press. https://doi.org/10.1017/CBO9780511811449

Graham, D. (1989). Incremental development: Review of nonmonolithic life-cycle development models. *Information and Software Technology, 31*(1), 7–20. https://doi.org/10.1016/0950-5849(89)90049-9

Ierusalimschy, R., de Figueiredo, L. H., & Celes, W. (2025). *Lua 5.4 reference manual*. Lua.org. https://www.lua.org/manual/5.4/manual.html

Klabnik, S., & Nichols, C. (2023). *The Rust programming language* (2nd ed.). No Starch Press. https://nostarch.com/rust-programming-language-2nd-edition

Leroy, X., Doligez, D., Frisch, A., Garrigue, J., Rémy, D., Sivaramakrishnan, K., & Vouillon, J. (2026c). *The OCaml language*. OCaml Documentation. https://ocaml.org/manual/5.5/expr.html

Nagura, M., & Kondo, R. (2024). A learning support method for novice programmers based on categorization of compilation error messages. *Journal of Software and Systems, 41*(2), 2_3–2_18. https://doi.org/10.11309/jssst.41.2_3

Nystrom, R. (2021). *Crafting interpreters*. https://craftinginterpreters.com/

Pierce, B. C. (2002). *Types and programming languages*. MIT Press. https://mitpress.mit.edu/9780262162098/types-and-programming-languages/

Python Software Foundation. (n.d.). *The Python language reference*. Python Documentation. https://docs.python.org/3/reference/index.html

Reis, A. J. D. (2011). Recursive-descent parsing. In *Compiler construction using Java, JavaCC, and Yacc*. Wiley. https://doi.org/10.1002/9781118112762.ch9

Smith, J. E., & Nair, R. (2005). The architecture of virtual machines. *Computer, 38*(5), 32–38. https://doi.org/10.1109/MC.2005.173

Thain, D. (2023). *Introduction to compilers*. University of Notre Dame. https://www3.nd.edu/~dthain/compilerbook/compilerbook.pdf

V8. (2023, December 5). *Maglev: V8's fastest optimizing JIT*. https://v8.dev/blog/maglev
