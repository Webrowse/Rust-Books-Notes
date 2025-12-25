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

