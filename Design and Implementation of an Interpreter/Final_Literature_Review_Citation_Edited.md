## Introduction

This section reviews how programming languages are implemented and executed. Existing implementations range from direct tree-walking interpreters to bytecode virtual machines, native-code compilers, and multi-tier just-in-time (JIT) systems (Nystrom, 2021; Smith & Nair, 2005). The review examines execution implementations across different paradigms, including Rust, Haskell, Lisp, Python, Lua, OCaml, and JavaScript runtimes. Their implementation approaches and common challenges are compared to identify principles that can inform the design of a minimal educational interpreter (Nystrom, 2021; Smith & Nair, 2005).

## How Each Language Implements Execution

Rust uses a compiled implementation in which the compiler checks ownership and borrowing rules at compile time, with violations preventing compilation; Rust's toolchain produces executables containing native machine code (Klabnik & Nichols, 2023). These rules constrain how values are accessed and modified, providing strong compile-time guarantees and a relatively explicit model of ownership (Klabnik & Nichols, 2023). This can make many classes of program behaviour predictable, although the ownership and borrowing system also introduces concepts that learners must understand before writing some classes of Rust programs (Klabnik & Nichols, 2023).

Python provides a high-level programming model based on objects and dynamic name binding. In Python, names refer to objects, and an object's type is a property of the object rather than of the name bound to it (Python Software Foundation, n.d.). In CPython, most objects are heap allocated and represented through object pointers in the C API (Python Software Foundation, n.d.). This dynamic model permits a name to be rebound to values of different types during execution, which supports flexibility but means that some type-related errors are discovered only when the relevant operation is executed. The language therefore places relatively little static type-checking burden on the programmer compared with statically typed languages such as Rust and OCaml (Python Software Foundation, n.d.; Pierce, 2002). Python also supports multiple programming styles and provides both imperative and functional facilities, making it useful for contrasting programming paradigms (Python Software Foundation, n.d.).

The Glasgow Haskell Compiler (GHC) supports both interpreted and compiled execution. GHCi normally converts loaded Haskell modules to bytecode and executes that bytecode using an interpreter, while Haskell programs can also be compiled to object code and native executables (Marlow, 2010; GHC Team, 2026a). Haskell uses non-strict, lazy evaluation: computation of an expression can be deferred until its result is needed, with sharing used to avoid repeatedly computing the same expression (Launchbury, 1993; HaskellWiki, n.d.). Haskell also defines conditional expressions as values: an `if ... then ... else ...` expression returns the value of the selected branch, and the condition must have type `Bool` while the two branches must have the same type (Marlow, 2010). Lazy evaluation is a significant semantic feature, but it also complicates reasoning about evaluation order and resource usage, especially when combined with effects or other strict operations (Launchbury, 1993).

Lua, like many virtual-machine-based interpreters, compiles source code to bytecode executed by a register-based virtual machine. The Lua 5.1 virtual machine used 38 opcodes, illustrating how a relatively small instruction set can support a practical scripting language (Man, 2006). The official Lua reference manual likewise describes Lua as dynamically typed and as executing bytecode on a register-based virtual machine (Ierusalimschy et al., 2025). In Lua, variables do not have fixed types; values carry types, and values—including functions—are first-class (Ierusalimschy et al., 2025). This makes the language flexible while shifting many type checks to runtime.

OCaml supports both bytecode and native-code execution. The `ocamlc` compiler produces bytecode that can be run by `ocamlrun`, while `ocamlopt` compiles OCaml programs to native code; the native-code compiler generally provides faster execution at the cost of longer compilation and larger executables (Leroy et al., 2026a, 2026b). OCaml uses automatic type inference based on the Hindley–Milner algorithm, allowing many programs to be statically checked without explicit type annotations on every expression (Leroy et al., 2026c). OCaml also performs exhaustiveness analysis on pattern matches and reports non-exhaustive matches as warnings by default (Leroy et al., 2026c, 2026d). This combination gives programmers strong static checking without requiring all types to be written explicitly.

Lisp is a family of languages rather than a single contemporary implementation, but McCarthy's original Lisp provides a particularly useful model for understanding minimal interpreters. The original Lisp work defines symbolic expressions (S-expressions), a small set of elementary functions, conditional expressions, lambda expressions, and a universal `apply` function that serves as an interpreter for Lisp programs (McCarthy, 1960). The representation of programs as symbolic expressions means that the same structural representation can be manipulated as data, a property now commonly described as homoiconicity (McCarthy, 1960). The original evaluator also shows that a compact language core can be implemented using a comparatively small evaluation mechanism (McCarthy, 1960).

The JavaScript runtime V8 uses a tiered execution architecture rather than being purely an interpreter or purely a traditional ahead-of-time compiler. JavaScript is first translated to Ignition bytecode and interpreted; as code becomes hot, V8 can move it through additional compilation tiers, including Sparkplug, Maglev, and TurboFan, to produce progressively more optimized native machine code (V8, 2021, 2023). This adaptive strategy improves startup and peak performance trade-offs by using fast execution mechanisms early and more expensive optimization when runtime feedback justifies it (V8, 2017, 2023). The architecture is consequently much more sophisticated than the execution model required by a minimal educational interpreter.


## Common Challenges Interpreters Face

### Performance vs. Simplicity

Programming-language implementations must balance execution speed, compilation cost, memory use, portability, and implementation complexity. Virtual machines and interpreters introduce execution overhead, while compilation and dynamic translation can improve runtime performance at the cost of additional machinery and compilation work (Smith & Nair, 2005). The appropriate balance depends on the goals of the implementation rather than on a single universally superior architecture.

Rust illustrates a compile-time-heavy approach: ownership and borrowing are enforced by the compiler, while the resulting program executes as native code (Klabnik & Nichols, 2023). Python emphasizes dynamic objects and runtime flexibility, while interpreted execution introduces runtime overhead compared with directly executing native code (Python Software Foundation, n.d.; Smith & Nair, 2005). OCaml offers an explicit choice between bytecode and native compilation: bytecode can be produced and run through `ocamlrun`, while native compilation generally gives faster execution but requires more compilation work (Leroy et al., 2026a, 2026b). JavaScript runtimes push this trade-off into the runtime itself by adapting execution based on observed program behaviour and selectively compiling hot code (V8, 2017, 2021, 2023).

For a minimal educational interpreter, implementation simplicity is a defensible design priority. A tree-walking interpreter can expose the core relationship between syntax, evaluation rules, and program behaviour without requiring the additional compiler and virtual-machine layers found in more performance-oriented systems (Nystrom, 2021). This does not imply that tree-walking is generally faster; rather, it is attractive because the implementation can remain direct enough to study and extend while still supporting a complete small language (Nystrom, 2021).

### Type-Error Timing

A central implementation decision is when type errors should be detected. Static type systems are designed to reject certain classes of erroneous programs before execution, whereas dynamically typed systems defer many checks until operations are performed at runtime (Pierce, 2002). The choice therefore changes both the runtime behaviour of the language and the responsibilities placed on the implementation.

Rust uses compile-time checking for ownership and many type-related constraints; programs that violate the relevant rules do not compile (Klabnik & Nichols, 2023). Rust also performs pattern and exhaustiveness analysis as part of compile-time checking (Klabnik & Nichols, 2023). OCaml similarly rejects statically ill-typed programs during compilation and additionally reports non-exhaustive pattern matching through warnings (Leroy et al., 2026c, 2026d). Python and Lua, in contrast, are dynamically typed and perform type-dependent checks during execution (Python Software Foundation, n.d.; Ierusalimschy et al., 2025).

For a beginner-oriented interpreter, detecting type errors before evaluation is a reasonable pedagogical design choice because it makes type feedback occur at a clearly defined stage of program processing. This is a design rationale rather than a universal empirical conclusion: research on novice programmers shows that compiler diagnostics can create learning difficulties when errors generate multiple confusing messages, while structured error categorisation can improve students' ability to address them (Nagura & Kondo, 2024). Licht therefore adopts early checking while aiming to keep the checker substantially simpler than Rust's full ownership and borrowing system.

### Syntax and Complexity

The issue of complexity can be viewed from two perspectives: the complexity required to implement a language and the complexity experienced by programmers who use it. These dimensions do not necessarily move together. A language may use a sophisticated implementation to provide a relatively convenient programming model, or expose more of its complexity directly to programmers.

Modern JavaScript runtimes illustrate implementation complexity through multi-tier interpretation and compilation, runtime profiling, and adaptive optimization (V8, 2017, 2021, 2023). Lisp provides a contrasting case: McCarthy's original formalism defines a small language core with S-expressions, a small set of primitive operations, and an evaluator that directly interprets those expressions (McCarthy, 1960). This illustrates how a compact language kernel can make an interpreter conceptually manageable, although users may have to construct higher-level facilities from the available primitives.

Rust places substantial language-enforced structure on the programmer through ownership, borrowing, types, and pattern checking (Klabnik & Nichols, 2023). Research on programming-language syntax also shows that syntax is an important barrier for novices and that the intuitiveness of language keywords and constructs can be studied empirically rather than assumed (Lappi et al., 2023). The relevant design objective for Licht is therefore not merely to minimize the number of features, but to choose rules and constructs that are explicit, consistent, and sufficiently expressive for the intended educational tasks.

An approach closer to Rust's philosophy of clear rules is therefore appropriate, but without adopting its complete level of compile-time strictness. Licht can place more responsibility on the interpreter than a dynamically typed language does, while avoiding the ownership, lifetime, and borrowing mechanisms that are outside the intended scope (Klabnik & Nichols, 2023). Likewise, rather than exposing only a very small collection of primitive forms as in the original Lisp kernel, Licht can provide common language constructs directly so that beginner programs do not require them to reconstruct basic control and arithmetic behaviour from lower-level pieces (McCarthy, 1960).

### Feature Completeness

The comparison of the selected languages reveals a common set of capabilities—conditional execution, functions, iteration, higher-order operations, and input/output—implemented through substantially different language and runtime mechanisms (Marlow, 2010; Python Software Foundation, n.d.; Klabnik & Nichols, 2023; Ierusalimschy et al., 2025; Leroy et al., 2026a). For a minimal educational interpreter, the feature set should therefore emphasize capabilities that support fundamental programming tasks while avoiding abstractions whose implementation cost is disproportionate to their educational value. This is the design criterion used for the proposed language.

Conditional constructs are fundamental to program control. Rust provides `if`/`else` expressions and exhaustive pattern matching through `match` (Klabnik & Nichols, 2023). Python provides `if`, `elif`, and optional `else` clauses (Python Software Foundation, n.d.). OCaml treats conditionals as expressions whose branches produce a value (Leroy et al., 2026c). Lisp uses conditional special forms, and the original McCarthy formulation includes conditional expressions as a core part of its evaluation model (McCarthy, 1960). Haskell's `if` expression likewise evaluates a Boolean condition and returns one of two branches, which must have the same type (Marlow, 2010). Since all surveyed languages provide conditional execution, conditional expressions are a core feature for Licht.

File input and output provide programs with access to persistent external data. Python supplies the built-in `open()` mechanism and file objects (Python Software Foundation, n.d.). Rust provides file operations through `std::fs`, with functions such as `read_to_string` returning `Result` values to represent success or failure (Klabnik & Nichols, 2023). Node.js exposes file-system operations through its `fs` module, including synchronous, callback-based, and promise-based forms (Node.js, n.d.). Lua provides file input/output through its `io` library, including `io.open()` and related file operations (Ierusalimschy et al., 2025). File I/O is therefore a useful capability for moving beyond self-contained demonstrations and supporting programs that read or persist external data.

Anonymous functions are closely related to lambda calculus and were present in the original Lisp formulation through lambda expressions (McCarthy, 1960). Python's `lambda` expression creates an anonymous function (Python Software Foundation, n.d.). Haskell supports lambda abstractions directly, and Rust and OCaml provide anonymous functions or closures through their function-expression forms (Marlow, 2010; Klabnik & Nichols, 2023; Leroy et al., 2026c). Although anonymous functions are useful for concise higher-order programming, they are not essential to the minimum set of capabilities required by Licht. Named functions can serve as first-class values and be passed to `map()` and `filter()` without requiring an additional closure syntax. Lambda functions are therefore excluded from the initial implementation as a deliberate scope decision.

Higher-order functions such as `map` and `filter` allow a function to be applied to collection elements or used to select elements meeting a predicate. Python provides `map()` and `filter()` as built-in functions that return iterators (Python Software Foundation, n.d.). Haskell provides `map` and `filter` over lists, while OCaml provides `List.map` and `List.filter` (Marlow, 2010; Leroy et al., 2026e). Rust provides analogous functionality through lazy iterator adapters such as `map` and `filter` (Klabnik & Nichols, 2023). Since Licht already supports named functions as first-class values, `map()` and `filter()` can be included without requiring anonymous-function syntax.

Python and Haskell additionally provide comprehension syntax for concise collection construction and transformation (Python Software Foundation, n.d.; Marlow, 2010). Comprehensions provide an alternative surface notation rather than a fundamentally new evaluation capability, so they are excluded from the minimal feature set. Rust's iterator abstraction is powerful and extensible, but introducing a full trait-based iterator model would add substantially more type-system and library machinery than is necessary for the intended interpreter (Klabnik & Nichols, 2023). Licht therefore uses simpler iteration constructs rather than reproducing the abstraction level of Rust's iterator system.

Iteration is the sequential processing of elements in a collection or sequence. Python's `for` statement creates an iterator and assigns successive values to the loop target, while Python also supports generators through `yield` (Python Software Foundation, n.d.). Lua supports numeric and generic `for` loops, including iteration over table elements through `pairs()` and `ipairs()` (Ierusalimschy et al., 2025). Rust expresses iteration through the `Iterator` trait and lazy adapters (Klabnik & Nichols, 2023). Haskell commonly expresses repeated computation through recursive functions over lists and other structures rather than imperative `for` loops (Marlow, 2010). For Licht, basic looping constructs provide sufficient iteration capability without requiring a separate iterator abstraction.

Taken together, the comparison supports a focused feature set centred on essential programming capabilities. The proposed interpreter will implement conditional expressions, named functions as first-class values, `map()` and `filter()` operations, simple iteration through basic looping constructs, and file input/output. Lambda functions, comprehensions, and complex iterator abstractions will be excluded because their additional convenience or expressive syntax does not justify the corresponding implementation cost within the project's intended scope. This feature selection is a project design decision informed by the implementation trade-offs observed across the surveyed languages (Nystrom, 2021; Smith & Nair, 2005).

## Design Response

Drawing on the preceding review, the proposed educational programming language and its reference interpreter will be called Licht, the German word for “light.” The name reflects the primary aim of the project: to provide a lightweight, beginner-oriented language whose design prioritizes clarity, simplicity, and ease of understanding over feature completeness or maximum performance. The following sections translate the implementation trade-offs identified in the literature into concrete design choices for a minimal interpreter (Nystrom, 2021; Lappi et al., 2023).

### Type Checking Design

Licht will use a lightweight static type-checking approach in which type errors are detected before program execution begins, following a model closer to OCaml than to Python. Static type systems are designed to reject classes of erroneous programs before execution, while OCaml demonstrates that strong static checking can coexist with substantial type inference (Pierce, 2002; Leroy et al., 2026c).

To achieve this, Licht will use a separate type-checking pass that traverses the Abstract Syntax Tree (AST) before evaluation begins. During this pass, the interpreter will verify that operations are performed on compatible types and that function calls receive and return expected types. If a type error is detected, execution will stop before the program is evaluated. Separating analysis from evaluation follows the broader compiler/interpreter architecture in which parsing, static analysis, and execution are distinct conceptual stages (Nystrom, 2021; Appel, 1997).

For type declarations, Licht will use a hybrid approach. Function parameters and return types will require explicit annotations, allowing the programmer to see the intended interface of a function. Local variables, however, will use type inference, where their types are determined from their assigned expressions. This balances explicit interface information with the reduced annotation burden associated with inference systems such as OCaml's (Leroy et al., 2026c). The resulting design is intended to preserve predictable static behaviour without requiring type annotations throughout every local expression.

### Execution Model Design

Licht will use a tree-walking interpreter design, where source code is first parsed into an Abstract Syntax Tree and the evaluator then directly traverses that tree to execute the program. Tree-walking interpretation is a well-established implementation strategy for small languages and is explicitly used by Nystrom's educational interpreter implementation (Nystrom, 2021).

Unlike bytecode-based interpreters such as Lua, Licht will not introduce an intermediate bytecode representation, and unlike systems such as Rust and modern JavaScript runtimes, it will not compile programs into native machine code as part of the language's execution model (Ierusalimschy et al., 2025; Klabnik & Nichols, 2023; V8, 2023). This keeps the execution path short and visible: source text is parsed into a tree, the tree is checked, and the checked tree is evaluated.

The interpreter will perform two separate passes over the AST. The first pass will be a type-checking phase, where the AST is analysed to verify that operations and function interactions are type-safe before execution begins. After the program passes this stage, a second evaluation pass will walk through the AST and execute the program. The separation is consistent with the broader idea of using distinct analysis and execution stages in language implementations, while being deliberately simpler than a full compiler pipeline (Appel, 1997; Nystrom, 2021).

Licht prioritises implementation simplicity and manageability over maximum execution speed. Lua's bytecode VM and JavaScript's adaptive multi-tier execution can reduce runtime overhead, but they require additional representation, dispatch, profiling, optimization, and code-generation machinery (Ierusalimschy et al., 2025; V8, 2017, 2021, 2023). Since the purpose of Licht is to provide a clear and understandable reference implementation, a tree-walking interpreter provides a better match to the project's educational and minimality goals (Nystrom, 2021).

### Language Complexity Design

Licht will follow an approach closer to Rust's philosophy of placing responsibility on the language implementation to provide explicit and predictable rules, but without adopting Rust's full level of strictness (Klabnik & Nichols, 2023). The type-checking system is the clearest example: rather than allowing type-related errors to emerge only during execution as in dynamically typed languages, Licht performs a separate checking pass before evaluation begins (Pierce, 2002; Python Software Foundation, n.d.; Ierusalimschy et al., 2025).

Unlike the small primitive kernel of the original Lisp model, Licht will provide common language constructs directly. Conditionals, basic arithmetic operators, functions, and other essential operations will be built into the language rather than requiring users to construct them from a very small set of lower-level primitives (McCarthy, 1960). This reduces the amount of language machinery that beginners must reconstruct mentally before they can express common programs.

However, Licht will not attempt to replicate Rust's complete compile-time safety model. Ownership checking, borrowing, lifetimes, and the associated memory-safety guarantees are central to Rust but would introduce significant additional implementation and conceptual complexity beyond the scope of a minimal educational interpreter (Klabnik & Nichols, 2023). The intended approach is therefore to adopt the principle of clear rules without reproducing the entire systems-programming type and ownership model.

### Feature Set Design

Based on the Feature Completeness analysis, Licht will implement a focused set of language features that provide essential programming capabilities while avoiding unnecessary complexity. The feature set is designed around the goal of maintaining a minimal, clear, and manageable educational interpreter (Nystrom, 2021).

Licht will include conditional expressions, named functions as first-class values, `map()` and `filter()` operations, simple iteration through basic looping constructs, and file input/output. Named functions will be sufficient to support higher-order operations such as `map()` and `filter()` without requiring an additional anonymous-function syntax. This design follows the functional capabilities already present in languages such as Python, Haskell, OCaml, Rust, and Lua while deliberately reducing the number of distinct syntactic mechanisms the interpreter must implement (Marlow, 2010; Python Software Foundation, n.d.; Klabnik & Nichols, 2023; Ierusalimschy et al., 2025; Leroy et al., 2026e).

Licht will intentionally exclude lambda functions, comprehensions, and complex iterator abstractions. Lambda functions add convenience for defining functions inline, but named functions can provide the higher-order behaviour needed for the supported collection operations. Comprehensions provide concise alternative syntax, while advanced iterator systems introduce additional implementation concepts that are unnecessary for the intended scope (Python Software Foundation, n.d.; Klabnik & Nichols, 2023). These exclusions are therefore deliberate scope controls rather than claims that the omitted features are inherently poor language features.

The final feature set prioritises usability and simplicity over feature completeness. By including fundamental constructs directly while avoiding unnecessary abstractions, Licht aims to provide enough functionality for practical small programs while remaining aligned with the project's objective of creating a minimal and manageable educational interpreter (Nystrom, 2021).

## Glossary

**Generalized Algebraic Data Type (GADT)**  
A data type whose constructors can express more specific type relationships than ordinary algebraic data types. GADTs can therefore allow a type system to represent relationships between constructors and result types more precisely (Leroy et al., 2026c; Pierce, 2002).

**Hindley–Milner style type inference**  
A static type-inference approach in which the compiler determines types from how expressions are used and can infer a most-general, or principal, type without requiring explicit annotations everywhere (Leroy et al., 2026c).

**Algebraic Data Type (ADT)**  
A data type constructed from combinations of alternatives (sum types) and collections of fields (product types). Algebraic data types provide a way to model values with a fixed structural set of alternatives (Pierce, 2002).

**Bytecode**  
An intermediate representation of a program intended for execution by a virtual machine rather than directly by the target processor. Bytecode separates a language implementation from the host machine's native instruction set (Smith & Nair, 2005; Ierusalimschy et al., 2025).

**Tree-walking interpreter**  
An interpreter that executes a program by directly traversing its Abstract Syntax Tree rather than first translating the program into a separate bytecode or native-code representation (Nystrom, 2021).

**JIT (Just-In-Time) compilation**  
A compilation strategy in which code is compiled into native machine code during program execution rather than entirely before execution starts. Modern JIT runtimes commonly use runtime information to decide which code should receive more expensive optimization (V8, 2017, 2023).

**Pattern matching**  
A mechanism for selecting program behaviour by comparing a value with patterns that describe its structure, such as constructors, tuples, records, or literals (Klabnik & Nichols, 2023; Marlow, 2010).

**Exhaustiveness / exhaustive matching**  
The property that the patterns in a match expression cover all possible values allowed by the type being matched. Exhaustiveness analysis can therefore identify omitted cases before execution in statically checked languages (Klabnik & Nichols, 2023; Leroy et al., 2026d).

**First-class values/functions**  
A property in which values, including functions, can be stored in variables, passed as arguments, returned from functions, and used in expressions as ordinary values (Pierce, 2002; Ierusalimschy et al., 2025).

**Closure**  
A function value that retains access to variables from the environment in which it was created. Closures therefore combine executable code with access to captured surrounding bindings (Klabnik & Nichols, 2023).

**Homoiconicity**  
A language property in which the structural representation of programs uses the same kinds of data structures that the language manipulates as data. Lisp's use of S-expressions provides the classic example of this idea (McCarthy, 1960).

## References

Appel, A. W. (1997). *Modern compiler implementation in ML*. Cambridge University Press. https://doi.org/10.1017/CBO9780511811449

HaskellWiki. (n.d.). *Lazy evaluation*. https://wiki.haskell.org/Lazy_evaluation

Ierusalimschy, R., de Figueiredo, L. H., & Celes, W. (2025). *Lua 5.4 reference manual*. Lua.org. https://www.lua.org/manual/5.4/manual.html

Karachalias, G., Schrijvers, T., Vytiniotis, D., & Jones, S. P. (2015). GADTs meet their match: Pattern-matching warnings that account for GADTs, guards, and laziness. In *Proceedings of the 20th ACM SIGPLAN International Conference on Functional Programming* (pp. 424–436). ACM. https://doi.org/10.1145/2784731.2784748

Klabnik, S., & Nichols, C. (2023). *The Rust programming language* (2nd ed.). No Starch Press. https://nostarch.com/rust-programming-language-2nd-edition

Lappi, V., Tirronen, V., & Itkonen, J. (2023). A replication study on the intuitiveness of programming language syntax. *Software Quality Journal, 31*, 1211–1240. https://doi.org/10.1007/s11219-023-09631-7

Launchbury, J. (1993). A natural semantics for lazy evaluation. In *Proceedings of the 20th ACM SIGPLAN-SIGACT Symposium on Principles of Programming Languages* (pp. 144–154). ACM. https://doi.org/10.1145/158511.158618

Leroy, X., Doligez, D., Frisch, A., Garrigue, J., Rémy, D., Sivaramakrishnan, K., & Vouillon, J. (2026a). *The OCaml system: Native-code compilation (ocamlopt)*. OCaml Documentation. https://ocaml.org/manual/5.5/native.html

Leroy, X., Doligez, D., Frisch, A., Garrigue, J., Rémy, D., Sivaramakrishnan, K., & Vouillon, J. (2026b). *The OCaml system: The runtime system (ocamlrun)*. OCaml Documentation. https://ocaml.org/manual/5.5/runtime.html

Leroy, X., Doligez, D., Frisch, A., Garrigue, J., Rémy, D., Sivaramakrishnan, K., & Vouillon, J. (2026c). *The OCaml language*. OCaml Documentation. https://ocaml.org/manual/5.5/expr.html

Leroy, X., Doligez, D., Frisch, A., Garrigue, J., Rémy, D., Sivaramakrishnan, K., & Vouillon, J. (2026d). *Common error messages: Pattern matching warnings and errors*. OCaml Documentation. https://ocaml.org/docs/common-errors

Leroy, X., Doligez, D., Frisch, A., Garrigue, J., Rémy, D., Sivaramakrishnan, K., & Vouillon, J. (2026e). *Stdlib.List*. OCaml Documentation. https://ocaml.org/manual/5.5/api/Stdlib.List.html

Marlow, S. (Ed.). (2010). *Haskell 2010 language report*. Haskell.org. https://www.haskell.org/definition/haskell2010.pdf

McCarthy, J. (1960). Recursive functions of symbolic expressions and their computation by machine, Part I. *Communications of the ACM, 3*(4), 184–195. https://doi.org/10.1145/367177.367199

Nagura, M., & Kondo, R. (2024). A learning support method for novice programmers based on categorization of compilation error messages. *Journal of Software and Systems, 41*(2), 2_3–2_18. https://doi.org/10.11309/jssst.41.2_3

Node.js. (n.d.). *File system*. https://nodejs.org/api/fs.html

Nystrom, R. (2021). *Crafting interpreters*. https://craftinginterpreters.com/

Pierce, B. C. (2002). *Types and programming languages*. MIT Press. https://mitpress.mit.edu/9780262162098/types-and-programming-languages/

Python Software Foundation. (n.d.). *The Python language reference*. Python Documentation. https://docs.python.org/3/reference/index.html

Smith, J. E., & Nair, R. (2005). The architecture of virtual machines. *Computer, 38*(5), 32–38. https://doi.org/10.1109/MC.2005.173

Steele, G. L. (1990). *Common LISP: The language* (2nd ed.). Digital Press. https://www.cs.cmu.edu/Groups/AI/html/cltl/cltl2.html

V8. (2017, May 15). *Launching Ignition and TurboFan*. https://v8.dev/blog/launching-ignition-and-turbofan

V8. (2021, April 27). *Sparkplug — a non-optimizing JavaScript compiler*. https://v8.dev/blog/sparkplug

V8. (2023, December 5). *Maglev: V8's fastest optimizing JIT*. https://v8.dev/blog/maglev

Man, K.-H. (2006). *A no-frills introduction to Lua 5.1 VM instructions* (Version 0.1). https://archive.org/details/a-no-frills-intro-to-lua-5.1-vm-instructions
