# REBORN Standard (RS)
### Revision 25008

## Purpose
The **REBORN Standard** exists to define how **REBORN** source code **must** be written and interpreted. \
It enforces style, grammar, and structural rules that all **REBORN** programs must follow. \
This standard exists to ensure consistency and eliminate ambiguity in compiler implementation and user code.

# R1. General Syntax Rules
- Semicolon (`;`) is required to terminate any expression or instruction.
- Curly braces (`{}`) must be used for all blocks, even one-liners.
- One statement per line is _not mandatory but recommended_.
- Tabs or 4 spaces are accepted for indentation (_not significant to parser_).
- Function bodies _should_ always have at least one `return`, with the only exception being `void`-types. (_Styling recommendation_)
- Reborn is an Object Oriented Programming Language.
- 4 characters tab indentation is _recommended_ for writing **REBORN** programs.

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

# R2. File Structure & Entry Point
- All `.rn` source files must contain at most one `main()` function (_entry point_).
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
\#Clarification: with _"if present"_ we mean that in specific cases like `.rh` header files you don't _need_ a `main()` entry point.

# R3. Keywords
- Reserved keywords (__See all Reborn keywords at the bottom of this document_) **cannot** be used as identifiers.
- Keywords are case-sensitive (i.e., `If` ≠ `if`).
- Keywords are never re-definable through macros or aliases, _unless case-altered_.

# R4. Data Types & Type Inference
- All variables must be declared before use.
- Supported types: `int`, `float`, `char`, `bool`, `string`.
- To declare a variable as constant add the `const` keyword at the beginning of the declaration.
Below there are examples on variable declaration and its syntax.

## R4.1 Variable Declaration Syntax
A variable declaration must follow exactly one of these forms:
- Type inferred declaration
  ```
  let identifier := expression;
  ```
  Example: `let number := 10;`
  
- Explicitly typed declaration
  ```
  let identifier: type = expression;
  ```
  Example: `let number: int = 10;`
  
- Constant variable declaration
  ```
  const let identifier := expression;
  ```
  or..
  ```
  const let identifier: type = expression;
  ```
- Array variable declaration
  ```
  let identifier[]: type = {expression1, expression2};
  ```

- Examples of invalid declarations (must be rejected by compiler):
  ```
  let x = 99;         // missing ':=' or ': type ='
  y = 99;             // assignment, not a declaration.
  ```

# R5. Functions
- Functions must be defined with a block (`{...}`).
- Functions must be declared without a block.
- Overloading is allowed by type.

## R5.1 Function Declaration & Definition Syntax
- Inferred return type
```
let fname := (param1: type, param2: type) {
    <code>
    return <expr>;
}
```

- Explicit return type
```
let fname: type (param1: type, param2: type) {
    <code>
    return <expr>;
}
```

- Examples of invalid declarations (must be rejected):
```
let f(a: int) { ... }    // missing ':=' before '('
let f := (a, b) { ... }  // missing parameter type
```


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

# R7. Header Imports
- Standard headers must use:
```
import rsl;
```
- A single `import` statement must be used for all imported header files:
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

# R8. Interop
- Foreign functions must be declared using extern:
```
extern int write(int fd, string text, int len);
```
- And as you can see they are declared with a C-style function declaration: \
  `extern int write()` as opposed to `extern let write: int()`

You can include C or C++ snippets using:
```
extern "C" {
    // C code
}
extern "Cpp" {
    // C++ code
}
```
Note: The first iteration of the **RSL** reimplements most of the [C Standard Library](https://en.cppreference.com/w/c/header.html) plus some of C++'s standard library, **excluding the STL**.

# R9. Compiler-Time Tools
- `sizeof(expr)` returns size in bytes of a type or value.
- `typeof(expr)` returns a `string` representation of the value's type.
- **These functions are not runtime functions; they are evaluated by the compiler**.

# R10. Comments
- Single-line: `// this is a comment`
- Multi-line: `/* this is a longer comment */`
- Nested comments are just comments.
Example: `/* Comment // Comment */` -> Is just one comment

# R11. Error Expectations
- A **REBORN** compiler should emit clear errors if:
  - A required semicolon is missing
  - An `array` is declared without size or initializer
  - A non-`void` function is missing its final `return`
  - `elif` or `else` is used without a prior `if`
  - Or more broadly if any of the **RULES** dictated by the **RS** were violated
- A **REBORN** compiler should emit clear warning for any violation of the **RS** styling rules.

# R12. Keywords
Here is every reserved keyword in Reborn.
### General
- `import`, `return`
- `//`, `/*`, `*/`, `;`, `,`, `'`, `"`
- ### Declarations, definitions and assignments
- `let`, `int`, `char`, `bool`, `string`, `void`
- `const`, `unsigned`, `extern`
- `:`, `=`, `:=`, `()`, `{}`, `[]`
### Conditionals
- `if`, `elif`, `else`, `for`, `while`
- `&&`, `||`, `&`, `|`
### Operators
- `++`, `--`, `>`, `<`, `>=`, `<=`, `==`, `!=`

# Appendix A
Every program created by the REBORN-lang organization and/or its GitHub account, that is written in Reborn, must comply with the latest available revision of the **RS**;
Thus enabling users to quote said code and use _that_ as a reference on how to write standard-compliant REBORN code.

# Appendix B
REBORN source files use the `.rn` extension. \
REBORN Header files use the `.rh` extension.
