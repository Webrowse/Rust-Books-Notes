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
Primary way is `Box::new(value)`, you get back a `Box<T>` pointer.  
when box is dropped, that memory is freed.  
If you forget to deallocate the memory in Heap, your app will eventually eat up all the memory on your machine,  
called 'leaking memory'.  
we can leak memory explicitly in some usecase, for e.g. for read-only config that is required by whole program,  
`Box::leak` will get a `'static` reference to it.  

### Static 

These region automatically loaded in the memory when program is called.  
Static memory lives throughout the dureation of the program. It contain the program's binary code.  
`'static` variable is allocated at the start of the program & is not deallocated before program shuts down.   
It is useful in trait bounds, `T: 'static`, T can live as long as we want it to live, as long as T is owned and self-sufficient.  
or it has not borrowed other non-static values.  
Ex. `std::thread:spawn`, a new thread may outlive the current thread.  
So we cannot refer anything in new thread that stores its value in old thread. Unless it is going to live throughout the program.

### Ownership

Heap stored variables are non-copy type by default. When the variable goes out of scope, its memory ideally should be deallocated.  
If Box type were Copy, Box2 = Box1, Both Box would assume ownership & will try to free the memory, double-free is a huge problem.

