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

# 2. Procedural Macros
These are more powerful and act like a source code preprocessor. You write a separate Rust function that takes in tokens, performs logic on them, and spits out new tokens.  
- Three Types:
    1. Function-like: Called like a function (my_macro!()).  
    2. Attribute: Attached to an item to transform it (like #[tokio::main]).  
    3. Derive: Adds new code after a struct or enum (like #[derive(Serialize)]).   
- The Cost: They can increase compile times because they often require heavy libraries (like syn for parsing) and generate a lot of hidden code that the compiler still has to process.
- Hygiene: Unlike declarative macros, these are not hygienic by default. You must use Spans to manually tell the compiler which parts of the generated code belong to which scope.