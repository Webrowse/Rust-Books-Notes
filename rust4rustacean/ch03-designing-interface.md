# Designing Interfaces

Core idea is that APIs are everywhere in the rust (internal and external).  
a simple philosophy : 
> Easy to use and hard to break

## There are 4 basic Principles
* Unsurprising
* Flexible
* Obvious
* Constrained

### 1. Unsurprising 

The goal is to make your interface intuitive so that users can guess how it works correctly.  
*Naming Practices*: iter() takes &self, and into_inner() takes the ownership of self.   
*Common Trait*: Users expected certain traits to 'just work'. You should almost always implement Debug, Send and Sync (Clone & Default) unless you want to exclude it delibrately.  
*Ergomonic Implementation*: Rust doesn't automatically works for references even if the base type implements it.  
Consider providing implementations for &T if T implements the traits. It makes the API smooth.  
*Deref for wrappers*: if you create a wrapper type, implement *Deref* allows user to call the inner type directly.  

### 2. Flexible

Your code should avoid unnecessary restrictions on the callers.  
- *Generic Arguments*: Use generics to allow different input types. For example, taking impl AsRef<str> is more flexible than taking a concrete &str because it accepts more types.  
- *Generics vs. Trait Objects*: Generics (static dispatch) are faster but can bloat binary size. Trait objects (&dyn Trait) can keep code cleaner and reduce compile times, but they require the trait to be object-safe.  
- *Borrowed vs. Owned*: If your function doesn't need to own data, use a reference. If it's unclear, the Cow (Clone-on-Write) type is useful for handling both borrowed and owned data efficiently.

### 3. Obvious
An interface should be hard to use incorrectly and its behavior should be clear.  
- *Documentation*: Clearly document cases where the code might panic or return an error, and provide end-to-end examples.  
- *Semantic Typing*: Use specific types instead of primitives to prevent mistakes. For example, using a custom enum is better than using three bool arguments in a row, which a user could easily swap by accident.  
- *Marker Types*: You can use zero-sized types and generics to enforce rules at compile time. An example is a Rocket type that can only call launch() when it is in the Grounded state.  
- *Must Use*: Use the #[must_use] attribute on types like Result to warn users if they forget to handle a return value.

### 4. Constrained
A well-designed API limits what the user can do to ensure you can safely change the internals later.  
- Visibility: Use pub(crate) to keep types internal unless they absolutely need to be public.  
- Non-exhaustive: Use the #[non_exhaustive] attribute on structs or enums. This allows you to add new fields or variants in the future without breaking the user's code.  
- Sealed Traits: If you want to define a trait that users can use but cannot implement themselves, use a "sealed trait". This is done by adding a private "supertrait" that the user cannot access.  
- Auto-Traits: Be careful with Send and Sync; adding a non-thread-safe private field to a public struct can accidentally remove these traits from your public API, which is a breaking change