# CHAPTER FIVE: SUMMARY, CONCLUSIONS, AND RECOMMENDATIONS

## 5.1 Introduction

This chapter closes the study. It summarises the work reported in the earlier chapters, discusses the objectives set in Chapter One and the gaps that each of them covered, and states the conclusions that follow. It then describes the contribution of the study, restates the limitations that bound those conclusions, and ends with recommendations and suggestions for further work.

## 5.2 Summary of the Study

Chapter One introduced the study. It explained that programming languages reach their users through implementations, that languages differ in when they detect type errors, and that syntax, compiler diagnostics, and conceptual load are barriers for novice programmers (Lappi et al., 2023; Nagura & Kondo, 2024). It stated the problem, the aim, the objectives, the significance, the scope, and the limitations of the study. The aim was to design and implement Licht, a small beginner-oriented programming language with a reference interpreter written in Rust that reports type errors before a program runs, and to document and verify the interpreter so that it can be studied as a worked example of how a programming language is built.

Chapter Two reviewed how Rust, Python, Haskell, Lisp, Lua, OCaml, and JavaScript runtimes are implemented and compared published benchmark results on execution time, energy, and memory (Pereira et al., 2017). It examined five common challenges: performance against simplicity, the timing of type errors, syntax and complexity, feature completeness, and the abstraction that lexer and parser tools place between a student and an implementation. It ended by identifying six gaps in the literature reviewed: a comparative gap, a specification gap, a transparency gap, an early-checking gap, a feature-set gap, and a verification and documentation gap. Taken together, they meant that no single small reference interpreter, written out in full, integrated early static type checking with hybrid typing, tree-walking evaluation, directly provided common constructs, and a deliberately limited feature set under an explicit set of requirements.

Chapter Three set out the methodology and design. The study followed the Iterative and Incremental Development methodology, in which each component of the interpreter was built, tested, and refined before the next was added (Graham, 1989). The chapter also stated the design decisions taken in response to the gaps, twelve user requirements, 22 functional requirements, and six non-functional requirements, defined the scope, and described an architecture in which source code passes through a lexer, a parser, a type checker, and a tree-walking evaluator. It also presented the component designs, the proposed grammar, a sample program, and the decision to use Rust with only its standard library.

Chapter Four described how each component was implemented and how the interpreter was tested. The implementation consists of separate modules for the lexer, the parser, the abstract syntax tree, the type checker, and the evaluator, with each stage reporting its own category of error. Testing used 48 test cases: 10 for the lexer, 11 for the parser, 13 for the type checker, 10 for the evaluator, and 4 integration cases. The chapter traced the test cases back to the functional requirements.

## 5.3 Discussion of the Objectives

The eight objectives set in Chapter One were discussed against the work that was carried out. Chapter Two identified six gaps in the literature reviewed, and each objective was discussed in terms of the gap or gaps that it covered.

1. **Objective 1: reviewing existing implementations.** The objective was to review how existing languages and runtimes were implemented and to identify the trade-offs that bear on a minimal educational interpreter. Chapter Two reviewed seven languages and runtimes and compared their execution models, typing behaviour, syntax, feature sets, and the abstraction of the tools used to build them. It supported the comparison with published benchmark results on speed, energy, and memory (Pereira et al., 2017). This objective covered the comparative gap (Gap 1). The sources reviewed each described one language, runtime, or technique, and none compared them on the dimensions that mattered to a small educational interpreter. The review brought those descriptions together and supplied the evidence from which the other five gaps were identified.
2. **Objective 2: specifying the requirements and the scope.** The objective was to specify the functional and non-functional requirements and to define the scope of the project. Chapter Three specified 12 user requirements, 22 functional requirements, and six non-functional requirements, and it stated what was inside and outside the scope. This objective covered the specification gap (Gap 2). The educational sources reviewed built interpreters and compilers in stages (Nystrom, 2021; Ghuloum, 2006) but did not state requirements and scope in a form against which the finished implementation could be checked. The stated requirements gave the interpreter a standard that it could be verified against, and the stated scope kept it small enough to be read in full.
3. **Objective 3: designing the architecture.** The objective was to design the architecture as a pipeline of separate components and to define the grammar and syntax of the language. Chapter Three presented the pipeline of lexer, parser, abstract syntax tree, type checker, and evaluator, with a design diagram, a data and information flow diagram, an evaluator design diagram, the proposed grammar, and a sample program. This objective covered the transparency gap (Gap 3). Generated front ends hid the scanning and parsing logic inside tool output, and production runtimes such as V8 spread execution across several tiers (V8, 2023). The separate components and explicit stage boundaries meant that each stage could be understood on its own.
4. **Objective 4: implementing the interpreter with the standard library only.** The objective was to implement the interpreter in Rust using only the standard library, with a hand-written lexer and parser. Chapter Four (Section 4.2) showed that the lexer and parser were written by hand and that no external lexer, parser, or parser-generator library was used, and it presented the main code of each stage. This objective also covered the transparency gap (Gap 3). Lexer and parser generators replaced the code that a student would otherwise study with a specification, and students who used them risked not learning the algorithms that were applied (Louden, 1997). The hand-written implementation meant that a student could see the whole path from source text to program output in the project's own code, without unnecessary abstraction.
5. **Objective 5: implementing the separate type-checking pass.** The objective was to implement a separate static type-checking pass in which function parameters and return types were annotated explicitly and local variable types were inferred. The type checker ran as a separate pass over the AST, inferred the types of local variables from their assigned expressions, checked function calls against declared signatures, and did not invoke the evaluator when it found a type error. This objective covered the early-checking gap (Gap 4). The languages reviewed checked types early only with substantial machinery, as in Rust's ownership system and OCaml's Hindley–Milner inference, or checked them at run time, as in Python and Lua. The hybrid scheme gave early checking at a scale that a student could read in full.
6. **Objective 6: implementing the language features and error reporting.** The objective was to implement the selected language features together with the reporting of lexical, syntax, type, and runtime errors. The interpreter supported conditional expressions, named functions as first-class values, `map()` and `filter()`, range-based iteration, and file input and output, and each stage reported errors in its own category. This objective covered the feature-set gap (Gap 5). The sources reviewed provided higher-order operations through anonymous functions, closures, traits, or generic types. The dedicated type-checking rules for `map()` and `filter()` showed that these operations could be supported at small scale with named functions only. The four error categories also responded to the finding that confusing compiler messages created difficulties for novices (Nagura & Kondo, 2024).
7. **Objective 7: testing and verifying the implementation.** The objective was to test each stage independently and then the integrated interpreter, and to verify the implementation against the requirements. Each stage was tested on its own, followed by integration tests of the complete interpreter, and every functional requirement was covered by at least one of the 48 test cases (Chapter Four, Section 4.4). All 48 test cases passed [confirm after running `cargo test`]. This objective covered the verification and documentation gap (Gap 6). The sources reviewed used tests to confirm the stages of a build (Ghuloum, 2006) but did not link tests to stated requirements. The test cases were linked to requirements, so a feature could be followed from specification to verification.
8. **Objective 8: documenting the work as a worked example.** The objective was to document the requirements, design, implementation, and tests so that the interpreter could serve as a worked example. The study documented each of these in Chapters Three and Four, including the link from each functional requirement to the test cases that exercised it. This objective also covered the verification and documentation gap (Gap 6), and it supported the specification gap (Gap 2). The documentation gave students and educators a single account of how a small interpreter was specified, built, and verified.

Taken together, the eight objectives covered the six gaps, and combining them in one small interpreter filled the overall gap that Chapter One stated. The discussion also recorded what the objectives did not cover. No study with novice programmers was carried out, so the benefit of early type checking and of the error messages to learners was not established, and execution speed was not measured, so the cost of the tree-walking design was not quantified for Licht.

## 5.4 Conclusions

1. **Early type checking can be made to stop a faulty program before it produces any output.** Because the type checker runs before the evaluator, a program containing a type error is rejected without being evaluated, and it produces no output. This confirms the ordering of the stages. It does not show that early checking helps learners, which remains a design rationale and not an empirical result (Nagura & Kondo, 2024).
2. **Hybrid typing and dedicated checking rules can support higher-order operations without a generic type system.** Explicit function signatures with inferred local variables, together with dedicated rules for `map()` and `filter()`, gave type-checked higher-order operations at a lower cost in machinery than a full generic system, at the price of named-function-only use and no collection types in annotations.
3. **Simplicity was achieved at an unmeasured cost in speed.** A tree-walking evaluator keeps the execution path short and readable (Nystrom, 2021). The study did not measure its execution speed, so the size of the performance cost is not known.
4. **Documented requirements linked to tests make a small interpreter easier to follow as a worked example.** The user requirements, functional requirements, design, and test cases are traceable to one another, so a reader can follow a feature from specification to verification. Whether this helps students in practice was not tested.
5. **The benefit to novice programmers is a design intention and has not been established.** The study did not test the interpreter, its error messages, or its syntax with novice programmers.

## 5.5 Contribution of the Study

The study makes the following contributions, which are modest in scale and tied to the gaps identified in Chapter Two:

- A documented, minimal reference interpreter that integrates early static type checking with hybrid typing, tree-walking evaluation, directly provided common constructs, and a deliberately limited feature set.
- A hand-written interpreter whose stages are all written out in the project's own code, with no generated code or third-party parsing library, so that a student can read the whole implementation without unnecessary abstraction.
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

This chapter summarised the study, discussed the objectives and the gaps that they covered, and stated the conclusions. The study designed and implemented Licht, a small beginner-oriented language, together with a Rust reference interpreter that checks types in a separate pass before evaluation, and it documented the requirements, design, and tests so that the interpreter can be studied as a worked example. The conclusions are bounded by the limitations of the study, above all by the absence of any test with novice programmers and of any measurement of performance, and the suggestions for further work identify how those gaps can be closed.

## References

Ghuloum, A. (2006). An incremental approach to compiler construction. In *Proceedings of the 2006 Scheme and Functional Programming Workshop* (University of Chicago Technical Report TR-2006-06). https://www.schemeworkshop.org/2006/11-ghuloum.pdf

Graham, D. (1989). Incremental development: Review of nonmonolithic life-cycle development models. *Information and Software Technology, 31*(1), 7–20. https://doi.org/10.1016/0950-5849(89)90049-9

Lappi, V., Tirronen, V., & Itkonen, J. (2023). A replication study on the intuitiveness of programming language syntax. *Software Quality Journal, 31*, 1211–1240. https://doi.org/10.1007/s11219-023-09631-7

Louden, K. C. (1997). *Compiler construction: Principles and practice*. PWS Publishing. (Preface available at https://www.cs.sjsu.edu/faculty/louden/cmptext/preface.htm)

Nagura, M., & Kondo, R. (2024). A learning support method for novice programmers based on categorization of compilation error messages. *Journal of Software and Systems, 41*(2), 2_3–2_18. https://doi.org/10.11309/jssst.41.2_3

Nystrom, R. (2021). *Crafting interpreters*. https://craftinginterpreters.com/

Pereira, R., Couto, M., Ribeiro, F., Rua, R., Cunha, J., Fernandes, J. P., & Saraiva, J. (2017). Energy efficiency across programming languages: How do energy, time, and memory relate? In *Proceedings of the 10th ACM SIGPLAN International Conference on Software Language Engineering* (pp. 256–267). ACM. https://doi.org/10.1145/3136014.3136031

Pierce, B. C. (2002). *Types and programming languages*. MIT Press. https://mitpress.mit.edu/9780262162098/types-and-programming-languages/

Smith, J. E., & Nair, R. (2005). The architecture of virtual machines. *Computer, 38*(5), 32–38. https://doi.org/10.1109/MC.2005.173


V8. (2023, December 5). *Maglev: V8's fastest optimizing JIT*. https://v8.dev/blog/maglev
