# REBORN Standard (RS)
### Revision 25006

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
- Supported types: `int`, `float`, `char`, `bool`, `string`.
- To declare a variable as constant add the `const` keyword at the beginning of the declaration.
Below there are examples on variable declaration and its syntax.

## R4.1 Variable Declaration Syntax
A variable declaration must follow exactly one of these forms:
- Type inferred declaration
  ```
  let identifier := expression;
  ```
  Example: `let number := 10;` \
  \
  Formatting rules:
      - One space required between `let` and identifier.
      - One space required between identifier and `:=`.
      - One space required between `:=` and the expression.

- Explicitly typed declaration
  ```
  let identifier: type = expression;
  ```
  Example: `let number: int = 10;` \
  \
  Formatting rules:
      - One space required between `let` and identifier.
      - Exactly one space **may** follow the colon before the type (recommended).
      - Single space required around the `=` (i.e. `type = expression`).
  
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
let z = 99;         // missing ':=' or ': type ='
let b: = 6;         // missing type
```

# R5. Functions
- Functions must be defined with a block.
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
- Formatting rules (must be enforced)
    - Function name followed immediately by `(` (no space before `(`).
    - Parameters formatted as: `identifier: type` or if inferred as: `identifier`
    - No space before `:` (**OPTIONAL, MORE OF A STYLING RULE**)
    - One space may follow `:` before the `type` (recommended)
    - Parameters separated by `, ` (comma then single space)
    - No semicolon after the closing `}` of a function definition (functions are blocks).
    - Function body must end with a `return` statement (unless the function is explicitly declared as `void`).

- Examples of invalid declarations (must be rejected):
```
let f(a: int) { ... }        // missing ':=' before '('
```
<!-- TODO: ADD MORE EXAMPLES -->


# R6. Conditionals and Loops
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

## R6.1 Alternative conditional blocks syntax
A conditional block in C-like languages is usually similar to \
`if (condition) { ... }` \
But because of REBORN's fixation with being dynamic you can also write \
parenless conditional blocks that can be cleaner and faster to write.
Example:
```
if condition {
    ...
} elif condition {
    ...
} else {
    ...
}
```

# R7. Header Imports
- Standard headers must use:
```
import <RSL.rh>;
```
- User-defined headers may use relative paths:
```
import "custom.rh";
```
All `import`s must appear before any function or variable declarations.

# R8. Interop
- Foreign functions must be declared using extern:
```
extern int write(int fd, string text, int len);
```
- And as you can see they are declared with a C-style function declaration: \
  `extern int write()` instead of `extern let write: int()`

You can include C or C++ snippets using:
```
extern "C" {
    // C code
}
extern "Cpp" {
    // C++ code
}
```
(_\*Note6_)

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
  - An `array` is declared without size or initializer (_\*Note7_)
  - A non-`void` function is missing its final `return` (_\*Note3_)
  - `elif` or `else` is used without a prior `if`
- A **REBORN** compiler should emit clear warning for any violation of the **RS** styling rules.

# Appendix A
- Every program created by the REBORN-lang organization and/or its GitHub account **must** comply with the latest **RS**
