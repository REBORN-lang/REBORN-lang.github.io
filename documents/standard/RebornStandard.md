# Reborn Standard (RS)

### Revision 25012

## Purpose

The **Reborn Standard** exists to define how **Reborn** source code **must** be written and interpreted. \
It enforces style, grammar, and structural rules that all **Reborn** programs must follow. \
This standard exists to ensure consistency and eliminate ambiguity in compiler implementation and user code.

---

# R1. General Syntax Rules

- Semicolon (`;`) is required to terminate any expression or instruction.
- Curly braces (`{}`) must be used for all blocks, even one-liners.
- One statement per line is _not mandatory but recommended_.
- Tabs or 4 spaces are accepted for indentation (_not significant to parser_).
- Function bodies _should_ always have at least one `return`, with the only exception being `void`-types. (_Styling recommendation_)
- Reborn is not an Object Oriented Programming Language and it does not plan to support OO features
  in the foreseeable future.
- 4 space indentation is _recommended_ for writing **Reborn** programs.

---

# R1.1 General style recommendations and formatting rules

This section is mixed with:

- Formatting **Rules**: If not met, the compiler **must** throw an error.
- Styling _recommendations_: If not met, the **Reborn Standard** purists may call your code ugly, but it will work nonetheless.

Abiding to **all** of this rules is **highly** recommended because it \
provides clarity and consistency between programs written in Reborn.

### Regarding section R4.1:

Formatting **rules** and Styling _recommendations_:

- **One space between `let` and identifier.**
- _One space between identifier and `:=`._
- _One space between `:=` and the expression._ \
  And once again, just to clarify: these rules **do not mean** that you cannot do something like `let var:=10;` \
  Also, with "one space between x and y" it means they must be **separated**, the character \
  that is **recommended** for separating these **x** and **y** is a space, but it can \
  obviously be other things too, like a `/**/` comment for example, even though that\
  is **not recommended** by any means, since it may cause unclarity.

### Regarding section R5:

Formatting **rules** and Styling _recommendations_:

- **Functions must be defined with a block** (`let identifier: type = (parameter: type) { ... }`).
- **Functions must be declared without a block** (`let identifier: type = (parameter: type);`). \
  _Namely "forward declaration"_
- **Overloading is allowed by type.** <!-- Consider: More of a feature than a rule though -->

### Regarding section R5.1:

Formatting **rules** and Styling _recommendations_:

- _Function name followed immediately by `(`_ (_no space before `(`_).
- **Parameters must be explicited with: `: type`**
- _No space before `:`_
- _One space follows `:` before the `type`_
- _Parameters separated by `, `_ (_`,` then a single space_)
- **No semicolon after the closing `}` of a function definition (functions are blocks).**
- **Function body must have at least one `return` statement (unless the function is explicitly declared as `void`).**

---

# R2. File Structure & Entry Point

- All Reborn source files must contain at most one `main()` function (_entry point_).

- The entry point, _per standard_ named `main()` does **not require** the `let` keyword in its definition.

- Entry point does not require a declared `return` type. \
  Example:
  
  ```
  // Implicitly of type void
  main := () {
      return;
  }
  ```

- The entry point can also be defined as `main: void() { ... }`

- If present, `main()` should be the last global definition in the file. \
  \#Clarification: with _"if present"_ we mean that in specific instances like header files you don't _need_ an entry point.

---

# R3. Keywords

- Reserved keywords (__See all Reborn keywords at the bottom of this document_) **cannot** be used as identifiers.
- Keywords are case-sensitive (i.e., `If` ≠ `if`).
- Keywords are never re-definable through macros or aliases, _unless case-altered_.

---

# R4. Data Types & Type Inference

- All variables must be declared before use.
- Supported types: `int`, `float`, `char`, `bool`, `string`, `void`
- To declare a variable as constant add the `const` keyword at the beginning of the declaration.
  Below there are examples on variable declaration and its syntax.

---

## R4.1 Variable Declaration Syntax

The general syntax for a variable declaration is:
```
let [attributes] identifier [:= | : type =] [value | expression];
```
Below there are examples of valid forms of variable declaration.

- Untyped declaration
  
  ```
  let identifier := expression;
  // NOTE: The wording is 'untyped' but what this
  // resolves to is actually 'type inferred'
  ```
  
  Example: `let number := 10;`

- Explicitly typed declaration
  
  ```
  let identifier: type = expression;
  ```
  
  Example: `let number: int = 10;`

- Constant variable declaration
  
  ```
  let const identifier := expression;
  ```
  
  or..
  
  ```
  let const identifier: type = expression;
  ```

- Array variable declaration
  
  ```
  let identifier[]: type = {expression1, expression2, ...expression<n>};
  ```

- Examples of invalid declarations (must be rejected by compiler):
  
  ```
  let x = 99;         // missing ':=' or ': type ='
  y = 99;             // this is an assignment, not a declaration.
  ```

---

# R5. Functions

- Functions must be defined with a block (`{...}`).
- Functions must be declared without a block.
- Overloading is allowed by type.

---

## R5.1 Function Declaration & Definition Syntax

- Inferred return type
  
  ```
  let <attributes> fname := (param1: type, param2: type, ...param<n>: type) {
      <code>
      return <expr>;
  }
  ```

- Explicit return type
  
  ```
  let <attributes> fname: type = (param1: type, param2: type, ...param<n>: type) {
      <code>
      return <expr>;
  }
  ```
- Example of attributes
  ```
  let static inline func: int = (x: int) {
      return x;
  }
  ```

- Examples of invalid declarations (must be rejected):
  
  ```
  let f(a: int) { ... }    // missing ':=' before '('
  let f := (a, b) { ... }  // missing parameter type <!-- This may become possible eventually :) -->
  ```

---

# R6. Conditionals and Loops

- Conditional blocks (`if`, `elif`, `else`)'s conditions **should** always be encapsulated between parenthesis (_Note: Parenless syntax is allowed: **R6.1**_).

- `elif` must directly follow an `if` or another `elif`.

- `else` must be the final branch for a conditional group.

- It is recommended to group related branches into a single structure:
  
  ```
  if (condition) {
    ...
  } elif (other) {
    ...
  } else {
    ...
  }
  ```

- `while` and `for` loops follow classic C structure:
  
  ```
  while (condition) { ... }
  for (init; condition; post) { ... }
  ```
- `for` loop with range:
  ```
  for::n {
      ...
  }
  ```
  With `n` being an integer positive number. \
  This is syntactic sugar that will resolve to a normal C `for (int i = 0; i < n; i++)`.

---

## R6.1 Alternative conditional blocks syntax

A conditional block in C-like languages is usually similar to \
`if (condition) { ... }` \
You can also write parenless conditional blocks
that can be both cleaner and faster to write, at your preference.
Example:

```
if condition { ... }
elif condition { ... }
else { ... }
```

This also applies to loops:

```
while condition { ... }
for init; condition; post { ... } // Not recommended in 'for' loops because the semicolons may cause unclarity.
```

---

# R7. Header Imports

- Standard headers must use:
  
  ```
  import rsl;
  ```

- A single `import` statement can be used for all imported header files:   *\*recommended*
  
  ```
  import {
    rsl.rh,
    example.rh
  }
  ```

- User-defined headers may use relative paths:
  
  ```
  import "custom.rh";
  ```
  
  All `import`s **should** appear before any function or variable declarations. \
  For **standard headers** you can omit the `.rh` file extensions.

---

## R7.1 Import optimization
An important optimization technique that is standard-enforced (_**mostly recommended, not a "must"**_)
is to utilize what is called *"import selectivity"*, it is a feature of Reborn that allows for the \
developer to only import specific functions and symbols from a given header file, this allows for \
much tinier code and faster compilation times, as the compiler does not need to import the entire \
file if, for example, the programmer only uses a couple of functions. \
Example:
```
from rsl import { printf(), getline() }

main := () {
    //
}
```
This is a programmer choice and it is not enforced by the **RS**. \
Compiler implementations of Reborn may also choose to implement the _**non-standard**_ feature \
**'inclusive import selectivity'**, what this means is that, if the programmer does:
```
import rsl;
```
The compiler will automatically check for which functions are being used and only import those \
functions, this is called *inclusive* because it does not require the programmer to explicitly \
request specific functions from the header file. \
This feature is _**non-standard**_ and it is up to the compiler implementation to decide whether to \
implement it or not, for example, the <u>[**CReborn**](https://github.com/reborn-lang/creborn)</u> implementation does not include this feature.

---

# R8. Interop

- Foreign functions must be declared using extern:
  
  ```
  extern int write(int fd, string text, int len);
  ```

- And as you can see they are declared with a C-style function declaration: \
  `extern int write()` as opposed to `extern let write: int()`

You can include C snippets using:

```
extern "C" {
    // C code
}
```

Note: The first iteration of the **RSL** reimplements most of the <u>[C Standard Library](https://en.cppreference.com/w/c/header.html)</u>.

---

# R9. Compiler-Time Tools

- `sizeof(expr)` returns size in bytes of a type or value.
- `typeof(expr)` returns a `string` representation of the value's type.
- **These functions are not runtime functions; they are evaluated by the compiler**.

---

# R10. Pointers

- To declare a pointer: \
  `let *p := &x;`: The `&` operator returns the address in memory of something
- To dereference a pointer, we can utilize the `@` operator \
  `printf("%_", @p); // With %_ as its format, printf() will infer the format type`

---

# R11. Comments

- Single-line: `// this is a comment`
- Multi-line: `/* this is a longer comment */`
- Nested comments are just comments.
  Example: `/* Comment // Comment */` -> Is just one comment, _obviously_

---

# R12. Data structures
The data structures available in *Reborn* are:
- `struct`: The simplest, most *C*-like data structure there is. It is to be noted that a `struct`
  in Reborn is much more similar to a `typedef struct` in C than a plain `struct`. \
  Example:
  ```
  let struct {
      type MemberIdentifier1;
      type MemberIdentifier2;
      ...
  } StructIdentifier;
  ```
  It can also be written as
  ```
  let struct StructIdentifier {
      ...
  };
  ```
  As showcased in the examples above, a field inside a `struct` is called a **member** and it \
  is composed of a *type* and an *identifier*. \
  A value (generically, a *symbol* or a *variable*) of which type is a `struct` is called a *struct
  instance* \
  Example:
  ```
  let struct Expression {
      // We are going to use this struct to abstract a simple mathematical expression (number,
      // operator, number)
      let FirstNum: int;
      let Operator: char;
      let SecondNum: int;
  }
  
  // Now we create, or 'initialise' an instance of this Expression
  let expr: Expression;
  // And now we can assign values to the members of this instance
  expr.FirstNum = 4;
  expr.Operator = '+';
  expr.SecondNum = 4;
  ```
- `enum`: An `enum`eration type is a user-defined type that consists of a finite set of named
  constant values. \
  These *named constant values* are also called *member*s, for familiarity. \
  To be consistent on mapping *Reborn* to *C*, the underlying type for `enum`s is an `int`eger type. \
  An `enum` has *implicit values* and *explicit values*, the *implicit* ones are automatically
  assigned by the compiler whilst the *explicit* ones are specified by the programmer. \
  Example:
  ```
  // Same as with structs, you can either specify the identifier before or after the block.
  let enum Lightswitch {
      let PoweredOn,          // Implicit
      let PoweredOff := 0;    // Explicit
  };
  
  // Then we can create a symbol with this enum type we just defined
  let Switch: Lightswitch = PoweredOn;
  ```
- `union`: A type more than a data structure, an `union` is a chunk of memory that may be
  interpreted with more than one type, **one at a time**. \
  Example:
  ```
  let union Value {
      let i: int;
      let f: float;
      let s: string;
  };
  ```
  As the other data structure types, an `union` is composed of *members*, with each of its *members* \
  having a name (*identifier*) and a *type*, something that the `union` has exclusively to the other \
  two data structure types is the *union maximum size* which is an integer number given by the \
  biggest data type in bytes, throughout all of the `union`'s *members*. \
  After creating an `union` we can create an *instance* of it, just like the previous data
  structures. \
  Example:
  ```
  let val: Value;
  val.i = 8; // val is now an int
  // If we use another member of this instance's union type (i.e. 'val.f' or 'val.s')
  // we will have effectively changed the type of val, if we use the wrong type on the printf()
  // format or any other function that requires an explicit type after changing it (proactively)
  // we will cause UB.
  ```
  **Note:** Reading a union member different from the last written one is \
  permitted but may result in undefined or implementation-defined behavior.

---

# R13. Type casting
Type casting is a way to tell the compiler to represent the given data with a different type. \
Obviously not every type can be represented as each other, for example you can't represent a `float` \
with an `int` because the number will have to be rounded up to a valid integer number, this doesn't \
mean that a Reborn compiler can't be able to do that, but utilizing a dedicated function to convert \
said data's type is safer and does not risk data losses or similar issues. \
Example:
```
let letter: char = 'a';
let letterCasted: int = letter-->int; // Operator -> ≠ Operator -->
printf("%_\n", letterCasted); // Will output ASCII decimal code of said character, in our case 97 ('a')
```

---

# R14. Error Expectations

- A **Reborn** compiler should emit clear errors if:
  - A required semicolon is missing
  - An `array` is declared without size or initializer
  - A non-`void` function is missing its final `return`
  - `elif` or `else` is used without a prior `if`
  - Or more broadly if any of the **RULES** dictated by the **RS** were violated
- A **Reborn** compiler should emit clear warning for any violation of the **RS** styling rules.

---

# R15. Keywords

Here is every reserved keyword in Reborn.

### General

- `import`, `return`, `from`

- `//`, `/*`, `*/`, `;`, `,`, `'`, `"`

- ### Declarations, definitions and assignments

- `let`, `int`, `char`, `bool`, `string`, `void`

- `const`, `unsigned`, `extern`

- `:`, `=`, `:=`, `()`, `{}`, `[]`
  
  ### Conditionals

- `if`, `elif`, `else`, `for`, `while`

- `&&`, `||`, `&`, `|`
  
  ### Operators

- `=:`/`==`

- `++`, `--`, `>`, `<`, `>=`, `<=`, `!=`

- `*`, `&`, `@`

- `.`, `->`

- `-->`, `::`

<!-- Add bitwise operations + their operators -->

---

# Appendix A

Every program created by the **REBORN-lang** organization and/or its <u>[GitHub account](https://github.com/REBORN-lang)</u>, that is written in the Reborn programming language, must comply with the latest available revision of the **RS**; \
Thus enabling users to quote said code and use it as a reference on how to write standard-compliant **Reborn** code.

---

# Appendix B

Reborn source files use the `.rn` extension. \
Reborn Header files use the `.rh` extension.

---

# Appendix C

The only recognized compiler implementations of **Reborn** are specified here:
- <u>[CReborn](https://github.com/reborn-lang/creborn)</u>: Development lead by
  <u>[czjstmax](https://github.com/jstmaxlol)</u>, founder and main maintainer for REBORN-lang.

---

# Appendix D

When the word '**symbol**' is used, any *function*, *variable*, etc, etc.. is meant.

---
