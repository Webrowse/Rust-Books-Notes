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

## Ownership

Heap stored variables are non-copy type by default. When the variable goes out of scope, its memory ideally should be deallocated.  
If Box type were Copy, Box2 = Box1, Both Box would assume ownership & will try to free the memory, double-free is a huge problem.

## Borrowing and Lifetimes

The primary difference between owning a value and having a mutable reference to it is that the owner is responsible for dropping the value when it is no longer necessary.  
```rust
let x = 42;
let mut y = &x; // y is of type &i32
let z = &mut y; // z is of type &mut &i32
```

if you move the value behind the `&mut`, then you must leave another value in its place, or owner would feel the need to drop the value, and it will find none.

```rust
fn main() {
    let mut s = Box::new(42);
    replace_with_84(&mut s);
}
 fn replace_with_84(s: &mut Box<i32>) {
    println!("before: {}", s); // 42
    let was = std::mem::take(s);
    println!("after: {}", s);  // 0
    println!("was value before: {}", was);  // 42
    *s = was;      // s == was == 42
    let mut r = Box::new(84);
    std::mem::swap(s, &mut r);
    assert_ne!(*r, 84);  // r = 42 after swap, so it passes the unequal test
}
```

### Interior Mutability
- Mutex
- RefCell
- UnsafeCell (forbidden)


## Lifetimes

A *lifetime* is a name of a region of code that some reference must be valid for. While the lifetime will frequently coincide with a scope, it does not have to.

### Lifetimes and the Borrow Checker

when 'a is used, borrow checker checks if 'a is still alive. It verifies through the point of using ref to the point of origin, and if there was no conflict. Similar to High-level Data Flow.

```rust
let mut x = Box::new(42);
let r = &x;                 // 'a starts
if randomFn() > 0.5 {
    *x = 84;                // data muted, now cannot use r here, because r points to the old x.
} else {
    println!("{}", r);      // 'a used here, 
}
```

If borrow checker is not sure, it will simply rejects. That is why we have Unsafe{}.

### Generic Lifetimes

```rust
struct StrSplit<'s, 'p> {
 delimiter: &'p str,
 document: &'s str,
}
impl<'s, 'p> Iterator for StrSplit<'s, 'p> {
 type Item = &'s str;
 fn next(&self) -> Option<Self::Item> {
 todo!()
 }
}
fn str_before(s: &str, c: char) -> Option<&str> {
 StrSplit { document: s, delimiter: &c.to_string() }.next()
}
```

We had to gave different lifetime to both limiter and document, to let borrow checker know we meant document while asking for next().

### Lifetime Variance

to be understood later, along with this code:
```rust
struct MutStr<'a, 'b> {
    s: &'a mut &'b str
}
let mut s = "hello";
*MutStr { s: &mut s }.s = "world";
println!("{}", s);
```
