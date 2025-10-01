# REBORN Standard (RS)
### Version 25005

## Purpose
The **REBORN Standard** exists to define how **REBORN** source code **must** be written and interpreted. \
It enforces style, grammar, and structural rules that all **REBORN** programs must follow. \
This standard exists to ensure consistency and eliminate ambiguity in compiler implementation and user code.

# R1. General Syntax Rules

- Semicolon (`;`) is required to terminate any expression or instruction.
- Curly braces (`{}`) must be used for all blocks, even one-liners.
- One statement per line is _not mandatory but recommended_.
- Tabs or 4 spaces are accepted for indentation (_not significant to parser_).
- Function bodies must always end in a `return`, with the only exception being `void`-types. (_\*Note3_)
- The `array` size must be explicitly specified at declaration. (_\*Note7_)
- **No object-oriented syntax is allowed.** (_\*NoteOOP_)
- 4 characters tab indentation is _recommended_ for writing **REBORN** programs.

# R2. File Structure & Entry Point
- All `.re` source files must contain at most one `main()` function (_entry point_).
- Entry point does not require a declared `return` type. \
Example:
```
main() {
    return;
}
```
- If present, `main()` should be the last global definition in the file.

# R3. Keywords
- Reserved keywords (See **Sk.** in the **REBORN** Design) must not be used as identifiers.
- Keywords are case-sensitive (i.e., `If` ≠ `if`).
- Keywords are never re-definable through macros or aliases.

# R4. Data Types & Type Inference
- All variables must be declared before use.
- Supported types: `int`, `float`, `char`, `bool`, `string`, `let` (_inferred_).
- `let` must be assigned at declaration and must never hold array literals. (_\*Note5_)
- To declare a variable as constant add the `const` keyword at the beginning of the declaration.

## R4.1 Variable Declaration Syntax
A variable declaration must follow exactly one of these forms:
- Type inferred declaration
    `let identifier := expression;`
    Example:
    `let number := 10;`
    Formatting rules:
     - One space required between `let` and identifier.
     - One space required between identifier and `:=`.
     - One space required between `:=` and the expression.

- Explicitly typed declaration
    `let identifier: type = expression;`
    Example:
    `let number: int = 10;`
    Formatting rules:
     - One space required between `let` and identifier.
     - **no space** allowed before the colon (i.e. identifier immediately followed by `:`).
     - Exactly one space **may** follow the colon before the type (recommended).
     - Single space required around the `=` (i.e. `type = expression`).

- Constant variable declaration
    `const let identifier := expression;`
    or..
    `const let identifier: type = expression;`

- Examples of invalid declarations (must be rejected by compiler):
```
let z = 99;         // missing ':=' or ': type ='
let a : int = 5;    // illegal space before colon
let b: = 6;         // missing type
```

# R5. Arrays
- Declared as `type array[] = [data];`
- You must specify size explicitly if `array` is declared without initializer. (_\*Note7_)
- You may use `char`acter arrays for `string` literals (Equivalent of C-style `string` in C++).

# R6. Functions
Syntax:
```
type function(type param1, type param2) {
    <code>
    return (result);
};
```
- Functions **must** always end with a `return` (even if returning nothing).
- Overloading is allowed by type.
- `let` may be used in place of `return` type for compile-time inference (_\*Note4_), but is not mandatory.
- Forward declarations (C-like) are not yet supported (_\*Note1_).

# R7. Conditionals and Loops
- Conditional blocks (`if`, `elif`, `else`) must always use braces.
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
`while` and `for` loops follow classic C structure:
```
while (condition) { ... }
for (init; condition; post) { ... }
```

# R8. Header Imports
- Standard headers must use:
```
include <RSL.rh>;
```
- User-defined headers may use relative paths:
```
include "custom.rh";
```
All `include`s must appear before any function or variable declarations.

# R9. Interop
- Foreign functions must be declared using extern:
```
extern int write(int fd, string text, int len);
```
You can include C or C++ snippets using:
```
extern C {
    // C code
}
extern Cpp {
    // C++ code
}
```
(_\*Note6_)

# R10. Compiler-Time Tools
- `sizeof(expr)` returns size in bytes of a type or value.
- `typeof(expr)` returns a `string` representation of the value's type.
- **These functions are not runtime functions; they are evaluated by the compiler**.

# R11. Comments
- Single-line: `// this is a comment`
- Multi-line: `/* this is a longer comment */`
- Comments **cannot** be nested.

# R12. Error Expectations
- A **REBORN** compiler should emit clear errors if:
  - A required semicolon is missing
  - A `let[]` declaration is attempted (_\*Note5_)
  - An `array` is declared without size or initializer (_\*Note7_)
  - A function is missing its final `return` (_\*Note3_)
  - `elif` or `else` is used without a prior `if`
- A **REBORN** compiler should emit clear warning for any violation of the **RS** styling rules.
