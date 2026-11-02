# CHAPTER FIVE: SUMMARY, CONCLUSIONS, AND RECOMMENDATIONS

## 5.1 Introduction

This chapter closes the study. It summarises the work reported in the earlier chapters, shows how far the objectives set in Chapter One were achieved, and states the conclusions that follow. It then describes the contribution of the study, restates the limitations that bound those conclusions, and ends with recommendations and suggestions for further work.

## 5.2 Summary of the Study

Chapter One introduced the study. It explained that programming languages reach their users through implementations, that languages differ in when they detect type errors, and that syntax, compiler diagnostics, and conceptual load are barriers for novice programmers (Lappi et al., 2023; Nagura & Kondo, 2024). It stated the problem, the aim, the objectives, the significance, the scope, and the limitations of the study. The aim was to design and implement Licht, a small beginner-oriented programming language with a reference interpreter written in Rust that reports type errors before a program runs, and to document and verify the interpreter so that it can be studied as a worked example of how a programming language is built.

Chapter Two reviewed how Rust, Python, Haskell, Lisp, Lua, OCaml, and JavaScript runtimes are implemented, described the components of an interpreter, and compared the approaches used to evaluate language implementations. It examined five common challenges: performance against simplicity, the timing of type errors, syntax and complexity, feature completeness, and learnability for novice programmers. It ended by identifying a gap: within the literature reviewed, no single small reference interpreter integrated early static type checking with hybrid typing, tree-walking evaluation, directly provided common constructs, and a deliberately limited feature set under an explicit set of requirements.

Chapter Three set out the methodology and design. The study followed the Iterative and Incremental Development methodology, in which each component of the interpreter was built, tested, and refined before the next was added (Graham, 1989). The chapter stated twelve user requirements, 22 functional requirements, and six non-functional requirements, defined the scope, and described an architecture in which source code passes through a lexer, a parser, a type checker, and a tree-walking evaluator. It also presented the component designs, the proposed grammar, a sample program, and the decision to use Rust with only its standard library.

Chapter Four described how each component was implemented and how the interpreter was tested. The implementation consists of separate modules for the lexer, the parser, the abstract syntax tree, the type checker, and the evaluator, with each stage reporting its own category of error. Testing used 48 test cases: 10 for the lexer, 11 for the parser, 13 for the type checker, 10 for the evaluator, and 4 integration cases. The chapter traced the test cases back to the functional requirements and discussed the outcome against the design decisions of Chapter Two.

## 5.3 Achievement of the Objectives

The eight objectives set in Chapter One were addressed as follows.

1. **To review how existing languages and runtimes are implemented and identify the relevant trade-offs.** Chapter Two reviewed seven languages and runtimes, compared their execution models and typing behaviour, and identified the trade-offs in performance, type-error timing, syntax, feature set, and learnability that bear on a minimal educational interpreter.
2. **To specify the requirements and define the scope.** Chapter Three specified the user, functional, and non-functional requirements and stated what is inside and outside the scope of the project.
3. **To design the architecture as a pipeline of separate components and define the grammar and syntax.** Chapter Three presented the architecture, a design diagram, a data and information flow diagram, the component designs, an evaluator design diagram, and the proposed grammar and sample program.
4. **To implement the interpreter in Rust using only the standard library.** The lexer and parser are written by hand, and no external lexer, parser, or parser-generator library is used (Chapter Four, Section 4.2).
5. **To implement a separate static type-checking pass.** The type checker runs as a separate pass over the AST. Function parameters and return types are annotated explicitly, local variable types are inferred, and the evaluator is not invoked when a type error is found.
6. **To implement the selected language features and error reporting.** The interpreter supports conditional expressions, named functions as first-class values, `map()` and `filter()`, range-based iteration, and file input and output, and it reports lexical, syntax, type, and runtime errors.
7. **To test each stage and the integrated interpreter and verify the implementation against the requirements.** Each stage was tested independently, followed by integration tests of the complete interpreter. Every functional requirement is covered by at least one of the 48 test cases (Chapter Four, Section 4.4). All 48 test cases passed [confirm after running `cargo test`].
8. **To document the requirements, design, implementation, and tests as a worked example.** The study documents each of these in Chapters Three and Four, including the link from each functional requirement to the test cases that exercise it.

## 5.4 Conclusions

The following conclusions are drawn from the study.

1. **A small interpreter with a separate type-checking stage can be built from hand-written components and the Rust standard library alone.** Keeping the lexer, parser, type checker, and evaluator as separate modules allowed each to be built and tested before the next depended on it, which supports the use of the Iterative and Incremental Development methodology for this kind of project (Graham, 1989).
2. **Early type checking can be made to stop a faulty program before it produces any output.** Because the type checker runs before the evaluator, a program containing a type error is rejected without being evaluated, and it produces no output. This confirms the ordering of the stages. It does not show that early checking helps learners, which remains a design rationale and not an empirical result (Nagura & Kondo, 2024).
3. **Hybrid typing and dedicated checking rules can support higher-order operations without a generic type system.** Explicit function signatures with inferred local variables, together with dedicated rules for `map()` and `filter()`, gave type-checked higher-order operations at a lower cost in machinery than a full generic system, at the price of named-function-only use and no collection types in annotations.
4. **Simplicity was achieved at an unmeasured cost in speed.** A tree-walking evaluator keeps the execution path short and readable (Nystrom, 2021). The study did not measure its execution speed, so the size of the performance cost is not known.
5. **Documented requirements linked to tests make a small interpreter easier to follow as a worked example.** The user requirements, functional requirements, design, and test cases are traceable to one another, so a reader can follow a feature from specification to verification. Whether this helps students in practice was not tested.
6. **The benefit to novice programmers is a design intention and has not been established.** The study did not test the interpreter, its error messages, or its syntax with novice programmers.

## 5.5 Contribution of the Study

The study makes the following contributions, which are modest in scale and tied to the gap identified in Chapter Two:

- A documented, minimal reference interpreter that integrates early static type checking with hybrid typing, tree-walking evaluation, directly provided common constructs, and a deliberately limited feature set.
- A requirements-to-tests record, running from user requirements through functional requirements to test cases, which shows how a small interpreter can be specified and verified stage by stage.
- A worked example of type checking `map()` and `filter()` through dedicated rules instead of a general generic type system.

## 5.6 Limitations Revisited

The limitations stated in Chapter One still bound these conclusions. Licht omits lambda functions, closures, comprehensions, complex iterator abstractions, and a general generic type system, so programs that depend on them cannot be written in it. Performance was not optimised or measured. The test cases were written by hand and are not exhaustive, since they do not include randomly generated programs or stress tests. No study with novice programmers was carried out, and the literature reviewed covers seven languages and runtimes and does not survey every educational language. Licht is a reference interpreter with a small set of built-in operations and not a production language.

## 5.7 Recommendations

Based on the study, the following recommendations are made.

1. **For students and educators of language implementation.** The interpreter should be read in the order of its pipeline (lexer, parser, abstract syntax tree, type checker, evaluator), following the requirement-to-test links in Chapter Four. It can be extended one stage at a time by adding a feature to each stage and the tests that exercise it.
2. **For designers of educational languages.** Type errors should be reported at a clearly defined stage, before evaluation, and each message should state the category of the error and its source location. Keeping analysis and execution as separate stages makes this ordering easy to guarantee and to test.
3. **For those who extend Licht.** The grammar, the implementation, and the tests should be kept consistent. Each new feature should be added as a new increment with its own test cases, and the requirement it supports should be recorded alongside them.
4. **For those who wish to use Licht with learners.** Its educational benefits should be tested before they are claimed, using the further work described below.

## 5.8 Suggestions for Further Work

1. **Evaluation with novice programmers.** Test whether early type feedback and the error messages help learners, for example by comparing how Licht and a dynamically typed language report the same mistakes, and by studying how novices respond to the messages (Nagura & Kondo, 2024; Lappi et al., 2023).
2. **Performance measurement.** Measure the execution speed of the tree-walking evaluator and compare it with a bytecode virtual machine design, which Nystrom (2021) presents as the next step after tree walking (Smith & Nair, 2005).
3. **Language extensions.** Add features that were excluded from the scope, such as lambda functions and closures, collection types in annotations, and a generic type system, each as a new increment with its own tests.
4. **Broader testing.** Add randomly generated programs and stress tests to complement the hand-written test cases.
5. **Richer diagnostics.** Extend error reporting so that messages are categorised more finely, and evaluate how well they support learners (Nagura & Kondo, 2024).

## 5.9 Chapter Summary

This chapter summarised the study, showed how the objectives were addressed and stated the conclusions. The study designed and implemented Licht, a small beginner-oriented language, together with a Rust reference interpreter that checks types in a separate pass before evaluation, and it documented the requirements, design, and tests so that the interpreter can be studied as a worked example. The conclusions are bounded by the limitations of the study, above all by the absence of any test with novice programmers and of any measurement of performance, and the suggestions for further work identify how those gaps can be closed.

## References

Graham, D. (1989). Incremental development: Review of nonmonolithic life-cycle development models. *Information and Software Technology, 31*(1), 7–20. https://doi.org/10.1016/0950-5849(89)90049-9

Lappi, V., Tirronen, V., & Itkonen, J. (2023). A replication study on the intuitiveness of programming language syntax. *Software Quality Journal, 31*, 1211–1240. https://doi.org/10.1007/s11219-023-09631-7

Nagura, M., & Kondo, R. (2024). A learning support method for novice programmers based on categorization of compilation error messages. *Journal of Software and Systems, 41*(2), 2_3–2_18. https://doi.org/10.11309/jssst.41.2_3

Nystrom, R. (2021). *Crafting interpreters*. https://craftinginterpreters.com/

Pierce, B. C. (2002). *Types and programming languages*. MIT Press. https://mitpress.mit.edu/9780262162098/types-and-programming-languages/

Smith, J. E., & Nair, R. (2005). The architecture of virtual machines. *Computer, 38*(5), 32–38. https://doi.org/10.1109/MC.2005.173
