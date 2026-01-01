# Ch07 - Macros

explains how to use macros make the compiler write code for you. In Rust, macros are tools for automatic code substitution, where you define rules for how the compiler should replace a macro call with actual source code.  

## 1. Declarative Macros (`macro_rules!`)
These are the most common macros. They act like a compiler-assisted search and replace.  
- **When to Use**: Use them to eliminate repetitive boilerplate, such as writing the same test for ten different data types or implementing a simple trait for all integer types.  
- **How They Work**: They don't just look at text, they look at tokens (the building blocks of code like numbers, punctuation, and names). The compiler uses these tokens to build a syntax tree (AST).
- Structure:  
    * Matchers: The "if" part that looks for a specific pattern in your input.  
    * Transcribers: The "then" part that defines what code should be generated.
- **Hygiene**: Declarative macros are hygienic, meaning they live in their own "universe". They cannot accidentally use or overwrite variables from the code where they are called unless you explicitly pass those variables in.  
- **Scoping**: They follow "textual scoping," meaning you must define a macro before you can use it in a file.