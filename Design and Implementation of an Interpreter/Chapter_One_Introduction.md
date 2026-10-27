# CHAPTER ONE: INTRODUCTION

## 1.1 Background of the Study

Programming languages are the means by which people instruct computers, and every language reaches its users through an implementation: a program that either translates source code into another form or executes it directly. Existing implementations range from direct tree-walking interpreters to bytecode virtual machines, native-code compilers, and multi-tier just-in-time (JIT) systems (Nystrom, 2021; Smith & Nair, 2005). The choice of strategy determines how quickly programs run, how much machinery the implementer must build, and when different kinds of errors are detected.

The timing of error detection is one of the clearest differences between languages. A static type system classifies expressions by the kinds of values they compute so that certain erroneous programs are rejected before they run, whereas dynamically typed systems defer many checks until the relevant operation is executed (Pierce, 2002). Rust rejects programs that violate its type, ownership, and borrowing rules at compile time (Klabnik & Nichols, 2023), while Python lets a name be bound to values of different types during execution, so some type-related errors appear only when the operation is run (Python Software Foundation, n.d.).

These design choices affect people who are learning to program. Research on novice programmers reports that syntax is an important barrier and that the intuitiveness of keywords and constructs can be studied empirically instead of assumed (Lappi et al., 2023). It also reports that compiler diagnostics can create learning difficulties when one error produces several confusing messages, while structured categorisation of messages can improve students' ability to address them (Nagura & Kondo, 2024). Languages that enforce strict rules, such as Rust, bring concepts such as ownership and borrowing that learners must understand before they can write some classes of programs (Klabnik & Nichols, 2023).

Interpreters are also studied as educational objects in their own right. Nystrom (2021) develops an interpreter in stages, from scanning and parsing to evaluation, so that the relationship between syntax, evaluation rules, and program behaviour can be examined at each step. A tree-walking design is attractive in this setting because the implementation can stay direct enough to study and extend while still supporting a complete small language (Nystrom, 2021).

This project is motivated by the possibility of combining these ideas in one small, documented system. It designs a lightweight, beginner-oriented programming language called Licht, the German word for “light,” and builds a reference interpreter for it in Rust. The language is meant to detect type errors before a program runs, to provide common programming constructs directly, and to stay small enough for its whole implementation to be read and understood.

## 1.2 Statement of the Problem

Within the literature reviewed in Chapter Two, the elements that this project brings together are documented separately and usually inside larger systems or systems designed around different goals. Production-grade runtimes such as V8 and compilers such as Rust's achieve performance or safety through substantial machinery (V8, 2017, 2021, 2023; Klabnik & Nichols, 2023). Languages differ in when they check types and in how much type information the programmer must write (Pierce, 2002; Python Software Foundation, n.d.). McCarthy's original Lisp shows that a compact kernel can support a complete evaluator, but it leaves users to build higher-level facilities themselves (McCarthy, 1960). Studies of novice programmers examine syntax and compiler diagnostics (Lappi et al., 2023; Nagura & Kondo, 2024), and educational texts show how an interpreter can be built in stages (Nystrom, 2021).

The sources reviewed do not describe a single small reference interpreter that integrates early static type checking with a hybrid scheme of explicit function signatures and inferred local variable types, a tree-walking execution model, common constructs provided directly by the language, and a deliberately limited feature set, under an explicit set of requirements. They also do not report how such a combination can be designed, implemented, and verified stage by stage.

The problem addressed by this project is therefore the lack, within the reviewed literature, of a minimal and documented reference interpreter of this kind, together with an account of how its requirements are specified and verified.

## 1.3 Aim and Objectives of the Study

### 1.3.1 Aim

The aim of this study is to design and implement Licht, a small beginner-oriented programming language with a reference interpreter written in Rust that reports type errors before a program runs, and to document and verify the interpreter so that it can be read and studied as a worked example of how a programming language is built.

### 1.3.2 Objectives

The objectives of the study are:

1. To review how existing languages and runtimes (Rust, Python, Haskell, Lisp, Lua, OCaml, and JavaScript) are implemented, and to identify the trade-offs that bear on the design of a minimal educational interpreter.
2. To specify the functional and non-functional requirements of Licht and to define the scope of the project.
3. To design the architecture of the interpreter as a pipeline of separate components (lexer, parser, abstract syntax tree, type checker, and evaluator) that can each be understood on its own, and to define the grammar and syntax of the language.
4. To implement the interpreter in Rust using only the standard library, with a hand-written lexer and parser and no external lexer or parser libraries, so that the whole implementation can be read without third-party abstractions.
5. To implement a separate static type-checking pass in which function parameters and return types are explicitly annotated and local variable types are inferred, so that type errors are reported with their category and source location before evaluation starts.
6. To implement the selected language features: conditional expressions, named functions as first-class values, `map()` and `filter()`, range-based iteration, and file input and output, together with the reporting of lexical, syntax, type, and runtime errors.
7. To test each stage independently and then the integrated interpreter, and to verify the implementation against the specified requirements, recording which tests exercise each requirement.
8. To document the requirements, design, implementation, and tests so that the interpreter can serve as a worked example for students of language implementation.

## 1.4 Research Questions

The study is guided by the following questions:

1. Which execution, typing, and feature-set trade-offs found in existing language implementations are relevant to a minimal educational interpreter?
2. Can a tree-walking interpreter with a separate static type-checking pass, explicit function signatures, and inferred local variable types be implemented in Rust using only its standard library?
3. Can the selected feature set, including `map()` and `filter()` over named functions, be supported without lambda functions, comprehensions, or complex iterator abstractions?
4. Can the resulting interpreter be shown, through stage-by-stage testing, to satisfy the specified requirements?

## 1.5 Significance of the Study

The study is intended to benefit the following groups. The benefits to students and educators of language implementation follow directly from what the project delivers. The benefits to novice programmers are design intentions that this project does not measure.

**Students and educators of language implementation.** Interpreters are usually studied through large systems or through texts that build them in stages (Nystrom, 2021). Licht offers a complete interpreter that is small enough to be read from start to finish and is divided into a lexer, parser, abstract syntax tree, type checker, and evaluator that can each be studied on its own. Its requirements, architecture, grammar, and tests are documented, and each functional requirement is linked to the tests that exercise it, so a reader can follow a feature from specification to design to verification. Educators can use it as a worked example of a tree-walking interpreter with a separate type-checking pass, and students can extend it one stage at a time.

**Novice programmers.** Research reports that syntax and compiler diagnostics are barriers for novices (Lappi et al., 2023; Nagura & Kondo, 2024). Licht is designed so that a program containing a type error is rejected before it runs, with the category and source location of the error, and so that common constructs are built into the language instead of being assembled from primitives. This is intended to give a learner feedback at a clearly defined stage and a language with few concepts to learn. The project does not test the effect of these features on learners, so this benefit remains a design intention.

**Researchers and language implementers.** The study provides a documented, minimal example of hybrid typing, in which function signatures are explicit and local variable types are inferred, and of higher-order operations (`map()` and `filter()`) supported through dedicated checking rules instead of a general generic type system. Because the stages are separate, the interpreter can serve as a base for adding or evaluating further language features.

**The developer.** The project gives hands-on experience in language design, interpreter construction, static type checking, and systems programming in Rust.

**The academic record.** The study addresses the gap identified in Chapter Two by providing a documented specification, implementation, and verification of a small reference interpreter.

## 1.6 Scope of the Study

The scope of the study is the design, implementation, and testing of a reference interpreter for Licht, a lightweight, beginner-oriented language. The scope is limited so that the interpreter stays small and understandable. The exclusions below are deliberate scope controls and are not claims that the omitted features are poor language features.

**In scope:**

- A hand-written lexer, parser, type checker, and tree-walking evaluator, written in Rust with its standard library, as a standalone application that runs Licht source files.
- The values and operations required by the functional requirements: integers, floats, Booleans, strings, and collections; arithmetic, comparison, and logical operators; `let` declarations; `if` and `else`; named functions; range-based iteration; `map()` and `filter()`; and file input and output.
- Static type checking with explicit function signatures and inferred local variable types, with type errors reported before evaluation.
- Reporting of lexical, syntax, type, and runtime errors.
- Stage-by-stage testing of the lexer, parser, type checker, and evaluator, and integration testing of the complete interpreter.

**Out of scope:**

- Lambda functions, closures, list comprehensions, and complex iterator abstractions.
- A general list type in function annotations, and a general generic or parametric type system.
- Bytecode generation, a virtual machine, native-code compilation, and adaptive optimisation.
- Ownership, borrowing, and lifetime checking of the kind found in Rust.
- Maximum execution performance, which is ranked below simplicity.
- External lexer, parser, or parser-generator libraries for the core components.

## 1.7 Limitations of the Study

The following limitations apply to the study and to the conclusions that can be drawn from it.

1. **Limited language.** Licht omits lambda functions, closures, comprehensions, complex iterator abstractions, and a general generic type system. Programs that depend on these features cannot be written in it. For example, `map()` and `filter()` accept named functions only, so even a one-off transformation needs a function declared beforehand.
2. **No emphasis on performance.** A tree-walking evaluator carries more execution overhead than a bytecode virtual machine or an optimising compiler (Smith & Nair, 2005; Nystrom, 2021). The project does not optimise the interpreter and does not measure its execution speed.
3. **Testing is not exhaustive.** The test cases are written by hand and cover every requirement, but they do not include randomly generated programs or stress tests. Verification therefore shows that the specified behaviour is met on the cases tested and not that the interpreter is free of faults.
4. **No study with learners.** The educational benefits described in this chapter are design intentions. The project does not test the effect of early type checking, the error messages, or the language syntax on novice programmers, and early checking is treated as a design rationale and not as an empirical result (Nagura & Kondo, 2024).
5. **Limited literature.** The review covers seven languages and runtimes and the sources available for them. It does not survey every educational language or every implementation strategy, so the gap is stated relative to the literature reviewed.
6. **Reference implementation only.** Licht has a small set of built-in operations and a minimal error-reporting scheme. It is a reference interpreter for study and not a production language with a full standard library or tooling.
7. **Project constraints.** The study is an undergraduate project carried out by one developer within a limited period, which bounds the number of features that can be designed, implemented, and tested.

## 1.8 Overview of the Methodology

The study follows the Iterative and Incremental Development (IID) methodology (Graham, 1989). In this methodology the system is built as a series of increments, and each increment is refined through repeated cycles of implementation, testing, and correction before the next one begins. In this project each increment is one component of the interpreter, built in the order lexer, parser, abstract syntax tree, type checker, and evaluator, and each is tested independently before it is integrated with the stages around it. Chapter Three describes the methodology, the requirements, and the design in detail.

## 1.9 Definition of Terms

**Interpreter.** A program that executes another program, here by traversing its abstract syntax tree directly instead of first translating it into bytecode or native code (Nystrom, 2021).

**Lexer (lexical analyser).** The component that reads source text and groups characters into tokens such as keywords, identifiers, literals, operators, and punctuation (Nystrom, 2021).

**Parser.** The component that checks that a token sequence conforms to the grammar of the language and builds the abstract syntax tree (Nystrom, 2021; Appel, 1997).

**Abstract Syntax Tree (AST).** A tree representation of the structure of a program that is independent of its original formatting and is used by the type checker and evaluator (Appel, 1997).

**Type checker.** The component that determines whether expressions and statements are type-correct without executing the program (Pierce, 2002).

**Static typing.** A discipline in which types are checked before a program runs, so that certain erroneous programs are rejected before execution (Pierce, 2002).

**Type inference.** The determination of the type of an expression from how it is used or from the expression assigned to it, so that the programmer does not have to write the type explicitly.

**Tree-walking evaluation.** Execution of a program by recursively traversing its abstract syntax tree and computing the value of each node (Nystrom, 2021).

**First-class function.** A function that can be stored in a variable, passed as an argument, and used like any other value (Pierce, 2002).

**Higher-order function.** A function that takes another function as an argument or returns one, such as `map()` and `filter()`.

## 1.10 Organisation of the Report

This report has five chapters. Chapter One introduces the study, including its background, problem, aim and objectives, significance, scope, and limitations. Chapter Two reviews how programming languages are implemented and executed, compares the common challenges that implementations face, and identifies the gap that the study addresses. Chapter Three presents the methodology, the system requirements and scope, the architecture and component design, the proposed grammar and syntax, and the tools and technologies. Chapter Four describes the implementation of each component and the testing of the interpreter against the requirements, and discusses the results. Chapter Five presents the summary, conclusions, and recommendations.

## References

Appel, A. W. (1997). *Modern compiler implementation in ML*. Cambridge University Press. https://doi.org/10.1017/CBO9780511811449

Graham, D. (1989). Incremental development: Review of nonmonolithic life-cycle development models. *Information and Software Technology, 31*(1), 7–20. https://doi.org/10.1016/0950-5849(89)90049-9

Klabnik, S., & Nichols, C. (2023). *The Rust programming language* (2nd ed.). No Starch Press. https://nostarch.com/rust-programming-language-2nd-edition

Lappi, V., Tirronen, V., & Itkonen, J. (2023). A replication study on the intuitiveness of programming language syntax. *Software Quality Journal, 31*, 1211–1240. https://doi.org/10.1007/s11219-023-09631-7

McCarthy, J. (1960). Recursive functions of symbolic expressions and their computation by machine, Part I. *Communications of the ACM, 3*(4), 184–195. https://doi.org/10.1145/367177.367199

Nagura, M., & Kondo, R. (2024). A learning support method for novice programmers based on categorization of compilation error messages. *Journal of Software and Systems, 41*(2), 2_3–2_18. https://doi.org/10.11309/jssst.41.2_3

Nystrom, R. (2021). *Crafting interpreters*. https://craftinginterpreters.com/

Pierce, B. C. (2002). *Types and programming languages*. MIT Press. https://mitpress.mit.edu/9780262162098/types-and-programming-languages/

Python Software Foundation. (n.d.). *The Python language reference*. Python Documentation. https://docs.python.org/3/reference/index.html

Smith, J. E., & Nair, R. (2005). The architecture of virtual machines. *Computer, 38*(5), 32–38. https://doi.org/10.1109/MC.2005.173

V8. (2017, May 15). *Launching Ignition and TurboFan*. https://v8.dev/blog/launching-ignition-and-turbofan

V8. (2021, April 27). *Sparkplug — a non-optimizing JavaScript compiler*. https://v8.dev/blog/sparkplug

V8. (2023, December 5). *Maglev: V8's fastest optimizing JIT*. https://v8.dev/blog/maglev
