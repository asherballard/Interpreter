# Java-Style Language Interpreter

An interpreter, written in Racket, for a small object-oriented language with Java-like syntax. It supports classes and inheritance, instance and static methods, nested functions, loops, and exceptions with `try`/`catch`/`finally`. All control flow is implemented in continuation-passing style.

Built for CSDS 345: Programming Language Concepts at Case Western Reserve University.

## Example

```java
class List {
  var val;
  var next;

  function getNext() {
    return next;
  }

  function setVal(x) {
    val = x;
  }

  function setNext(x) {
    if (x == 0)
      next = 0;
    else {
      next = new List();
      next.setVal(val + 1);
      next.setNext(x - 1);
    }
  }

  static function main() {
    var l = new List();
    l.setVal(10);
    l.setNext(5);
    return l.getNext().getNext().getNext().getNext().getNext().val;
  }
}
```

Interpreting this program returns `15`: the value returned by `main`.

## Language features

| Area | Supported |
|---|---|
| Classes | Fields with optional initializers, `new`, dot access (`obj.field`, `obj.method()`) |
| Inheritance | `extends`, `this`, `super`, and inherited fields and methods |
| Methods | Instance and `static` methods, recursion, nested functions that close over enclosing variables |
| Control flow | `if`/`else`, `while`, `break`, `continue`, `return`, blocks `{ }` |
| Exceptions | `throw`, `try`/`catch`/`finally`, including exceptions thrown across method calls |
| Operators | Arithmetic `+ - * / %`, comparison `== != < <= > >=`, boolean `&& \|\| !` |

## How it works

1. **Parsing:** `classParser.rkt` and `lex.rkt` turn source code into a parse tree of nested lists.
2. **Class declarations:** `Interpreter.rkt` loads each class's fields and methods into the program state, then calls `main`.
3. **Evaluation:** `evaluators.rkt` walks the parse tree. Every statement takes continuations for `return`, `break`, `continue` and `throw`, so jumps like an exception escaping several method calls are just calls to the right continuation, not Racket exceptions.
4. **State:** `state.rkt` keeps a stack of scope layers (bindings from names to values) plus class and object information. Each new block or function call pushes a layer. Object fields are stored in boxes, so every reference to an object sees the same updates.

| File | Role |
|---|---|
| `Interpreter.rkt` | Entry point: `interpret` and top-level class processing |
| `evaluators.rkt` | Statement and expression evaluation (CPS) |
| `state.rkt` | Scopes, variable binding and lookup, classes and objects |
| `operators.rkt` | Mapping from operator symbols to Racket functions |
| `helpers.rkt` | Parse-tree accessor helpers |
| `classParser.rkt`, `lex.rkt` | Lexer and parser **(provided by the course)** |

## Running it

Requires [Racket](https://racket-lang.org/) (DrRacket works too).

In DrRacket, open `Interpreter.rkt`, click **Run**, then enter in the REPL:

```racket
(interpret "Test1_15.txt")
```

`main` is run from the last class declared in the file.

## Tests

Each test file's name contains the value its program should return:
`Test3_125.txt` should return `125`, and `Test13_-716.txt` should return `-716`. The 13 tests cover fields, methods, recursion, linked-list manipulation through objects, nested functions, inheritance and exceptions.

## Project history

The interpreter was built in four cumulative phases:

| Phase | Added | Built by |
|---|---|---|
| 1 | [e.g. expressions, variables, `if`, `while`] | Team: Asher Ballard, Devin Riehle, Naveen Cherukuri  |
| 2 | [e.g. blocks, `break`/`continue`, exceptions] | Team Asher Ballard, Devin Riehle, Naveen Cherukuri|
| 3 | [e.g. functions and nested functions] | Team Asher Ballard, Devin Riehle, Naveen Cherukuri|
| 4 | [e.g. classes, objects, inheritance] | Asher Ballard (solo) |

The lexer and parser (`lex.rkt`, `classParser.rkt`) were provided by the course instructors.
