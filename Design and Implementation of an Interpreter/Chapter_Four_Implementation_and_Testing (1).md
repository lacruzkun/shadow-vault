# CHAPTER FOUR: IMPLEMENTATION AND TESTING

## 4.1 Introduction

This chapter presents the implementation of the Licht interpreter and the tests used to verify it against the requirements set out in Chapter Three. Chapter Two compared how Rust, Python, Haskell, Lua, OCaml, Lisp, and JavaScript runtimes execute programs and identified six gaps in the literature reviewed. Chapter Three turned those gaps into design decisions, a pipeline of lexer, parser, type checker, and tree-walking evaluator, and a set of requirements: 22 functional requirements and six non-functional requirements.

The chapter follows the incremental order of Section 3.1. Section 4.2 describes how each component is implemented and shows the main code for each. Section 4.3 sets out the testing strategy and the test cases for each stage. Section 4.4 traces the test cases back to the functional requirements, and Section 4.5 summarises the chapter.

## 4.2 Implementation

Licht is implemented as a standalone Rust application whose modules mirror the processing pipeline of Section 3.3: lexer, parser, type checker, and evaluator. The AST definitions, the shared type representation, and the error type are separate modules, and `src/lib.rs` exposes the pipeline as a small library. Each subsection below ends with the main code excerpt for its component. Every listing names the file it comes from, and the code is reproduced from that file. The excerpts are shortened to show the central logic of each stage.

### 4.2.1 Implementation Environment and Project Structure

The interpreter is written in Rust and uses only the Rust standard library for its core components, as specified in Section 3.6. No lexer-generator, parser-generator, or parser-combinator crate is used, so the path from source text to tokens, AST nodes, type checking, and evaluation can be followed directly in the project's own code (Nystrom, 2021; Appel, 1997). Because no generated code or third-party parsing library stands between the reader and the algorithms, a student can read the whole implementation of each stage without unnecessary abstraction (Louden, 1997; Nystrom, 2021).

The code is organised into separate modules for the lexer, the parser, the AST definitions, the type checker, the evaluator, the shared type and error definitions, and the REPL. Treating each stage boundary as a module boundary allows a stage to be built and tested before the next one depends on it (Graham, 1989), and it supports the extensibility requirement of Section 3.2.3.

The files and their relationships are shown below. All paths are relative to the project root (`licht/`).

```text
licht/
├── Cargo.toml
├── src/
│   ├── main.rs            binary entry point (file or REPL)
│   ├── lib.rs             library root: declares modules and pipeline helpers
│   ├── lexer.rs           source text -> Vec<Token>
│   ├── parser.rs          Vec<Token> -> Program (AST)
│   ├── ast.rs             Expr, ExprKind, Stmt, Block, FuncDecl, Program
│   ├── types.rs           Type enum and the compatible() rule
│   ├── checker.rs         Program -> Result<(), LichtError>
│   ├── interpreter.rs     AST -> values and program output
│   ├── error.rs           ErrorKind, Pos, LichtError
│   ├── repl.rs            persistent read-eval-print loop
│   ├── scanner.rs         legacy bytecode scanner (not compiled)
│   ├── chunk.rs           legacy bytecode chunk (not compiled)
│   ├── compiler.rs        legacy bytecode compiler (not compiled)
│   └── vm.rs              legacy bytecode VM (not compiled)
├── tests/
│   ├── chapter4_tests.rs  cases L1-L10, P1-P11, T1-T13, E1-E10, I1-I4
│   ├── repl_tests.rs      REPL state, rollback, multi-line input
│   ├── print_tests.rs     variadic print
│   └── index_tests.rs     list indexing and bounds checks
└── examples/
    ├── sample.licht       Section 3.5 sample program
    ├── factorial.licht
    ├── even_odd.licht
    ├── type_error.licht
    └── data.txt
```

The dependency edges follow the pipeline direction. An arrow means "uses types or functions from":

```text
src/main.rs ──────────────> src/lib.rs (run_source_to_stdout, repl::run)
src/lib.rs ───────────────> src/{lexer,parser,checker,interpreter,ast,types,error,repl}.rs
src/lexer.rs ─────────────> src/error.rs
src/parser.rs ────────────> src/{lexer,ast,types,error}.rs
src/checker.rs ───────────> src/{ast,types,error}.rs
src/interpreter.rs ───────> src/{ast,types,error}.rs
src/repl.rs ──────────────> src/{lexer,parser,checker,interpreter,ast,error}.rs
src/ast.rs ───────────────> src/{types,error}.rs
src/types.rs, src/error.rs > standard library only
```

`src/lib.rs` exposes `compile`, `run_source`, and `run_source_to_stdout`. `compile` runs the lexer, parser, and checker; `run_source` and `run_source_to_stdout` then create an `Interpreter`. `src/main.rs` reads a file (or starts the REPL) and calls the library. `tests/chapter4_tests.rs` drives the public lexer, parser, checker, and interpreter APIs directly. The four legacy files (`scanner.rs`, `chunk.rs`, `compiler.rs`, `vm.rs`) are an earlier bytecode experiment; because `src/lib.rs` does not declare them as modules, Cargo does not compile them and the pipeline does not use them.

### 4.2.2 Lexer

The lexer is a hand-written scanner that reads the source one character at a time and groups characters into the token categories listed in Section 3.4.1: keywords, identifiers, integer, float, string and Boolean literals, operators, punctuation, and delimiters. Whitespace and comments are recognised and discarded so that the parser never sees them (Ierusalimschy et al., 2025).

Several operators share a first character with a longer operator: `=` and `==`, `-` and `->`, `!` and `!=`, `<` and `<=`, `>` and `>=`. The scanner therefore looks at the next character before it chooses a token, so `==` is one comparison token and not two assignment tokens (Reis, 2011; Thain, 2023). Float literals require digits on both sides of the decimal point, which keeps `3.14` and `3` unambiguous. Invalid characters and malformed elements, such as an unterminated string literal, are reported by the lexer before parsing starts.

Listing 4.1 shows the operator and punctuation branch of `pub fn lex` in `src/lexer.rs`. The scanner reads the current character `c`, looks at `next`, and uses one `match` to choose both the token and how many characters it consumes.

```rust
// src/lexer.rs — operator and punctuation branch of pub fn lex (excerpt)
while i < chars.len() {
    let c = chars[i];
    let pos = Pos::new(line, col);

    // ... whitespace, comment, number, identifier and string branches ...

    // operators and punctuation: look at the next character to tell
    // `=` from `==`, `-` from `->`, `!` from `!=`, and so on
    let next = if i + 1 < chars.len() {
        Some(chars[i + 1])
    } else {
        None
    };
    let (tok, len) = match (c, next) {
        ('=', Some('=')) => (Tok::EqEq, 2),
        ('=', _) => (Tok::Assign, 1),
        ('!', Some('=')) => (Tok::Ne, 2),
        ('!', _) => (Tok::Bang, 1),
        ('<', Some('=')) => (Tok::Le, 2),
        ('<', _) => (Tok::Lt, 1),
        ('>', Some('=')) => (Tok::Ge, 2),
        ('>', _) => (Tok::Gt, 1),
        ('&', Some('&')) => (Tok::AndAnd, 2),
        ('|', Some('|')) => (Tok::OrOr, 2),
        ('-', Some('>')) => (Tok::Arrow, 2),
        ('-', _) => (Tok::Minus, 1),
        ('+', _) => (Tok::Plus, 1),
        ('*', _) => (Tok::Star, 1),
        ('/', _) => (Tok::Slash, 1),
        ('%', _) => (Tok::Percent, 1),
        (',', _) => (Tok::Comma, 1),
        (':', _) => (Tok::Colon, 1),
        (';', _) => (Tok::Semi, 1),
        ('(', _) => (Tok::LParen, 1),
        (')', _) => (Tok::RParen, 1),
        ('[', _) => (Tok::LBracket, 1),
        (']', _) => (Tok::RBracket, 1),
        ('{', _) => (Tok::LBrace, 1),
        ('}', _) => (Tok::RBrace, 1),
        ('&', _) => return Err(lex_error("invalid character '&' (did you mean '&&'?)", pos)),
        ('|', _) => return Err(lex_error("invalid character '|' (did you mean '||'?)", pos)),
        _ => return Err(lex_error(format!("invalid character '{}'", c), pos)),
    };
    tokens.push(Token { tok, pos });
    i += len;
    col += len;
}
```

**Listing 4.1:** Operator and punctuation branch of the lexer, `src/lexer.rs` (excerpt).

### 4.2.3 Parser and Abstract Syntax Tree

The parser is a hand-written recursive-descent parser that follows the grammar in Section 3.4.2 (Reis, 2011). Each level of the expression grammar is one function that calls the level below it: assignment, logical or, logical and, comparison, term, factor, unary, postfix, and primary. This gives the precedence order `!` > `&&` > `||` and the usual arithmetic precedence without special-case code.

Statement parsing handles `let` declarations, `if` and `else`, `for ... in` loops, `return`, and expression statements. A block may end with an expression whose value becomes the value of the block, which is how a function such as `add` returns `x + y` without a `return` keyword.

The parser builds AST nodes while it recognises the grammar. The node kinds are Rust enums for declarations, statements, and expressions, with associated data for operators, operands, arguments, and bodies (Klabnik & Nichols, 2023); `Expr` pairs an `ExprKind` with its source position. The expression `x + 2 * 3` therefore becomes an addition node whose right child is a multiplication node, which makes precedence explicit for the type checker and evaluator (Appel, 1997).

Listing 4.2 shows the expression node from `src/ast.rs` and the term level from `src/parser.rs`. `parse_term` passes `Parser::parse_factor` to the shared `binary_level` helper, so multiplication and division bind more tightly than addition and subtraction.

```rust
// src/ast.rs — expression node and binary variant (excerpt)
pub struct Expr {
    pub kind: ExprKind,
    pub pos: Pos,
}

pub enum ExprKind {
    // Int, Float, Bool, Str, List, Var, Unary, Index, Assign, Call and If
    // variants are declared here as well
    Binary {
        op: BinOp,
        left: Box<Expr>,
        right: Box<Expr>,
    },
}
```

```rust
// src/parser.rs — addition and subtraction level, and its helper (excerpt)
fn parse_term(&mut self) -> PResult<Expr> {
    self.binary_level(
        Parser::parse_factor,
        &[(Tok::Plus, BinOp::Add), (Tok::Minus, BinOp::Sub)],
    )
}

fn binary_level(
    &mut self,
    next: fn(&mut Parser) -> PResult<Expr>,
    ops: &[(Tok, BinOp)],
) -> PResult<Expr> {
    let mut left = next(self)?;
    loop {
        let mut matched: Option<BinOp> = None;
        for (tok, op) in ops.iter() {
            if self.check(tok) {
                matched = Some(*op);
                break;
            }
        }
        match matched {
            Some(op) => {
                let pos = self.here();
                self.advance();
                let right = next(self)?;
                left = Expr {
                    kind: ExprKind::Binary {
                        op,
                        left: Box::new(left),
                        right: Box::new(right),
                    },
                    pos,
                };
            }
            None => break,
        }
    }
    Ok(left)
}
```

**Listing 4.2:** AST expression node and the term level of the parser, `src/ast.rs` and `src/parser.rs` (excerpt).

### 4.2.4 Type Checker

The type checker is a separate recursive pass over the AST that runs before any code is evaluated (Pierce, 2002). It keeps a type environment that maps visible variables and functions to types. For a `let` declaration it determines the type of the assigned expression and records it against the variable name, so `let total = add(3, 4);` records `total` as `int`. Function parameters come from explicit annotations, and the return type is either annotated or `unit` when the `-> type` part is omitted: the parameters are placed in a function-local environment, and the final value of the body is checked against the declared return type.

Operator and condition rules follow Section 3.4.4. Arithmetic operators require numeric operands, comparisons produce `bool`, `&&`, `||`, and `!` require `bool` operands, and an `if` condition must be `bool`. Ordinary calls are checked by comparing the number and types of the arguments with the declared parameters.

`map()` and `filter()` have dedicated rules instead of ordinary signatures. For `map()`, the function's parameter type must match the collection's element type, and the result is a collection of the function's return type. For `filter()`, the function must accept the element type and return `bool`, and the result keeps the original element type. Element types are tracked internally, so no generic list type appears in the language. When any check fails, the checker reports an error and the evaluator is not invoked.

Listing 4.3 shows how a `let` declaration records an inferred type and how the shared `map`/`filter` rule in `check_call` compares the function's parameter type with the element type of the collection.

```rust
// src/checker.rs — let inference (excerpt from check_stmt)
Stmt::Let { name, init, pos } => {
    let t = self.check_expr(init)?;
    if t == Type::Unit || t == Type::Never {
        return type_err(
            format!("cannot store a value of type {} in variable '{}'", t, name),
            *pos,
        );
    }
    self.declare(name, t);
    Ok(Type::Unit)
}
```

```rust
// src/checker.rs — shared map()/filter() rule (excerpt from check_call)
"map" | "filter" => {
    self.expect_arity(name, args.len(), 2, pos)?;
    let ft = self.check_expr(&args[0])?;
    let lt = self.check_expr(&args[1])?;
    let (params, ret) = match ft {
        Type::Func(p, r) => (p, *r),
        other => {
            return type_err(
                format!(
                    "the first argument of {} must be a function, found {}",
                    name, other
                ),
                args[0].pos,
            );
        }
    };
    if params.len() != 1 {
        return type_err(
            format!(
                "{} needs a function with exactly one parameter, but this one takes {}",
                name,
                params.len()
            ),
            args[0].pos,
        );
    }
    let elem = match lt {
        Type::List(inner) => *inner,
        other => {
            return type_err(
                format!(
                    "the second argument of {} must be a list, found {}",
                    name, other
                ),
                args[1].pos,
            );
        }
    };
    if !compatible(&params[0], &elem) {
        return type_err(
            format!(
                "{} cannot apply a function that takes {} to a list of {}",
                name, params[0], elem
            ),
            args[0].pos,
        );
    }
    if name == "map" {
        if ret == Type::Unit || ret == Type::Never {
            return type_err("map needs a function that returns a value", args[0].pos);
        }
        Ok(Type::List(Box::new(ret)))
    } else {
        if ret != Type::Bool {
            return type_err(
                format!(
                    "filter needs a function that returns bool, but this one returns {}",
                    ret
                ),
                args[0].pos,
            );
        }
        Ok(Type::List(Box::new(elem)))
    }
}
```

**Listing 4.3:** `let` inference and the `map()`/`filter()` rule in the type checker, `src/checker.rs` (excerpt).

### 4.2.5 Evaluator

The evaluator walks the checked AST recursively and computes the value of each node (Nystrom, 2021). A stack of runtime environments maps names to values. Literals return their stored values, variable references are looked up in the environment, and binary expressions evaluate both operands before applying the operator. An `if` expression evaluates its condition and then only the selected branch.

A call evaluates its arguments and binds them to the parameters in a new environment before the function body runs. The last expression of the body is the implicit return value, and `return` exits early. Named functions are runtime values, so `is_even` and `double` can be passed to `map()` and `filter()`, which the evaluator implements as built-in operations. A `for i in range(0, 10)` loop obtains the values of the range and runs the body once for each value.

Listing 4.4 shows how a call replaces the active scope stack with a fresh frame, binds the arguments to the parameters, evaluates the body, and turns an early return into the value of the call.

```rust
// src/interpreter.rs — user-defined function call (excerpt)
fn call_function(&mut self, name: &str, args: Vec<Value>, pos: Pos) -> EvalResult<Value> {
    let func = match self.funcs.get(name) {
        Some(f) => Rc::clone(f),
        None => return Err(rt_error(format!("undefined function '{}'", name), pos)),
    };
    if self.depth >= MAX_DEPTH {
        return Err(rt_error("recursion is too deep (stack overflow)", pos));
    }
    // a call gets a fresh environment holding only its parameters (no closures)
    let mut frame: HashMap<String, Value> = HashMap::new();
    for (param, value) in func.params.iter().zip(args.into_iter()) {
        frame.insert(param.name.clone(), value);
    }
    let saved = std::mem::replace(&mut self.scopes, vec![frame]);
    self.depth += 1;
    let result = self.exec_block(&func.body);
    self.depth -= 1;
    self.scopes = saved;
    let value = match result {
        Ok(v) => v,
        Err(Flow::Return(v)) => v,
        Err(other) => return Err(other),
    };
    if func.ret == Type::Unit {
        Ok(Value::Unit)
    } else {
        Ok(value)
    }
}
```

**Listing 4.4:** Function call in the evaluator, `src/interpreter.rs` (excerpt).

`read_file` and `write_file` call the Rust standard-library file operations. Any failure from those calls is converted into an interpreter-level runtime error (Klabnik & Nichols, 2023).

### 4.2.6 Error Reporting

Each stage reports its own category of error: lexical, syntax, type, or runtime. The category tells the programmer which stage rejected the program, and the message names the source construct involved so it can be located, as required by the error-clarity requirement in Section 3.2.3. Because type checking happens first, type errors never surface as runtime failures. The runtime category is left for failures that depend on the environment, such as a file that cannot be read, and for arithmetic failures such as division by zero.

Listing 4.5 shows the single error type that all four stages share. The category is stored with a source position (`Pos`, holding a line and a column) and the message, so every error is reported in the same form; `render` also prints the offending source line with a caret.

```rust
// src/error.rs — shared error type and report format (excerpt)
pub enum ErrorKind {
    Lexical,
    Syntax,
    Type,
    Runtime,
}

impl ErrorKind {
    pub fn label(&self) -> &'static str {
        match self {
            ErrorKind::Lexical => "Lexical error",
            ErrorKind::Syntax => "Syntax error",
            ErrorKind::Type => "Type error",
            ErrorKind::Runtime => "Runtime error",
        }
    }
}

pub struct Pos {
    pub line: usize,
    pub col: usize,
}

pub struct LichtError {
    pub kind: ErrorKind,
    pub message: String,
    pub pos: Pos,
}

impl fmt::Display for LichtError {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(
            f,
            "{} at line {}, column {}: {}",
            self.kind.label(),
            self.pos.line,
            self.pos.col,
            self.message
        )
    }
}

impl LichtError {
    pub fn render(&self, source: &str) -> String {
        let mut out = self.to_string();
        if let Some(text) = source.lines().nth(self.pos.line.saturating_sub(1)) {
            let number = self.pos.line.to_string();
            let pad = " ".repeat(number.len());
            let caret_pad = " ".repeat(self.pos.col.saturating_sub(1));
            out.push_str(&format!(
                "\n {} | {}\n {} | {}^",
                number, text, pad, caret_pad
            ));
        }
        out
    }
}
```

**Listing 4.5:** Error type and its report format, `src/error.rs` (excerpt).

## 4.3 Testing

Each stage of Licht is tested on its own before it is integrated with its neighbours, following the incremental method of Section 3.1 (Nystrom, 2021; Appel, 1997). Every test case has one expected outcome, either a specific output or a specific error category, and an observed result that differs from it counts as a failure. The cases in Sections 4.3.2 to 4.3.6 are implemented in `tests/chapter4_tests.rs`, which calls the library through `licht::run_source` and the public stage APIs. The remaining test files cover the REPL (`tests/repl_tests.rs`), variadic `print` (`tests/print_tests.rs`), and list indexing (`tests/index_tests.rs`).

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

Every functional requirement in Section 3.2.2 is covered by at least one test case from Section 4.3. The table groups the requirements (FR1 to FR22, in the order of Section 3.2.2) and lists the cases that exercise them.

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

The non-functional requirements of Section 3.2.3 are assessed by inspection of the design and code rather than by test cases:

- **Simplicity, manageability, and portability:** the core uses only the Rust standard library, with no external crate, one module per stage, and small shared modules for the AST, types, and errors (Section 4.2.1).
- **Predictable type behaviour:** cases T3 to T13 show that type errors are found before evaluation begins.
- **Usability:** the sample program in Section 3.5 uses conditionals, loops, `map()`, `filter()`, and file input and output directly, without building them from lower-level primitives.
- **Error clarity:** every error belongs to one of four named categories and names the construct involved (Section 4.2.6).
- **Extensibility:** lexing, parsing, type checking, and evaluation are separate modules, so a new feature touches only the stages it needs.

## 4.5 Chapter Summary

This chapter described the implementation of the Licht interpreter as four pipeline modules (lexer, parser, type checker, and evaluator), supported by the AST, type, error, and REPL modules, written in Rust with only the standard library. It then set out 48 test cases: 10 for the lexer, 11 for the parser, 13 for the type checker, 10 for the evaluator, and 4 integration cases.

The test cases cover all 22 functional requirements of Chapter Three, and the non-functional requirements are addressed by the module structure and the four error categories.

## References

Appel, A. W. (1997). *Modern compiler implementation in ML*. Cambridge University Press. https://doi.org/10.1017/CBO9780511811449

Graham, D. (1989). Incremental development: Review of nonmonolithic life-cycle development models. *Information and Software Technology, 31*(1), 7–20. https://doi.org/10.1016/0950-5849(89)90049-9

Ierusalimschy, R., de Figueiredo, L. H., & Celes, W. (2025). *Lua 5.4 reference manual*. Lua.org. https://www.lua.org/manual/5.4/manual.html

Klabnik, S., & Nichols, C. (2023). *The Rust programming language* (2nd ed.). No Starch Press. https://nostarch.com/rust-programming-language-2nd-edition

Louden, K. C. (1997). *Compiler construction: Principles and practice*. PWS Publishing. (Preface available at https://www.cs.sjsu.edu/faculty/louden/cmptext/preface.htm)

Nystrom, R. (2021). *Crafting interpreters*. https://craftinginterpreters.com/

Pierce, B. C. (2002). *Types and programming languages*. MIT Press. https://mitpress.mit.edu/9780262162098/types-and-programming-languages/

Reis, A. J. D. (2011). Recursive-descent parsing. In *Compiler construction using Java, JavaCC, and Yacc*. Wiley. https://doi.org/10.1002/9781118112762.ch9

Thain, D. (2023). *Introduction to compilers*. University of Notre Dame. https://www3.nd.edu/~dthain/compilerbook/compilerbook.pdf
