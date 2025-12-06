## Most Important Regions for writing Rust Code

- The Stack
- The Heap
- Static Memory

### Stack

Every time a fn is called, a continous chunk of memory called *frame* is allocated on top of the stack.  
That frame contains all the variable & parameters of that function.

When function returns, the stack frame is return to the OS. The bytes that makes up yhe values of function's  
local variables are not immidietly wipes, but it can be overwrite by other functions. It is illegal to use, even if it is not overwritten.  

Practically, **Stack = Lifetime**

### Heap

Value in heap exists till they are deallocated.